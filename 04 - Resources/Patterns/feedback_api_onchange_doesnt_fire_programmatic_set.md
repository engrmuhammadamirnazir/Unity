---
name: @api.onchange does not fire when fields are set programmatically — wizards must always have an action-method fallback
description: Odoo's @api.onchange only triggers from UI events (typing, dropdown pick, date entry). When a TransientModel field is set via default_get / context defaults / programmatic create / barcode scanner / fast-Apply-before-debounce, the onchange never runs and any state it would have populated stays empty. Action methods that depend on onchange-populated state then fail with confusing generic errors.
type: feedback
updated: 2026-05-02
originSessionId: 2026_05_02h_remittance_v32_to_36
---

## The rule

`@api.onchange` is a **UI-only callback**. It fires when the field changes in the form view as the user is typing/picking/etc. It does NOT fire in any of these cases:

- Field set via `context={'default_<field>': value}` on action open
- Field set programmatically via `Model.create({...})` then read back
- Field set via barcode scanner / Tab-and-Apply rapid input that bypasses the field's debounce
- Field set via URL query param

Any wizard action method (`def action_apply` / `action_release` / etc.) that depends on the onchange having populated derived state will fail when the field is set by any of those non-UI paths.

## Why

In Odoo's web client, onchange callbacks are dispatched by the form renderer when it detects a value change in a focused field. The dispatch happens client-side then sends a record-snapshot to the server's `onchange()` RPC. Programmatic field-setting paths do NOT go through the form renderer, so the onchange dispatch never fires.

This is by design (onchange is a presentation-layer concern), but it's a recurring trap for wizards.

## Real incident — 2026-05-02 Remittance v10.0.32 pickup wizard

User reported `Invalid Operation: No deposit selected. Type the pickup code first.` on the pickup-by-code wizard at `wizard/remittance_pickup_wizard.py:action_release` even though they had typed a valid MTCN.

The wizard had this design:
- `pickup_code = fields.Char(...)` — operator types here
- `inbound_tx_id = fields.Many2one(..., readonly=True)` — auto-resolved
- `@api.onchange('pickup_code')` — searches for matching deposit, sets `inbound_tx_id`
- `def action_release()` — uses `self.inbound_tx_id`, raises `UserError('No deposit selected.')` if blank

Failure modes seen in the field:
1. Operator types code, hits Apply faster than the debounce — onchange queued but not yet dispatched
2. Wizard opened with a `default_pickup_code` context — onchange never fires
3. Barcode scanner set the field as one rapid event — UI didn't register a "change" worth dispatching

In every case `inbound_tx_id` stayed empty, and `action_release` raised the generic error.

## Fix pattern — fallback-resolve in the action method

Add a helper that does the same lookup the onchange does, and call it from the action method when the cached state is empty.

```python
def _resolve_X_by_Y(self, y_value):
    """Returns (record|False, error_message|None).  Used by both the
    onchange (for live UX feedback) and action_apply (as a fallback
    when the onchange did not run)."""
    if not y_value:
        return False, 'Type Y first.'
    record = self.env['M'].search([('field', '=', y_value)], limit=1)
    if not record:
        return False, f'No record matches {y_value!r}.'
    return record, None

@api.onchange('y_value')
def _onchange_y_value(self):
    resolved, _err = self._resolve_X_by_Y(self.y_value)
    self.x_id = resolved.id if resolved else False

def action_apply(self):
    self.ensure_one()
    x = self.x_id
    if not x:
        # FALLBACK — onchange may not have fired
        resolved, err = self._resolve_X_by_Y(self.y_value)
        if not resolved:
            raise UserError(err or 'No record selected.')
        self.x_id = resolved.id
        x = resolved
    # Proceed with x.do_thing()...
```

The `_resolve_X_by_Y` helper is the SINGLE SOURCE OF TRUTH for the lookup logic — both UI and programmatic paths use it.

**Bonus:** the helper can return specific diagnostics (wrong kind / wrong stage / already-used / not-found) instead of the generic "no record selected" UserError. That dramatically improves operator experience.

## Detection signal

Operators report:
- "Invalid Operation: <generic error>" when they swear they typed the right value
- The field they typed shows up correctly on screen but the action refuses
- Works fine when they type slowly but fails when they paste / scan / hit Apply quickly

## Reusable across

Any Odoo workspace with TransientModel wizards that resolve a typed value into a record before applying. Especially:
- Pickup-by-code / barcode-scan wizards
- Code-redemption wizards (coupons, gift cards, MTCNs)
- Lookup-by-reference wizards (invoice number, SO number, partner ref)
- Any wizard with `@api.onchange` driving a `Many2one` resolution

Also captures a more general principle: **`@api.onchange` is presentation-layer only. Don't make business logic dependent on it.**
