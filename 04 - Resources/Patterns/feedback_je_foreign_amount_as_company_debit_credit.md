---
name: Odoo does NOT auto-convert amount_currency to debit/credit when both are passed explicitly — JEs unbalance silently
description: When building account.move.line dicts in code, if you set BOTH `amount_currency` (foreign value) AND `debit`/`credit` (company value), Odoo USES BOTH AS GIVEN — no conversion. Naive code that sets `debit=amount_currency_value` produces unbalanced moves. Always compute `debit/credit = currency._convert(amount_currency, company_currency, ...)` explicitly.
type: feedback
updated: 2026-05-02
originSessionId: 2026_05_02h_remittance_v35_je_balance_fix
---

## The rule

When constructing `account.move.line` records in code with foreign-currency amounts, **always compute the company-currency `debit`/`credit` explicitly via `_convert()` or your operator-quoted rate**:

```python
# ❌ WRONG — Odoo will store the EUR amount AS the company-USD amount
{'account_id': X, 'debit': 10000, 'credit': 0,
 'currency_id': eur.id, 'amount_currency': 10000}  # implies 10000 EUR = 10000 USD?!

# ✅ RIGHT — debit/credit explicitly in company currency
eur_in_company = eur_amt * exchange_rate  # OR eur._convert(eur_amt, company, company_id, date)
{'account_id': X, 'debit': eur_in_company, 'credit': 0,
 'currency_id': eur.id, 'amount_currency': eur_amt}
```

## Why

Odoo's `account.move` validation requires `sum(debit) == sum(credit)` per move, with both values in **company currency**. The `amount_currency` field tracks the foreign-currency value separately for reconciliation and display — it does NOT replace the company-currency calculation.

When you pass an explicit value for `debit`/`credit`, Odoo trusts you. There's no "auto-convert from amount_currency" fallback. If you give it 10,000 USD on the debit side and 10,000 USD on the credit side using different `amount_currency` values, Odoo is happy — the move "balances" at 10,000 USD even though the foreign-currency math is wrong.

The reverse case (where you set ONLY `amount_currency` and leave `debit/credit` as 0) DOES auto-convert, because Odoo knows it has to. But the moment you pass any non-zero value for `debit`/`credit`, you've taken responsibility.

This is by design (Odoo lets accountants override the auto-conversion for known FX deltas), but it's a recurring trap for code that builds JEs.

## Real incident — 2026-05-02 Remittance v10.0.35

`bank_eur_out_usd_in` JE builder had been broken since v9 with this naive code:

```python
eur_amt = self.amount        # 10000 EUR
usd_amt = self.converted_amount  # 11800 USD
lines = [
    {'name': '...', 'account_id': payer_ar.id, 'partner_id': sender.id,
     'debit': eur_amt, 'credit': 0.0,                # ← 10000 (wrong: in company-USD this should be ~11800)
     'currency_id': eur_id, 'amount_currency': eur_amt},
    {'name': '...', 'account_id': bank_eur.id,
     'debit': 0.0, 'credit': eur_amt,                # ← 10000 (same wrongness, both wrong cancel)
     'currency_id': eur_id, 'amount_currency': -eur_amt},
    {'name': '...', 'account_id': funds_usd.id,
     'debit': usd_amt, 'credit': 0.0,                # ← 11800 (correct since USD == company)
     'currency_id': usd_id, 'amount_currency': usd_amt},
    {'name': '...', 'account_id': fx_clearing.id,
     'debit': 0.0, 'credit': usd_amt,                # ← 11800
     'currency_id': usd_id, 'amount_currency': -usd_amt},
    {'name': '...', 'account_id': fx_clearing.id,
     'debit': eur_amt, 'credit': 0.0,                # ← 10000 (literal, not 11760 spot or 11800 rate)
     'currency_id': eur_id, 'amount_currency': eur_amt},
]
# sum(debit) = 10000+11800+10000 = 31800
# sum(credit) = 10000+11800       = 21800
# IMBALANCE = 10000  →  Odoo refuses to post: "The entry is not balanced."
```

Two compounded bugs: (1) literal foreign amounts as company values, (2) the 5th line had no balancing CR. Both were silent for years because the kind was rarely used.

**Fix:** explicitly compute company-currency values per line. Use the operator-quoted rate when the JE is meant to balance at that rate, fall back to Odoo spot otherwise:

```python
# Use operator-quoted rate for EUR-to-company conversion to keep partner AR clean
if eur_ccy == company_ccy:
    eur_company = eur_amt
elif usd_ccy == company_ccy:
    eur_company = eur_amt * self.exchange_rate  # operator's USD-per-EUR
else:
    eur_company = eur_ccy._convert(eur_amt, company_ccy, self.company_id, date)

# USD line (target_currency_id == USD == company_ccy here for Daleel)
usd_company = usd_amt if usd_ccy == company_ccy else \
              usd_ccy._convert(usd_amt, company_ccy, self.company_id, date)
```

Plus: any imbalance left over (operator rate vs Odoo spot delta) gets a final `± FX Gain/Loss` plug line to ensure `sum(debit) == sum(credit)` exactly.

## Detection signal

- `Validation Error: The entry is not balanced` on a JE where the amounts "look right"
- JE that posts cleanly when `currency_id` matches `company_currency_id` but breaks on cross-currency
- Logs show `account.move._check_balanced` raising
- Manual JE builder using `account.move.line` create with explicit `debit`/`credit` values

## How to apply

1. **Always pass `debit`/`credit` in company currency.** Compute via `_convert()` or your business rate, never use the foreign amount literally.
2. **Add a balance check + FX plug at the end of any multi-line builder.** `sum(debit) - sum(credit)` should be 0; if not, append a final line on FX Gain/Loss account with the delta.
3. **Test with company_currency != one of the foreign legs.** Most builder bugs hide when company currency happens to match a foreign leg (e.g., Daleel's USD == bank_eur_out_usd_in's USD target).
4. **Defensive: assert post-construction.** Before calling `_post_move`:
   ```python
   sum_dr = sum(l.get('debit', 0) for l in lines)
   sum_cr = sum(l.get('credit', 0) for l in lines)
   assert abs(sum_dr - sum_cr) < 0.005, f'JE unbalanced: DR={sum_dr} CR={sum_cr}'
   ```

## Reusable across

Any Odoo workspace where code builds `account.move.line` records directly:
- Custom payment posting (POS, e-commerce gateways, FX dealers, MTOs)
- Inventory valuation adjustments with foreign-currency stock
- Multi-currency invoicing with operator-quoted rates
- Reconciliation correction scripts
- Manual JE builders in custom wizards
- Migration scripts that backfill historical JEs

Especially: any module that uses `_pick_journal('bank', non_company_currency)` and constructs lines in that journal's currency.
