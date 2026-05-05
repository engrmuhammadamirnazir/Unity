---
name: feedback_odoo19_sale_mrp_smart_button_via_stock_reference
description: Odoo 19 Manufacturing smart button on SO walks sale.stock_reference_ids.production_ids — NOT the move_orig_ids chain. Manually-created MOs miss this link silently.
type: feedback
originSessionId: 89607173-80b4-4933-8021-0547884a31b7
---
## Rule

In Odoo 19, the Manufacturing smart button on `sale.order` is computed by `sale_mrp.SaleOrder._compute_mrp_production_ids` via:

```python
@api.depends('stock_reference_ids.production_ids')
def _compute_mrp_production_ids(self):
    for sale in self:
        mos = sale.stock_reference_ids.production_ids
        sale.mrp_production_ids = mos.filtered(
            lambda mo: not mo.production_group_id.parent_ids and mo.state != 'cancel'
        )
        sale.mrp_production_count = len(sale.mrp_production_ids)
```

This walks `sale.order → stock_reference_ids → stock.reference → production_ids → mrp.production`. NOT through the `order_line.move_ids.move_orig_ids.production_id` chain you'd expect from earlier versions.

`stock.reference` is a v19 model (file: `addons/stock/models/stock_reference.py`, extended in `addons/sale_stock/models/stock_reference.py` for `sale_ids` and `addons/mrp/models/stock_reference.py` for `production_ids`). Each procurement run creates one `stock.reference` row that bundles all moves + productions + sales for a single procurement chain. The procurement engine wires up `stock_reference_sale_rel` (sale ↔ reference) AND `stock_reference_production_rel` (production ↔ reference) automatically.

**Why:** When an MO is created via the standard procurement engine (`_action_launch_stock_rule()` on a sale.order.line with route=Manufacture), Odoo creates the `stock.reference` and links everything correctly. When an MO is created MANUALLY (`env['mrp.production'].create({...}).action_confirm()` with just `origin="S01622"` and `sale_line_id=...`), Odoo creates a DIFFERENT `stock.reference` named after the MO itself, with the MO in `production_ids` but no sale_id link. The SO continues to point to its own (production-less) reference. Smart button shows 0.

This bit Obliq twice — 2026-05-01 (initial fix script created MO manually) and again on 2026-05-05 (smart button still missing because the link was never wired). Concrete data: SO S01622 had `stock_reference_ids = [3512]` with production_ids=[]; MO STO/MO/03407 had its own ref 3513 with production_ids=[4673, 4674] but `sale_ids=[]`. Adding ref 3513 to SO via `order.write({"stock_reference_ids": [(4, 3513)]})` made the smart button appear.

**How to apply:**

1. **Prefer procurement-engine MO creation** over manual `mrp.production.create()`. Use:
   ```python
   so.order_line.filtered(...)._action_launch_stock_rule()
   ```
   Even on locked/done SOs you can run this on individual lines (it creates the procurement chain end-to-end).

2. **If you MUST create MO manually** (e.g., the SO is locked + procurement no longer triggers + you need a paper-trail MO for an existing delivery), wire the stock.reference yourself:
   ```python
   mo = env['mrp.production'].create({...})
   mo.action_confirm()
   # mo gets its own stock.reference auto-created — find it
   mo_ref = env['stock.reference'].search([('production_ids', 'in', mo.ids)], limit=1)
   # Link it to the SO so the smart button compute sees it
   so.write({"stock_reference_ids": [(4, mo_ref.id)]})
   ```

3. **Diagnostic for "manufacturing button missing" on SO**: probe `sale.stock_reference_ids.production_ids`. If empty AND an MO exists with origin=SO.name, the reference is unlinked. Don't waste time on `order_line.move_ids.move_orig_ids.production_id` — that path is irrelevant in v19.

4. **The `move_dest_ids` link between MO finished move and SO outgoing move is a SEPARATE concern** (procurement chain integrity). Adding it does NOT fix the smart button by itself. We added that link too on 2026-05-05 (insert into `stock_move_move_rel`) for chain integrity, but `mrp_production_count` only flipped to 1 after the `stock_reference_ids` write.

5. **Sub-MO filter:** the compute filters out sub-MOs via `not mo.production_group_id.parent_ids`. So when a multi-level BoM produces parent + child MOs, only the parent shows in the smart button. Don't manually filter — the framework already does.

## Sister rule (Obliq-specific but generally applicable)

When fixing Home Centre / VinSupplier "SKU NOT FOUND" by setting `default_code` on an active variant of a multi-attribute template, ALSO check the sibling templates / variant axes — Obliq's "Studio Obliq Lili Coffee Table - Veneer / Oak" (tmpl 4159, variants 4706 Black + 4707 Ivory) had the same empty-default_code pattern as the "/ Walnut" tmpl 4157 fixed on 2026-05-01. Whenever an active variant of a Studio Obliq template has the brand + base name but variants are color/material attribute-driven and active variants have no `default_code`, expect Home Centre import to fail for that template's SKUs. Probe via:
```sql
SELECT pp.id, pp.default_code, pt.id, pt.name->>'en_US'
FROM product_product pp JOIN product_template pt ON pp.product_tmpl_id = pt.id
WHERE pp.active AND pt.active AND pp.default_code IS NULL
ORDER BY pt.id;
```
Then cross-reference against `mrp_bom.code` to find which SKUs belong on which variants.
