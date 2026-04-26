---
type: client
tags: [client, active, remittance, suleman, arif, odoo-19-enterprise]
client: Salueman Arif — Remittance
status: live
location: TBD (Pakistani bridge-remittance operator)
created: 2026-04-22
updated: 2026-04-26
---

# Remittance — Salueman Arif

**Bridge-remittance operator.** Custom `remittance_management` module on Bitnami Odoo 19 Enterprise. Biggest private-module build of Q2 2026 — v19.0.9.0.0 lifecycle-first refactor (2026-04-22) followed by **v19.0.10.0.0 (2026-04-26)** which restored the multi-currency Balances dashboard, swept smart defaults across forms, gave the Compliance Matrix actual `ValidationError` teeth (v9 was orphan), and shipped 17 PDF/XLSX/CSV reports under a new Reports menu.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Bitnami Odoo 19 Enterprise — `52.28.45.137` |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/remittance.pem` · user `bitnami` |
| Domains | `remittanceaccounting.ecosire.com` (prod) + `remtest.ecosire.com` (staging) |
| Custom module | `remittance_management` @ **`v19.0.10.0.0`** (LIVE on both DBs 2026-04-26 evening) |
| Last pre-deploy backup | `20260426_195210` — both DB dumps + module folder tarball at `/home/bitnami/backups/` |
| GitHub | `engrmuhammadamirnazir/remittance_management` (PRIVATE) — main `11a7e1b` + tag `v19.0.10.0.0` |
| User Manual PDF (latest) | `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/Remittance_User_Manual_v19.0.10.0.0_Addendum.pdf` (245 KB, 5 pages, Ecosire letterhead) |

## Current state (2026-04-26)

**v19.0.10.0.0 LIVE** — Balances dashboard restored, smart-default sweep, Compliance Matrix wired with real teeth, 17 reports shipped, PKR removed.

### What v10 added
- **`Remittance → Balances`** top-menu (sequence 15) backed by new `remittance.partner.balance` SQL view — one row per partner with EUR/USD/AED columns, 8 filter chips (Clients/Bridges/Both/Has EUR/Has USD/Has AED/Outstanding only/Active 30d). Restores the multi-currency client view that was removed in v9.
- **Smart-default sweep** — `_onchange_kind` locks `currency_id` from kind on `remittance.transaction` (8 of 9 kinds; `variance_adjust` keeps user-pick), `default_remittance_location_id` on `res.partner` auto-fills `location_id` (no overwrite). Module-wide auto-fills also wired on bank.cost / cash.cost / suspense.item / invoice / outbound_allocation. Currency renders as `widget="badge"` when locked.
- **KYC + sanctions toggles** on `res.partner` with audit trail (`*_date`, `*_by_id`, action methods stamping who/when + chatter). Compliance Matrix `_check_required` now actually fires (raises `ValidationError`) on `draft → kyc_cleared` advance — v9 had it defined but NEVER CALLED from `action_advance_stage` (orphan).
- **17 reports** under new `Remittance → Reports` menu with 5 sub-categories on Ecosire letterhead. PDF (QWeb) + XLSX (raw `xlsxwriter`, no OCA dep) + CSV via unified `/remittance/export` controller. `Statement` smart button on partner form for one-click PDF.
- **Audit fixes:** A2 — `_compute_remittance_balances` now reads from `account.move.line` joined via `remittance_tx_id` (was reading `tx.amount` which missed USD leg of `bank_eur_out_usd_in`). A3 — `remittance.balance.line.role` from `is_remittance_intermediary` flag (was guessing from debit/credit sign).

### What v10 removed
- **PKR currency** (3 rows from `res_company_activated_currency_rel`); pre-migration aborts (UserError) if any `remittance_tx_id`-tagged GL line uses PKR.
- Orphan `views/remittance_balance_overview_views.xml`.
- Redundant `My Queue` menu.

### Deploy hotfixes folded in (4 cascading)
1. Self-contained Ecosire letterhead — Odoo 19 `web.external_layout_standard` uses `t-attf-class="header ..."` not literal `class="header"`, so `hasclass('header')` xpath fails. Fixed via primary self-contained template + `t-out 0` slot.
2. `data/knowledge_data.xml` wrapped in `<data noupdate="1">` — Odoo 19 RelaxNG rejects `<odoo noupdate="1">` with direct records.
3. Balance breakdown article body switched from `<![CDATA[]]>` to inline HTML — schema rejects CDATA inside `<field type="html">`.
4. **🔥 P0 PROD BREAK** — `<t t-inherit="web.ListView" t-inherit-mode="primary">` xpath couldn't locate parent element in v19's compiled ListView, broke every list view fleet-wide. Hotfix removed inheriting template + registry entry. **KPI hero banner deferred to v10.0.x patch.**

These four are now fleet-wide feedback files in D:/Development project-local memory: `feedback_odoo19_relaxng_strict.md`, `feedback_odoo19_external_layout_pattern.md`, `feedback_owl_listview_inherit_p0.md`, plus the Bitnami CLI env note added to `remittance_server.md`.

---

## Prior state (2026-04-22) — v19.0.9.0.0 lifecycle-first refactor (PRIOR — superseded by v10)

**v19.0.9.0.0 LIVE** — lifecycle-first refactor responding to Suleman's meeting feedback: "still feels like Excel, need end-to-end tracking of client money until it reaches payer-designated final destination, clear all current data, forward only".

**Architecture:**
- 6 shadow models dropped: `remittance.client / bridge / case / ledger.entry / reconciliation / balance.engine`. `account.move.line` is the single source of truth via new `remittance_tx_id` tag column.
- 4 new models: `remittance.stage.config` (10 seeded gate rows), `remittance.suspense.item` (variance discipline with maker-checker), `remittance.flow` (SQL-view lens over chains), `account.move.line._inherit`.
- `remittance.transaction` refactored: 9 kinds (cash_eur_in/out, cash_aed_in/out/expense, bank_eur_in, bank_eur_out_usd_in — one-tx dual-currency — bank_usd_out, variance_adjust) × 10-stage Kanban pipeline (draft → kyc_cleared → funds_received → eur_locked → usd_booked → usd_out → aed_payout → closed + on_hold + cancelled) × dual-partner legs × `flow_root_id` (stored recursive compute).
- GL dispatcher posts real `account.move` per kind at `funds_received` stage entry. Every line tagged with `remittance_tx_id` so Balance Breakdown reads directly from the GL.
- 2 OWL widgets: Flow Timeline (vertical chain walker on tx form) + Partner Ledger (xlsx-exact grid on res.partner form with yellow/green/blue cell semantics matching NEW BUSINESS 2024.xlsx `accounts` sheet).
- 4-role security: Accountant < Operations < Finance/Compliance < Admin. Maker-checker ir.rule on suspense resolution.
- 12 control accounts seeded per company (1010/1020/1110/1120/1210/1310/1410/1420/2910/4910/4920/6910) + cash+bank journals per activated currency (EUR/USD/AED/PKR).

**Deployment trail:**
- Spec: `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/specs/2026-04-22-remittance-v19.0.9-lifecycle-first-design.md` (883 lines, commit `5239ce7`)
- Plan: `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/plans/2026-04-22-remittance-v19.0.9-implementation-plan.md` (4085 lines, commit `8110f38`)
- Pre-deploy backups: `/home/bitnami/backups/remittanceaccounting_pre_v19_0_9_final_20260422_161808.dump` + `remtest_pre_v19_0_9_20260422_145324.dump`
- Rollback script: `/home/bitnami/rollback_v19_0_9.sh` (prompts YES for safety)
- Post-seed shell script (Enterprise `create_asset` workaround): `D:/Development/odoo19/server/addons/remittance_management/scripts/seed_control_accounts.py`

## Design lineage

- **v19.0.7.0.0** (2026-04-14) — spec v3.2 decisions: DROP bridge, cash skips invoice, dual-leg, auto variance, multi-client outbound split.
- **v19.0.8.0.0** (2026-04-17) — 15-sheet-feature buildout from NEW BUSINESS 2024.xlsx: split-lines, categories, location auto-infer, cost_tier, 7-bucket P&L, matchup dashboard, Sonstig drill.
- **v19.0.8.1.x** (2026-04-17) — balance.line role-split, OWL role badges, partner_client/partner_bridge drill-down.
- **v19.0.8.2.x** (2026-04-17 to 04-21) — ledger compact, legacy menu cleanup, dashboard partner-role, knowledge-article rewrite.
- **v19.0.9.0.0** (2026-04-22) — lifecycle-first rebuild: shadow ledger dropped, real GL posting, Flow Timeline + Partner Ledger widgets, 10-stage Kanban, forward-only clean slate.

## Where detail lives

- **D:/Development project memory** — `remittance_client.md`, `remittance_v19_0_9_lifecycle_first.md`, `session_2026_04_22_remittance_v09_lifecycle.md`, `feedback_enterprise_account_create_asset.md`, `feedback_bitnami_odoo19_migration_signature.md`, `remittance_server.md`, and all prior session/v8.x memory files.
- **Code** — `D:\Development\odoo19\server\addons\remittance_management\` (dev) + `D:\EcosireClients\ActiveClients\Suleman-Remittance\` (client repo + docs + backups).

## Open threads (post-v10)

- **Suleman UAT** pending on `https://remtest.ecosire.com` — open new `Remittance → Balances` dashboard, run a Partner Statement, set a partner's `default_remittance_location_id` and create a tx to verify auto-fill, toggle off `kyc_verified` and verify ValidationError fires.
- **v10.0.x patches (non-blocking, queued):**
    - KPI hero banner above Balances list — re-wire via non-inheriting OWL pattern (wrapper Component or kanban-header).
    - Tri-format Export ▾ button on tx/shipment/balance lists (Reports menu provides access today).
    - Drop dormant `is_pakistan` field references on `remittance.location` model + `views/remittance_location_views.xml`.
    - Refresh `static/description/index.html` (App Store HTML) to drop PKR/Pakistan copy.
    - Re-run Playwright E2E acceptance suite on remtest (prior aborted under active P0 OwlError).
- **Residual payment** collectable upon v10 sign-off (if any of the original $2,500 milestone is outstanding — confirm with Suleman after UAT).

## Next actions

- [ ] Email Suleman with v10 PDF addendum + UAT checklist (Balances menu, KYC toggle, Reports menu).
- [ ] Re-run Playwright E2E acceptance on remtest (8 scenarios spec'd in v10 design doc §8.2).
- [ ] Wire KPI hero banner via non-inheriting pattern (v10.0.1 patch).
- [ ] Cosmetic post-PKR cleanup (is_pakistan field + App Store HTML copy).
- [ ] Collect residual upon v10 sign-off.
