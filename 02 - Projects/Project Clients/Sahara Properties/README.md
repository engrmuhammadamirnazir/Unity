---
type: client
tags: [client, active, sahara, properties, ajman, odoo-19]
client: Sahara Properties
status: live
location: Ajman, UAE
created: 2026-04-22
updated: 2026-04-22
---

# Sahara Properties

**Property management client — Ajman, UAE.** Odoo 19 on Bitnami (AWS), custom Property Management System module.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Bitnami Odoo 19 on AWS — `3.232.201.222` |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/sahara.pem` · user `bitnami` |
| Domain | `saharaproperties.ecosire.com` |
| Custom module | `property_management_system` (PMS) @ `v19.0.6.0.5` |
| Also installed | `private_enterprise` (phone-home blocker) |
| Active DBs | `saharaproperties` (prod) + `testingsahara` (staging) |
| Contact | Zahoor (finance lead) |
| Scale | 100+ tenants, 2 accountants + 1 Financial Manager |

## Recent state (2026-04-22)

- PMS v19.0.6.0.5 LIVE on both DBs; shipped to `github.com/ecosire/Property-Management-System` (main + 19.0, commit `1357c4d`).
- Key features: lease discount, consolidated invoice, unit/project on invoice, PDC form refinements, VAT in tax summary, bi-monthly span-based narration.
- **Fresh-start wipe** completed on both DBs via `scripts/clients/sahara/pms_fresh_start.py` — 7-category wipe preserving opening balance JE (AED 20.42M, 32 GL lines).
- TRN added to invoice/bill header + footer relabeled.
- 326/198 tests pass.

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/sahara_client.md`, `sahara_opening_entry.md`, `session_2026_04_22*.md`
- Code: `D:\Development\odoo19\server\addons\property_management_system\` + `D:\EcosireClients\ActiveClients\Sahara-Properties\`
- Vault legacy: [[PRJ - Sahara Properties]]

## Open threads

- App Store HTML still stale at v5.26 wording — non-blocking for private repo.
- 7 Qs pending for Zahoor (per `sahara_opening_entry.md`).

## Next actions

- [ ] Next deployment/maintenance cadence TBD with client.
- [ ] Periodic tenant report validation after v6.0.5 bi-monthly fix.
