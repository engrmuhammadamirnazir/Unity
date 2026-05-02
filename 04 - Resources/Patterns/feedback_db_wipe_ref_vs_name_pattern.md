---
name: When wiping account.move by SQL pattern, match on `name` (move number) NOT `ref` — refs often carry "Payment for X" / "Reversal of: X" prefixes that don't start with the journal code
description: A SQL DELETE with `ref ILIKE 'INV/2026/%'` looks safe but misses every move whose ref is `'Payment for INV/2026/00001'`, `'Reversal of: BNK1/2026/00001, ...'`, or any ref Odoo built that PREFIXES the journal-numbered name with descriptive text. The move's own `name` always starts with the journal code (e.g. `BNK1/2026/00001`), so use `name LIKE 'PREFIX/...'`. For SQL pattern wipes, use NAME unless you really mean to filter by REF.
type: feedback
updated: 2026-05-02
originSessionId: 2026_05_02i_remittance_v37_v38_bank_hybrid
---

## The rule

**Wipe `account.move` rows by their `name` column, not their `ref` column.**

```sql
-- ❌ WRONG — misses any move whose ref is "Payment for X" or "Reversal of: X"
DELETE FROM account_move WHERE
       ref ILIKE 'INV/2026/%'
    OR ref ILIKE 'BNK1/2026/%'
    OR ref ILIKE 'RINV/2026/%';

-- ✅ RIGHT — name always starts with the journal code prefix
DELETE FROM account_move WHERE
       name LIKE 'INV/2026/%'
    OR name LIKE 'BNK1/2026/%'
    OR name LIKE 'RINV/2026/%';
```

If you really do need REF-based filtering (e.g. catch-all where name is unknown), use leading wildcard `ref ILIKE '%INV/2026/%'` OR pattern-match all the descriptive prefixes:

```sql
ref ILIKE 'Payment for INV/2026/%'
OR ref ILIKE 'Reversal of:%INV/2026/%'
OR ref ILIKE 'Remittance%'
OR ref ILIKE 'INV/2026/%'
```

## Why

Odoo's `account.move` has TWO label fields:

| Field | Format | Built by |
|-------|--------|----------|
| `name` | `JOURNAL_CODE/YYYY/NNNNN` (e.g. `BNK1/2026/00001`) | `ir.sequence` on first post; locked thereafter |
| `ref` | Free-text description | App code that creates the move (varies) |

The `name` is structurally consistent — it ALWAYS starts with the journal code prefix. `ref` is a free-text descriptive field that often carries:

- Bank payments: `ref = 'Payment for ' + invoice.name` (so `ref = 'Payment for INV/2026/00001'`, NOT starting with `'BNK1/'` or `'INV/'`)
- Move reversals: `ref = 'Reversal of: ' + reversed_move.name + ', <reason>'` (e.g. `ref = 'Reversal of: INV/2026/00001, v10.0.27 retroactive migration to hawala convention'`)
- Cross-currency exchange moves: `ref = 'Reversal of: EXCH/2026/05/0004'`
- Custom code that sets `ref` to a domain reference, settlement number, etc.

So when you grep/wipe by `ref ILIKE 'INV/2026/%'`, every "Payment for INV/2026/00001" move slips through — they have `name = 'BNK1/2026/00001'` and `ref = 'Payment for INV/2026/00001'`. Neither side of the pattern matches.

## Real incident — 2026-05-02 Remittance v10.0.37 wipe

Goal: clear all transactions on `remtest` for a fresh demo. First-pass SQL used `ref ILIKE 'INV/2026/%' OR ref ILIKE 'BNK1/2026/%' OR ref ILIKE 'RINV/2026/%' OR ref ILIKE 'BEUR/2026/%' …`. Wipe reported `DELETE 16` (account_move) + clean.

After the wipe, Treasury KPI on the dashboard still showed **25,000.00 EUR**. Investigation:

```sql
SELECT j.code, am.name, am.ref FROM account_move am
JOIN account_journal j ON j.id = am.journal_id
WHERE am.state = 'posted' AND am.date >= '2026-01-01';
```

17 moves remained:

| journal | name | ref |
|---|---|---|
| BNK1 | BNK1/2026/00001 | `Payment for INV/2026/00001` |
| INV | RINV/2026/00001 | `Reversal of: INV/2026/00001, v10.0.27 retroactive migration ...` |
| INV | INV/2026/00002 | `Reversal of: BNK1/2026/00001, v10.0.27 retroactive migration ...` |
| BNK1 | BNK1/2026/00002 | `Payment for INV/2026/00003` |
| ... | ... | ... |

These all had REFs that DON'T start with the journal prefix. The Treasury KPI summed `aml.debit - aml.credit` on these moves' lines (all on bank/cash accounts) — 25k EUR remained.

**Fix:** second-pass DELETE switched to `name LIKE 'PREFIX/2026/%'`. Removed 60 more AMLs + 23 moves (some with reconcile FKs requiring partial/full reconcile cleanup first). Treasury post-fix: 0 across all 14 bank/cash journals.

## Detection signals

- After a "wipe transactions" SQL pass, dashboard KPIs (Treasury, Outstanding, etc.) still show non-zero values
- `SELECT COUNT(*) FROM account_move WHERE date >= 'YYYY-...' AND state='posted'` returns more than expected
- Inspecting leftover moves shows refs like `Payment for X`, `Reversal of: X`, etc. that don't start with the journal prefix
- The journals with leftovers tend to be the auto-generated ones (bank payments, exchange-rate moves, reversal moves), not the originating invoice journal

## How to apply

1. **For account.move wipe SQL: use `name`, not `ref`.** Move names always start with the journal code.
2. **If you must use ref:** add leading wildcards (`ref ILIKE '%PREFIX/...'`) and explicitly list known descriptive prefixes (`'Payment for ...'`, `'Reversal of: ...'`, `'Remittance ...'`, etc.).
3. **After any wipe, run a verification query** — count remaining moves by journal code AND inspect a sample if non-zero.
4. **Test the wipe on a backup or non-prod DB first.** Easy rollback via `pg_restore`.
5. **Reconcile FK gotcha:** before deleting AMLs, drop `account_partial_reconcile` and `account_full_reconcile` rows that reference them (RESTRICT FK). Standard pattern from this incident:
   ```sql
   DELETE FROM account_partial_reconcile WHERE debit_move_id IN (...) OR credit_move_id IN (...);
   DELETE FROM account_full_reconcile WHERE id IN (SELECT DISTINCT full_reconcile_id FROM ... WHERE full_reconcile_id IS NOT NULL);
   UPDATE account_move SET state='draft' WHERE ...;  -- can't delete posted directly
   DELETE FROM account_move_line WHERE move_id IN (...);
   DELETE FROM account_move WHERE ...;
   ```

## Reusable across

Any Odoo workspace where ad-hoc SQL is used to wipe transactional data:
- Demo prep / sandbox reset
- Year-end purge of test transactions
- Module reinstall preparation when "uninstall + reinstall" is too heavy
- Multi-tenant client cleanup
- Any module with custom JE builders that sets `ref` to descriptive text rather than the move's own name

## Related memories

- [reference_account_move_reversal_pattern.md](reference_account_move_reversal_pattern.md) — Odoo reversal mechanics (where `Reversal of:` ref text comes from)
- [feedback_aggregator_currency_axis_bug.md](feedback_aggregator_currency_axis_bug.md) — same session: read-side aggregator bug
