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

**Bridge-remittance operator.** Custom `remittance_management` module on Bitnami Odoo 19 Enterprise. Biggest private-module build of Q2 2026 (end-to-end from build to UAT).

## Quick facts

| Field | Value |
|-------|-------|
| Server | Bitnami Odoo 19 Enterprise — `52.28.45.137` |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/remittance.pem` · user `bitnami` |
| Domain | `remittanceaccounting.ecosire.com` |
| Custom module | `remittance_management` @ `v19.0.8.2.4` (LIVE on prod + remtest 2026-04-21 18:22 UTC) |
| DB stats | 1029 transactions identical across both DBs |
| Test coverage | 237/238 tests pass |

## Recent state (2026-04-22)

- **v19.0.8.2.4 LIVE** — seven post-v8.1.5 drill-surface fixes (Matchup scroll + Dashboard Clients/Bridges partner-first + Print PDF partner-aware + Top 5 Bridges → partner + Revenue Trend = invoice.fees + Compensation Aging → Bridge Outstanding Aging on balance.line + Ledger button → remittance.transaction).
- Pure view/asset/report upgrade, no schema change.
- Backups at `/home/bitnami/backups/*_pre_v8_2_4_20260421_182051.*`.
- GitHub push deferred (private repo not yet synced).
- User Guide PDF at `docs/Remittance_User_Manual_v19.0.8.2.3.pdf` (still valid — send to Suleman).

## Design lineage (big session 2026-03-31 onward)

- v19.0.7.0.0 — spec v3.2 decisions: DROP bridge, cash skips invoice, dual-leg, auto variance, multi-client outbound split, full lifecycle tree.
- v19.0.8.0.0 — 15-sheet-feature buildout: split-lines sum==amount, data-driven categories, location auto-infer, cost_tier, 7-bucket P&L, matchup dashboard, Sonstig drill, bank recon + snapshots.
- v19.0.8.1.x — balance.line role-split (131 bridge + 71 client rows on prod), OWL role badges, partner_client/partner_bridge drill-down.
- v19.0.8.2.x — ledger 18→9 cols + 6 legacy menus + dashboard partner-role + Active Bridges + top_bridges bridge→partner resolution.

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/remittance_client.md`, `remittance_module_status.md`, `remittance_session_2026_04_17_complete.md`, `remittance_v813_partner_first_fix.md`, `remittance_spec_v32_decisions.md`, `remittance_audit_2026_04_17.md`, `remittance_v32_build_decisions_2026_04_17.md`, `remittance_v8_build_complete_2026_04_17.md`, `remittance_server.md`, `session_2026_03_31_remittance.md`, `session_2026_04_17j.md`, `session_2026_04_17l.md`
- Code: `D:\Development\odoo19\server\addons\remittance_management\` + `D:\EcosireClients\ActiveClients\Suleman-Remittance\`

## Open threads

- GitHub push for v19.0.8.2.4 deferred → clear backlog.
- I1 variance back-fill pending (per `remittance_audit_2026_04_17.md`).
- User Manual PDF to be sent to Suleman (UAT activation).

## Next actions

- [ ] Push v19.0.8.2.4 to private GitHub repo.
- [ ] Email User Guide PDF to Suleman + kick off UAT.
- [ ] Back-fill I1 variance.
