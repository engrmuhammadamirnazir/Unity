---
name: SQL aggregator views grouping by foreign currency_id MUST sum amount_currency, NOT debit-credit — silent cross-rate double-count
description: When a custom Odoo aggregator (auto=False model with raw SQL view) sums account_move_line.debit-credit but groups/displays by aml.currency_id (foreign currency tag), the displayed "balance in EUR" is actually company-currency-equivalent of EUR-tagged lines. Hidden whenever rate ≈ 1.0 or whenever amount_currency happens to == debit. Surfaces the moment a JE builder explicitly converts foreign→company at a non-trivial rate. Use SUM(CASE WHEN amount_currency!=0 THEN amount_currency ELSE debit-credit END) instead.
type: feedback
updated: 2026-05-02
originSessionId: 2026_05_02i_remittance_v37_aggregator_fix
---

## The rule

When building an Odoo aggregator that GROUPS by `aml.currency_id` (the foreign currency tag) and reports a "balance per currency", **always sum `amount_currency`, not `debit - credit`**:

```sql
-- ❌ WRONG — produces company-currency totals labeled as foreign currency
SELECT
    aml.partner_id,
    aml.currency_id,
    SUM(aml.debit) - SUM(aml.credit) AS balance  -- company currency!
FROM account_move_line aml
GROUP BY aml.partner_id, aml.currency_id

-- ✅ RIGHT — true foreign-currency balance per partner/per currency
SELECT
    aml.partner_id,
    aml.currency_id,
    SUM(CASE
        WHEN aml.amount_currency != 0 THEN aml.amount_currency
        ELSE aml.debit - aml.credit  -- legacy account.payment shortcut
    END) AS balance
FROM account_move_line aml
GROUP BY aml.partner_id, aml.currency_id
```

The COALESCE-style fallback is required because Odoo's `account.payment` posting writes `amount_currency = 0` when the line's currency_id matches the journal's currency (a "no foreign component" shortcut). For those lines, `debit - credit` IS the foreign-currency balance — they happen to also be in company currency, or are tagged with currency_id but stored as same-rate company-currency values.

## Why

Odoo's `account.move.line` model has TWO sets of amount fields:

| Field | Currency | When populated |
|-------|----------|----------------|
| `debit`, `credit` | **Always company currency** | Every line. Sum must balance per move. |
| `amount_currency` | **`currency_id`** (foreign or company) | When line touches a foreign-currency context, OR explicitly set. Signed: positive on debit side, negative on credit side. |
| `currency_id` | n/a | Foreign currency tag. May equal company_currency_id (then amount_currency is the same number as debit-credit). |

The validation contract is: `SUM(debit) == SUM(credit)` PER MOVE, BOTH IN COMPANY CURRENCY. There's no per-currency balance check at the move level — that's a higher-level concept the application/aggregator builds.

When you construct a SQL view that groups by `currency_id`, you're saying "show me the balance in this foreign currency". But if you sum `debit - credit`, you're summing company-currency values — a 10,000 EUR line on a USD-company ledger would have `debit = 10,000 × spot_eur_to_usd ≈ 11,765 USD`. Sum that with other EUR-tagged lines and you get a USD-equivalent total falsely labeled "EUR balance".

This is invisible whenever:
- Company currency == one of the foreign currencies in play (e.g. company is USD, customer ledger is in USD only)
- Spot rate happens to be 1.0 (rare in production)
- Line was posted at rate 1.0 by old code that set `debit = amount` literally (the v9-era bug pattern from feedback_je_foreign_amount_as_company_debit_credit.md)

It surfaces the moment any JE explicitly converts foreign→company at a non-trivial rate (e.g. EUR→USD @ 1.18). Then the discrepancy = `(company_value − foreign_value)` per converted line shows up in the displayed balance.

## Real incident — 2026-05-02 Remittance v10.0.37

`remittance.partner.balance` and `remittance.balance.line` (both `_auto = False` SQL views) had been built since v10.0 with `SUM(debit) - SUM(credit)` grouped by `currency_id`. The bug was hidden because:

1. The v9-era `_je_bank_eur_out_usd_in` builder wrote `debit = self.amount` literally (10,000 EUR posted as 10,000 USD on Daleel's USD-company ledger). amount_currency happened to also be 10,000. So `SUM(debit) - SUM(credit) ≈ SUM(amount_currency)` for those lines — the bug was masked.

2. v10.0.36 fixed `_je_bank_eur_out_usd_in` to correctly compute `eur_company = eur_amt × spot_eur_to_usd ≈ 11,764.71` while keeping `amount_currency = 10,000`. **The aggregator immediately started showing the wrong EUR balance** — Amir's 10,000 EUR conversion drained 11,764.71 from his "EUR balance" (in fact: drained 10,000 EUR which was equivalent to 11,764.71 USD-on-the-GL).

User report: "Amir balance should have reduced 10k but it reduced more — remaining EUR should have been 15k from initial 25k."

Diagnosis: aggregator math reconciled to the cent — `SUM(amount_currency for non-zero) = -10,000 EUR` but `SUM(debit-credit)` on the same EUR-tagged lines = `-11,764.71 USD-as-company`. Sign-flipped client POV showed +13,235.29 EUR instead of +15,000 EUR. Discrepancy = 1,764.71 = exactly `(spot 1.176 − operator 1.18) × 10,000` cross-multiplied.

**Fix:** swap to `SUM(CASE WHEN amount_currency != 0 THEN amount_currency ELSE debit-credit END)` in both views. Post-deploy on remtest verified Amir EUR = +15,000 ✓.

## Detection signals

- Aggregator-displayed balance differs from a manual GL trial-balance by a fixed proportional amount per cross-currency line
- Customer/operator complains "balance reduced by more than the transaction amount"
- Affected only after a JE builder change that introduced explicit company-currency conversion (`_convert()` calls)
- View uses `_auto = False` + raw `cr.execute("CREATE OR REPLACE VIEW ...")`
- Pattern `SUM(aml.debit) - SUM(aml.credit)` appears in a SQL view that ALSO groups by `aml.currency_id` (or includes `currency_id` in the SELECT)

## How to apply

1. **Search every `_auto = False` model for the antipattern.** `grep -rn "SUM(aml.debit) - SUM(aml.credit)" path/to/module/models/` — for any hit that ALSO has `currency_id` in the GROUP BY or SELECT, fix it.

2. **Replace with the COALESCE form:**
   ```sql
   SUM(CASE
       WHEN aml.amount_currency != 0 THEN aml.amount_currency
       ELSE aml.debit - aml.credit
   END) AS balance
   ```

3. **Counts (debit/credit columns shown for transparency in list views) need similar treatment** if displayed alongside the balance:
   ```sql
   SUM(CASE WHEN aml.amount_currency != 0
            THEN GREATEST(aml.amount_currency, 0)
            ELSE aml.debit END) AS total_debit,
   SUM(CASE WHEN aml.amount_currency != 0
            THEN GREATEST(-aml.amount_currency, 0)
            ELSE aml.credit END) AS total_credit
   ```

4. **Test the fix with cross-currency rates ≠ 1.0.** Any rate where `amount_currency != debit` exposes the bug.

5. **Migration:** SQL views auto-rebuild on `--update <module>`. No data fix needed.

## Reusable across

Any Odoo workspace where custom code builds aggregator views over `account_move_line` and reports per-currency balances:
- Multi-currency partner ledgers (any AP/AR module)
- Treasury / cash-management dashboards
- Customer-statement-style POV views (with sign-flip)
- Remittance / FX dealer / MTO modules
- Multi-currency consolidation tools
- Any "balance per partner per currency" KPI

Especially after: a JE builder change that introduced explicit `_convert()` for company-currency debit/credit.

## Related memories

- [feedback_je_foreign_amount_as_company_debit_credit.md](feedback_je_foreign_amount_as_company_debit_credit.md) — the JE-builder side of the same family of bugs (Odoo doesn't auto-convert when both `amount_currency` AND `debit/credit` are passed explicitly). The aggregator-axis bug above is the read-side counterpart of that write-side gotcha.
- [feedback_module_pump_static_validation_insufficient.md] — `py_compile` + `ET.parse` doesn't catch SQL-view bugs; only running with cross-currency data exposes them.
