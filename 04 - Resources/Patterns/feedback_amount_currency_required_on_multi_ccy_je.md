---
name: Multi-currency JEs MUST set `amount_currency` AND `currency_id` — `debit`/`credit` are ALWAYS company currency
description: When you write a journal entry programmatically with `currency_id = foreign_ccy` but forget to populate `amount_currency`, Odoo treats your `debit`/`credit` numerical values as company-currency amounts (NOT foreign). For a USD-company processing EUR, writing `debit = 15000` with `currency_id = EUR` means "15,000 USD," not "15,000 EUR converted to USD." This breaks reconciliation across multi-currency moves (residuals stuck) and bypasses Odoo's automatic IAS 21 FX gain/loss machinery (no exchange move auto-created on reconcile).
type: feedback
updated: 2026-05-03
originSessionId: 2026_05_03c_remittance_v10_0_40_ias21_fx
---

## The rule

**Every line in a multi-currency journal entry must set BOTH `currency_id` AND `amount_currency`.** `debit` and `credit` are ALWAYS in company currency. The foreign-currency leg lives in `amount_currency` (signed: positive on debit-side, negative on credit-side).

## The pattern that fails

```python
# WRONG — treats foreign numerical value as company-currency amount
move = self.env['account.move'].create({
    'line_ids': [
        (0, 0, {
            'account_id': bank.id,
            'debit': 15000,                  # interpreted as 15,000 USD!
            'credit': 0.0,
            'currency_id': eur.id,           # EUR currency tag is set...
            # 'amount_currency': MISSING     # ...but the foreign leg is unset
        }),
        # ...
    ],
})
```

For a USD-functional company processing 15,000 EUR:
- Odoo sees: `debit = 15,000 USD` (company currency, the only authoritative ledger amount)
- Odoo also sees: `currency_id = EUR` and `amount_currency = 0` (foreign leg empty)
- Reconciliation against an Odoo-posted out_invoice's AR (which DID set `amount_currency = +15,000 EUR` and `debit = 17,647.06 USD` at spot rate 1.176) leaves a 2,647.06 USD residual
- `payment_state = 'partial'`, `amount_residual` non-zero, FX loss/gain NOT auto-recognised

## The fix

```python
# RIGHT — both legs explicitly set
foreign_ccy = self.currency_id
company_ccy = self.company_id.currency_id
is_foreign = bool(foreign_ccy) and foreign_ccy != company_ccy
date_ref = ...  # today, settlement date, etc.

if is_foreign:
    debit_company = foreign_ccy._convert(
        foreign_amount, company_ccy, self.company_id, date_ref)
else:
    debit_company = foreign_amount

amount_currency_signed = foreign_amount if is_foreign else 0.0
# Sign convention: + on debit-side, - on credit-side, 0 on same-currency moves

move = self.env['account.move'].create({
    'line_ids': [
        (0, 0, {
            'account_id': bank.id,
            'debit': debit_company,                # company-currency conversion
            'credit': 0.0,
            'currency_id': foreign_ccy.id,
            'amount_currency': amount_currency_signed,   # signed foreign-currency value
        }),
        # corresponding credit line
        (0, 0, {
            'account_id': ar.id,
            'debit': 0.0,
            'credit': debit_company,
            'currency_id': foreign_ccy.id,
            'amount_currency': -amount_currency_signed,
        }),
    ],
})
```

## Why this matters — Odoo's reconcile() and IAS 21

Odoo's `account_move_line.reconcile()` is where multi-currency magic happens. When two AMLs are reconciled:
1. Odoo compares their **company-currency** amounts (sum of debits minus sum of credits)
2. If they DON'T balance in company currency BUT they DO balance in foreign currency, Odoo creates an `account_partial_reconcile.exchange_move_id` — a separate journal entry that books the difference to:
   - `company.income_currency_exchange_account_id` (gain) or
   - `company.expense_currency_exchange_account_id` (loss)
3. This is exactly what **IAS 21 §28** requires: "Exchange differences arising on the settlement of monetary items at rates different from those at which they were translated on initial recognition shall be recognised in profit or loss in the period in which they arise."

If `amount_currency` is missing on either side, Odoo can't see the foreign-currency match and won't generate the exchange move. The residual just sits there.

## Detection signals

- `payment_state = 'partial'` on an invoice that should be fully paid
- `amount_residual` non-zero after reconciliation that "should have cleared" the AR
- Bank account balance shows a negative residual when net economic activity should be zero
- `account_move_line` rows with `currency_id` set but `amount_currency = 0` on a non-same-currency line — code smell
- Company-currency dashboards show drift between booked AR and cleared AR even though invoices show as paid

## Real incident — 2026-05-03 Suleman Remittance v10.0.40

`_create_odoo_payment` at `models/remittance_invoice.py:506` (v10.0.27 origin) was writing 4 lines with this shape:
```python
'debit': self.total,           # = 15,000 (EUR amount as a Python number)
'currency_id': self.currency_id.id,    # EUR
# 'amount_currency': not set
```

Symptoms during 2026-05-02 / 2026-05-03 demo prep:
- TXN/2026/00027 (15,000 EUR Cash EUR In) ended at `state = 'closed'` but linked invoice INV/2026/00003 stuck at `payment_state = 'partial'` with `amount_residual = 2,647.06`
- EUR Bank dashboard showed -2,700 phantom (rounding-related visible artifact of same gap)

Fix in v10.0.40: rewrote the builder per the pattern above — `currency._convert()` for company amounts, `amount_currency` populated. `reconcile()` now auto-creates the FX exchange move; `payment_state` reaches `'paid'`, IAS 21 compliance restored.

## How to apply

1. **Audit any custom JE builder** in your codebase. Grep for `'currency_id'` in `account.move` `line_ids` constructions and check whether `'amount_currency'` is set on the same dict.
2. **For new builders:** always pair `currency_id` and `amount_currency`. Never write a foreign-currency `debit`/`credit` value without conversion.
3. **For balance-shift JEs (no real currency conversion):** if both sides are foreign and you want the company-currency amounts to match exactly (no FX line), use the same `debit_company` value across both sides and let `amount_currency` carry the foreign leg.
4. **For mixed-currency JEs (e.g., bank EUR receipt clearing a same-currency AR):** use today's spot for cash legs, original-rate for clearing legs IF you want to avoid the auto-exchange move (NOT recommended for IAS 21 compliance — the spread should hit P&L).
5. **For exchange differences:** trust `reconcile()` to do the right thing. Don't manually plug an FX line — Odoo's exchange move is more robust (it tracks reconcile_id, captures the exact rate-shift, and is auditable).

## Reusable across

Every Odoo module that programmatically constructs multi-currency journal entries:
- Bank statement importers
- Custom payment / receipt wizards
- Inter-company transfers
- Cross-currency settlement / clearing flows (remittance, FX trading, hawala/MTO)
- Manufacturing landed cost in foreign currency
- Vendor bill auto-validation flows

## Related memories

- [feedback_je_foreign_amount_as_company_debit_credit.md](feedback_je_foreign_amount_as_company_debit_credit.md) — write-side counterpart family
- [feedback_aggregator_currency_axis_bug.md](feedback_aggregator_currency_axis_bug.md) — read-side currency-axis family
- [feedback_kind_blind_compute_on_stored_field.md](feedback_kind_blind_compute_on_stored_field.md) — same family: explicit handling at boundaries
- [reference_account_move_reversal_pattern.md](reference_account_move_reversal_pattern.md) — Odoo reversal mechanics
