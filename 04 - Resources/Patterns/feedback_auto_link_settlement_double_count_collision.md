---
name: Odoo create() hooks that auto-create related records can collide with explicit creation in the same transaction
description: When a model's create() override auto-creates related records via FIFO/heuristic logic, callers that ALSO explicitly create those related records in the same transaction will silently double-count. Constraints based on aggregate sums then fire confusing errors. Pass a context flag to suppress the auto-creation for callers that manage the related records themselves.
type: feedback
updated: 2026-05-02
originSessionId: 2026_05_02h_remittance_v33_pickup_doublelink
---

## The rule

When a model's `create()` override invokes a hook (e.g. `_auto_link_<related>_on_create`) that uses heuristic / FIFO logic to create related records automatically, **any caller that explicitly creates the same related records in the same transaction must opt out of the auto-creation** — usually via a context flag.

Otherwise: two competing create paths produce two records, an aggregate constraint sees `actual = 2 × intended`, and raises with a misleading "exceeds limit" error.

## Why

Odoo's `Model.create()` runs synchronously. Any hook fired from `create()` (override, post-init, on-create cron) executes BEFORE the calling code's next line runs. If the hook creates related records, those records are visible (and count toward constraint sums) by the time the caller's own related-record-create runs.

The hook's logic is typically heuristic (FIFO, first-match, partner-based search) and assumes it's the SOLE creator of those records. Callers that have specific intent (the operator typed an exact MTCN — settle THAT specific deposit, not the FIFO-oldest) need a way to bypass the heuristic.

Without an opt-out, the only workaround is post-hoc cleanup: delete the auto-created records in the caller, then create the intended ones. That's brittle and racy.

## Real incident — 2026-05-02 Remittance v10.0.32 pickup wizard

After v10.0.32 fixed the onchange-fallback issue (see [[feedback_api_onchange_doesnt_fire_programmatic_set]]), the pickup wizard could resolve the MTCN. But on `action_release`, the user got:

```
Validation Error
Settlement of 5000.0 AED on TXN/2026/00016 exceeds open amount 0.00.
Reduce the line amount.
```

— even though TXN/2026/00016 had `open_amount = 5000.0` per direct DB query.

**Trace:**
1. `RemittancePickupWizard.action_release()` calls `Tx.create({outbound, source_transaction_id=inbound, ...})`.
2. `RemittanceTransaction.create()` override calls `_auto_link_settlement_on_create()` → `_auto_link_settlement_fifo()` which searches for matching open deposits by beneficiary + currency and creates a `remittance.tx.settlement` record.
3. The wizard's next line was `Settle.create({outbound, inbound, amount})` — explicit settlement.
4. Now TWO settlement records point at TXN/2026/00016, both for 5000.
5. `_check_amount_within_open` constraint runs on the second record: `others = first auto-link record (5000) + prior TXN/2026/00017 settlement (5000) = 10000`. `inbound.amount - already = 10000 - 10000 = 0`. New record `amount = 5000 > 0 + 0.005` → raises.

## Fix pattern

Add a context flag to the auto-link hook. Default behaviour unchanged; opt-in to suppress.

```python
def _auto_link_<related>_on_create(self):
    if self.env.context.get('skip_auto_<related>_link'):
        return self.env['<related>'].browse()  # empty recordset
    return self._do_auto_link()
```

Caller that manages the related records itself:

```python
record = Model.with_context(skip_auto_<related>_link=True).create({...})
# Now create the related records explicitly without competing with auto-link
RelatedModel.create({'parent_id': record.id, 'amount': intended, ...})
```

**Defensive guard in the caller** — useful when the hook lives in a parent codebase you don't fully control:

```python
record = Model.with_context(skip_auto_<related>_link=True).create({...})
if record.related_ids:
    # Hook fired anyway (test factory, future change, mass-create path)
    matching = record.related_ids.filtered(lambda r: r.matches_intent())
    if matching:
        return  # auto-created records satisfy intent — reuse
    # Auto-created the WRONG related — replace
    record.related_ids.sudo().unlink()
    RelatedModel.create({...})
else:
    RelatedModel.create({...})
```

## Why operator's typed input must always win

In the pickup-wizard case, FIFO would settle the OLDEST open deposit. But the operator typed an MTCN that maps to a SPECIFIC deposit. If FIFO wins:
- Audit trail breaks: operator says "released against code X" but GL says "released against deposit Y"
- Customer balance moves the wrong way
- Reconciliation later fails because the partner's MTCN tracker doesn't match the GL

Anytime an operator action is **deposit-specific** (MTCN, RMA, claim ID, voucher), the operator's typed value MUST override FIFO/heuristic auto-link.

## Detection signal

- Constraint error like "exceeds open amount 0.00" or "exceeds remaining 0.00" when the math should clearly work
- Aggregator/SQL view shows `2 × intended` for a related record's sum
- Two records with identical `parent_id`+`amount` created in the same `create_date` second

## Reusable across

- Remittance: pickup wizard, settle wizard, allocation wizard
- Sales: SO autoship/dropship hooks colliding with explicit shipment creation
- Inventory: receipt auto-pull colliding with manual transfer creation
- Accounting: payment auto-reconcile colliding with manual reconciliation
- Any module where `create()` overrides perform side-effect record creation
