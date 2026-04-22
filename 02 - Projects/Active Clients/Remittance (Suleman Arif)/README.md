---
type: client
tags: [client, active, remittance, suleman, arif, odoo-19-enterprise]
client: Salueman Arif — Remittance
status: live
location: TBD (Pakistani bridge-remittance operator)
created: 2026-04-22
updated: 2026-04-22
---

# Remittance — Salueman Arif

**Bridge-remittance operator.** Custom `remittance_management` module on Bitnami Odoo 19 Enterprise. Biggest private-module build of Q2 2026 — culminated 2026-04-22 in the v19.0.9.0.0 lifecycle-first refactor that rebuilt the system around end-to-end money-flow tracking.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Bitnami Odoo 19 Enterprise — `52.28.45.137` |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/remittance.pem` · user `bitnami` |
| Domains | `remittanceaccounting.ecosire.com` (prod) + `remtest.ecosire.com` (staging) |
| Custom module | `remittance_management` @ `v19.0.9.0.0` (LIVE on both DBs 2026-04-22 evening) |
| DB state | 0 transactions (wipe approved 2026-04-22), 134 intermediary partners preserved, 10 stage_config seeded |
| GitHub | `engrmuhammadamirnazir/remittance_management` (PRIVATE) — branch `feat/v19.0.9-lifecycle-first` + tag `v19.0.9.0.0` |

## Current state (2026-04-22)

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

## Open threads

- **Suleman UAT** pending on `https://remtest.ecosire.com` — test `bank_eur_out_usd_in` end-to-end (the TXN/2026/01031-class scenario), verify Partner Ledger xlsx widget, Flow Timeline.
- **Polish for next session (non-blocking):** refresh 13 knowledge articles (still reference old "bridge" concept), add Partner Statement PDF report action, fresh data import script once Suleman provides updated xlsx.
- **$1,875 (75%) residual of $2,500 engagement** collectable upon Suleman UAT sign-off.

## Next actions

- [ ] Email Suleman with UAT checklist + remtest login instructions.
- [ ] Refresh knowledge articles (drop "bridge" terminology).
- [ ] Optional: Partner Statement PDF report action.
- [ ] Collect residual $1,875 upon sign-off.
