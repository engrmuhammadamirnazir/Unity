---
name: Oenoteca LC on aged picking + zero-on-hand SKUs silently skips auto-JE
description: When validating a stock.landed.cost on an aged picking whose moves have mostly been sold to zero, Odoo v19 sets account_move_id=False on the LC even though state=done — the freight/tariff value never hits 110100 GL. Always check account_move_id post-validate and post a manual STJ JE if missing.
type: feedback
originSessionId: aad6d6491c57c4444
---

# Oenoteca v19 — Landed Cost auto-JE silently skipped on aged-picking + zero-on-hand SKUs

**Rule:** After every `stock.landed.cost.button_validate()` on Oenoteca prod, **always read `account_move_id` and verify it is non-False** before declaring the LC complete. If `state='done'` but `account_move_id=False`, post a manual STJ JE to backfill the missing GL entry (DR 110100 Inventory $X / CR 500002 Freight-in $X for freight; DR 110100 / CR 500001 Tariffs/Duties $X for tariff/duty).

**Why:** Odoo v19's stock_account auto-JE pipeline is already known-broken on Oenoteca for pure inventory adjustments (see [feedback_oenoteca_stock_account_broken.md](feedback_oenoteca_stock_account_broken.md) — `stock.move.account_move_id = False` on every confirmed receipt + every inv-adj since Feb 2026). On 2026-05-04 we discovered a SECOND failure mode in the same family: when a Landed Cost is created against a `stock.picking` whose receipt happened weeks/months earlier AND most of the underlying SKUs have already been sold to zero (qty_available=0 on the products), the LC's `compute_landed_cost()` + `button_validate()` flow appears to succeed (state lands at `done`, cost lines compute correctly, `stock.valuation.adjustment.lines` get written), but **`account_move_id` is silently False**. No GL entry is ever created. The freight/tariff value is "lost" to GL even though the cost-line computation is intact.

This was caught on Enohance INV 92/2026 freight LC (LC/2026/0032, $608.15, applied to picking 502 = WH/IN/00083 from 2026-03-19; picking had 22 moves but only 2 SKUs still on hand at LC time). It is NOT caught on fresh receipts (BWG 588462 the same day worked clean — picking 691 was validated 2026-05-04, LC validated 2026-05-04, all 3 SKUs still on hand at qty=1, auto-JE fired correctly).

The trigger condition is some interaction of:
- Picking date significantly older than LC date
- Most/all SKUs on the picking have qty_available=0 by LC time
- Possibly: AVCO recompute pathway determines no live cost layer to adjust → bails before creating the JE → leaves `account_move_id=False`

We have NOT root-caused which trigger applies — only the symptom and recovery.

**How to apply:**

1. After `lc.button_validate()` returns success and `lc.state == 'done'`, ALWAYS read back:
```python
lc = env['stock.landed.cost'].browse(lc_id).read(['state', 'account_move_id', 'cost_lines_ids'])
assert lc[0]['state'] == 'done'
if not lc[0]['account_move_id']:
    # AUTO-JE SKIPPED — backfill manually
    ...
```

2. If `account_move_id=False`: build a manual STJ JE replicating the GL impact the auto-JE would have created. Aggregate `stock.valuation.adjustment.lines` by `cost_line_id.account_id` (the freight/tariff CR account) and per-product `stock.move.account_id` (the inventory DR account, almost always 53 = 110100) so totals match what `compute_landed_cost()` allocated. Pattern:

```python
move_vals = {
    'journal_id': 15,                  # STJ Inventory Valuation
    'date': fields.Date.today(),       # match LC date
    'ref': f"LC/{lc.name}-manual-je-backfill",
    'line_ids': [
        (0, 0, {'account_id': 53,  'debit':  total_uplift, 'credit': 0,            'name': f"LC {lc.name} freight uplift"}),
        (0, 0, {'account_id': 160, 'debit': 0,             'credit': total_uplift, 'name': f"LC {lc.name} freight clear-down"}),
    ],
}
mv = env['account.move'].create(move_vals)
mv.action_post()
```

3. **Standard_price on the products is also NOT auto-recomputed** when this gotcha hits — the AVCO update pathway is part of the same skipped flow. After backfilling the manual JE, separately decide whether to:
   - **Apply the std_price update** (`product.product.write({'standard_price': new_value})`) so future sales of the remaining stock book correct COGS — RECOMMENDED when there ARE still bottles on hand from this LC.
   - **Leave std_price alone** when on_hand=0 across all affected SKUs — no live stock to AVCO-adjust.

   Niccolò's call which path; flag it explicitly in the report rather than silently writing.

4. **Detection side-channel** — after a session that includes any LC, query for orphan-LC pattern across history to catch any prior occurrences:
```python
env['stock.landed.cost'].search_read(
    [('state', '=', 'done'), ('account_move_id', '=', False)],
    ['id', 'name', 'date', 'amount_total', 'picking_ids']
)
```
If non-empty, you have a backlog of "valid but ungposted" LCs. Each needs the same manual JE backfill.

**Reference:** Session 2026-05-04d Enohance INV 92/2026 freight LC. Recovered with `STJ/2026/05/0002` + path-flagged std_price for Sordo Barolo 2021 (pid 1223) + Falchetto Lurei 2023 (pid 1669).

**Sister patterns:**
- [feedback_oenoteca_stock_account_broken.md](feedback_oenoteca_stock_account_broken.md) — same family, different trigger (inv-adj instead of LC).
- [feedback_amount_currency_required_on_multi_ccy_je.md](feedback_amount_currency_required_on_multi_ccy_je.md) — multi-currency JE construction rule that applies when the freight bill is in EUR but Oenoteca is USD-functional.

**Long-term fix:** investigate why `stock.landed.cost._action_post()` returns early without creating the move when AVCO has no live layer. Likely the issue is in `stock_landed_costs` calling `_create_account_move` only if the SVL update succeeded, and the SVL update bails because all relevant `stock.valuation.layer` rows are already settled. Until fixed, the manual-backfill workaround is the canonical recovery path on Oenoteca v19.
