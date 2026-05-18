---
name: NEVER use TRUNCATE ... CASCADE on Odoo accounting tables — cascade chain reaches res_company / res_partner / account_account / res_users and wipes master data
description: Postgres TRUNCATE ... CASCADE propagates through Odoo's dense FK graph and pulls master tables (res_company, res_partner, account_account, account_journal, res_currency, res_users) down with transactional tables. Use ordered DELETE instead — most Odoo FKs are SET NULL or RESTRICT, only aml.move_id → account_move is CASCADE which does the line cleanup. Always pg_dump first.
type: feedback
updated: 2026-05-19
originSessionId: 2026_05_19_suleman_txn_00069_diagnosis_and_dual_db_wipe
---

## The rule

**On any Odoo Postgres DB, never use `TRUNCATE ... RESTART IDENTITY CASCADE` to clear transactional data, even when you've enumerated the transactional tables explicitly.** The cascade chain is unbounded in practice. Use ordered `DELETE` statements per-table, in dependency order.

## Why

Caught live 2026-05-19 on Suleman's bitnami AWS `remtest` (DB at v19.0.10.2.1). User wanted a transactional wipe ("keep COA, journals, partners, currencies, FX rates, users"). One `TRUNCATE ... RESTART IDENTITY CASCADE` listed ~42 transactional tables (`account_move`, `account_payment`, `account_bank_statement_line`, transactional `remittance_*`, etc.). The cascade chain reached `res_company` (0 rows), `res_partner` (0), `account_account` (0), `account_journal` (0), `res_currency` (0), `res_users` (0). Master data gone. Restored from a pre-wipe `pg_dump` taken minutes earlier; zero data lost only because the backup existed.

Odoo's FK graph is dense enough (m2m rel tables + cross-module FKs + computed-cache tables) that tracing the exact chain isn't useful. Treat TRUNCATE CASCADE as effectively unbounded on this kind of schema.

## How to apply

### 1. Always `pg_dump` before any bulk DELETE/TRUNCATE

```bash
PGPASSWORD=... pg_dump -h 127.0.0.1 -U <user> -F c \
  -f /tmp/<db>_pre_wipe_$(date +%Y%m%d_%H%M%S).pgdump <db>
```

~6 MB per DB on Suleman's remtest, <2s. Keep at least 7 days.

### 2. Verify FK ON DELETE rules first

```sql
SELECT conname, conrelid::regclass AS from_tbl, confrelid::regclass AS to_tbl, confdeltype
FROM pg_constraint
WHERE contype='f' AND (
  conrelid::regclass::text = '<table>' OR confrelid::regclass::text = '<table>'
)
ORDER BY conrelid::regclass::text;
-- confdeltype:  c=CASCADE, n=SET NULL, r=RESTRICT, a=NO ACTION
```

In Odoo accounting:
- `account_move_line.move_id → account_move` is **CASCADE** — deleting an `account_move` auto-deletes its lines
- `account_move_line.remittance_tx_id → remittance_transaction` is **SET NULL** — deleting rem_tx leaves AML in place with NULL ref
- `remittance_transaction.odoo_move_id → account_move` is **SET NULL**
- `account_move.origin_payment_id → account_payment` is **SET NULL**
- `account_move.statement_line_id → account_bank_statement_line` is **SET NULL**

These mean ordered DELETE works cleanly without needing to manually NULL out cross-refs.

### 3. Wipe pattern (transactional-only, keeps master data)

In one `BEGIN; ... COMMIT;` transaction, `-v ON_ERROR_STOP=1`:

```sql
-- Leaf remittance subordinate tables first
DELETE FROM remittance_compliance;
DELETE FROM remittance_suspense_item;
DELETE FROM remittance_outbound_allocation;
DELETE FROM remittance_tx_settlement;
DELETE FROM remittance_transaction_link;
DELETE FROM remittance_transaction_split;
DELETE FROM remittance_shipment_cost;
DELETE FROM remittance_shipment;
DELETE FROM remittance_logistics_invoice;
DELETE FROM remittance_invoice;
-- ... (other transactional remittance_* tables)
-- Wizard transient tables (usually empty)
DELETE FROM remittance_settle_wizard_line;
DELETE FROM remittance_settle_wizard;
-- ... (other *_wizard tables)
-- M2M rel tables
DELETE FROM remittance_client_attachment_rel;
DELETE FROM remittance_compliance_attachment_rel;
DELETE FROM ir_attachment_remittance_compliance_rel;
-- Parent transaction table (cross-refs handled by FK SET NULL rules)
DELETE FROM remittance_transaction;
-- Account side
DELETE FROM account_partial_reconcile;
DELETE FROM account_payment;
DELETE FROM account_bank_statement_line;
DELETE FROM account_bank_statement;
DELETE FROM account_move;  -- account_move_line auto-cascades via CASCADE FK
```

Verify **inside the same transaction** with master-data counts before COMMIT. Roll back if anything looks suspicious.

### 4. Views must NOT appear in TRUNCATE/DELETE lists

In Odoo v19, `remittance_balance_line`, `remittance_journey`, `remittance_partner_balance`, `remittance_company_solvency`, `remittance_company_turnover`, `remittance_sonstig_report` are **VIEWs**. Listing them aborts the transaction with `ERROR: "<name>" is not a table`. Pre-filter:

```sql
SELECT table_name, table_type FROM information_schema.tables
WHERE table_schema='public' AND table_name LIKE '<prefix>%';
```

Only `BASE TABLE` rows belong in the wipe list.

### 5. Sequences are NOT reset by DELETE

`remittance.transaction.name` next value (TXN/YYYY/####) lives in `ir_sequence_date_range` — DELETE on `remittance_transaction` doesn't roll that back. Default after wipe: next tx continues from the last deleted number + 1. If user wants names to restart from 00001, reset `ir_sequence_date_range` explicitly. Same applies to `account_move.name` per-journal sequences.

### 6. Terminate active Odoo connections before destructive ops

```sql
SELECT pg_terminate_backend(pid) FROM pg_stat_activity
 WHERE datname='<db>' AND pid != pg_backend_pid();
```

Otherwise `DROP DATABASE` blocks and `pg_restore` can race against an active session.

### 7. Tell the user to log out + log back in after a bulk wipe

Odoo caches partner/company/account records in process memory; a stale session can hit ORM cache mismatches when posting fresh data. Also restart workers if the DB went through DROP/CREATE/restore.

## Applies to

Any Odoo Postgres DB on any workspace — Development test DBs, ECOSIRE.IO managed-hosting client DBs, future deploys. Generalises beyond remittance. Cross-references:

- [[feedback_db_wipe_ref_vs_name_pattern]] — column selection when wiping moves by SQL
- [[feedback_backup_pipeline_must_verify_push]] — verifying backups before destructive ops

## Out-of-scope

Building an Odoo CLI helper (`--clear-transactional`) or adding a `wipe_transactional()` server action — nice to have but tooling, not a code bug.
