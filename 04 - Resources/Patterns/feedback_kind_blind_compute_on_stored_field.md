---
name: Stored computes that aggregate via FK reverse-collections MUST filter by `kind` / `state`
description: A `store=True` compute that says `amount - sum(reverse_collection.amount)` will return `amount` (full) on rows for which the reverse collection is ALWAYS empty. List views with that field as a column will sum the full amount across rows the field doesn't semantically apply to, producing phantom totals (e.g. $13,126 of "Open exposure" on 4 Closed transactions). When a model holds multiple flavours (kinds, states, sides of a relationship), every stored aggregate needs to either filter by which flavour it applies to, or be explicitly zeroed for the flavours it doesn't.
type: feedback
updated: 2026-05-03
originSessionId: 2026_05_03b_remittance_v10_0_39_demo_prep
---

## The rule

**For every `store=True` computed field that aggregates via FK reverse-collections (`sum(reverse_ids.mapped('field'))`), check whether the formula is meaningful for ALL `kind` / `state` values, or only a subset.** If subset-only, EXPLICITLY zero the field for the other subset — don't let the formula's "no rows in the reverse collection" case silently produce a misleading non-zero stored value.

## The pattern that fails

```python
# WRONG — applies to all kinds, but the formula only makes sense for inbound deposits
@api.depends('amount', 'settlement_inbound_ids.amount')
def _compute_open_amount(self):
    for rec in self:
        settled = sum(rec.settlement_inbound_ids.mapped('amount'))
        rec.open_amount = max(rec.amount - settled, 0.0)
```

For an OUTBOUND record, `settlement_inbound_ids` is always empty (outbounds are the OUTBOUND side of the relationship, not the INBOUND side). So:
- `settled = 0`
- `open_amount = amount - 0 = amount`

The stored value on closed outbounds is the full amount, suggesting they have open exposure when they're already settled. List views that show `open_amount` as a column AND aggregate the column total (Odoo standard sum aggregator on Monetary) accumulate phantom exposure.

## The fix

```python
# RIGHT — kind-aware
OUTBOUND_KINDS = (
    'cash_eur_out', 'cash_aed_out', 'cash_aed_expense',
    'bank_eur_out_usd_in', 'bank_usd_out',
)

@api.depends('amount', 'kind', 'settlement_inbound_ids.amount')
def _compute_open_amount(self):
    for rec in self:
        if rec.kind in OUTBOUND_KINDS:
            # Field doesn't apply to outbounds — explicitly zero
            rec.open_amount = 0.0
        else:
            settled = sum(rec.settlement_inbound_ids.mapped('amount'))
            rec.open_amount = max(rec.amount - settled, 0.0)
```

Note: `kind` MUST be added to `@api.depends` so the value recomputes if the kind ever changes (it shouldn't, but discipline).

Also need: a one-time SQL backfill in the version migration to fix existing stored rows:

```sql
UPDATE remittance_transaction
   SET open_amount = 0.0
 WHERE kind IN ('cash_eur_out', 'cash_aed_out', 'cash_aed_expense',
                'bank_eur_out_usd_in', 'bank_usd_out')
   AND COALESCE(open_amount, 0) <> 0;
```

(The compute change won't fire on existing rows unless they're modified; the SQL UPDATE retrofits them so the dashboard / list-view total recomputes correctly without waiting for a write.)

## Symmetric mirror — same model, opposite direction

`_compute_settlement_amounts` had a symmetric mirror bug. `settlement_unsettled = amount - sum(settlement_outbound_ids)` is the OUTBOUND-side metric — but on INBOUND records, `settlement_outbound_ids` is always empty (inbounds have no outbound-side settlements pointing at them), so `settlement_unsettled = amount` on every inbound. Same fix pattern: zero on inbounds.

When you find one direction of this bug on a model with two-sided relationships, **always check the symmetric direction**.

## Why it surfaces visually

Odoo list views with stored Monetary columns auto-sum the column at the bottom. The `<list>` view's `<field>` doesn't need any explicit aggregator — the framework adds one. So phantom non-zero stored values surface as totals on list footers, kanban summary panels, and dashboard KPIs that read those fields directly without re-filtering.

## Real incident — 2026-05-03 Suleman Remittance v10.0.39

User screenshot of Transactions list:
- 2 outbounds (Cash EUR Out 10k, Cash AED Out 5k), all stage=closed → Open column showed full amounts (10k, 5k)
- 2 inbounds (Cash EUR In 10k, Cash AED In 5k), all stage=closed → Open column correctly showed 0
- List footer: "Total Open: $13,126.18" (= 10k EUR + 5k AED converted to USD via Daleel's company currency)

User's reaction: "check issues in total" — the phantom $13,126.18 looked like real open exposure on a fully-closed book.

Same bug had a symmetric `settlement_unsettled` mirror that wasn't visible in this screenshot but would have appeared if the user added that column to the list view.

## Detection signals

- List view footer shows non-zero total for a field where every individual row's stage is "closed" / "cancelled" / "done"
- KPI dashboards show "open exposure" or "outstanding" amounts that don't match operations' mental model
- After a wipe of all transactions of a particular kind, that kind's previously-aggregated total stays at the pre-wipe value (because the stored values on OTHER kinds were also non-zero)
- Recompute via Odoo's `--update` doesn't help (the formula still produces the wrong answer)

## How to apply

1. **For every `store=True` compute that uses `mapped()` over a reverse-FK collection**: ask "is this formula meaningful for every value of every selection field on this model?"
2. **If not, branch on the selection field** and explicitly zero the irrelevant subset.
3. **Add the selection field to `@api.depends`** so the value recomputes if the kind ever changes.
4. **In the version migration, run an SQL UPDATE** to retrofit existing stored rows.
5. **Check for symmetric mirror bugs** — same model, opposite direction of the relationship.

## Reusable across

Any Odoo model with:
- Multiple `kind` / `state` / `direction` values where some computes only apply to a subset
- Reverse FK collections that exist on only one side of a two-sided relationship
- Two-sided settlement / matching / clearing relationships (this remittance pattern, but also: stock.move (incoming/outgoing), account.move (debit/credit), sale.order vs purchase.order link tables, mrp work-orders linked to manufacturing orders, project.task parent/subtask, etc.)

## Related memories

- [feedback_aggregator_currency_axis_bug.md](feedback_aggregator_currency_axis_bug.md) — sister rule on SQL aggregator views (same family: pick the right formula axis)
- [feedback_je_foreign_amount_as_company_debit_credit.md](feedback_je_foreign_amount_as_company_debit_credit.md) — same family: write-side mirror of read-side aggregator bugs
