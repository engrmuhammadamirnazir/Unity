---
type: client
tags: [client, active, alvi-dental, healthcare, hetzner, odoo-19]
client: Alvi Dental
status: live (post-migration UAT ongoing)
location: Pakistan
created: 2026-04-22
updated: 2026-04-22
---

# Alvi Dental

**Dental clinic group.** Migrated from AWS to Hetzner EX44 on 2026-04-10 with 3 tenants live on a shared Hetzner instance.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Hetzner EX44 — `23.88.13.113` |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/alvi_aws.pem` (old AWS, decommissioned; Hetzner uses shared key) |
| Domains | `alvidental.ecosire.net` · `iqa.ecosire.net` · `umaimamustafa.ecosire.net` |
| Tenants | 3 DBs live on Odoo 19 |
| Modules | `simplify_access_management` + barcode PDF (rlPyCairo) |

## Recent state (2026-04-22)

- **2026-04-13**: 6 post-migration bugs fixed.
- **2026-04-17** access grants applied:
  - 3 Gulshan Requisition Users
  - Attendance Officer → admin + Amir
  - Own-only attendance → 22 other users
- **2026-04-17** barcode PDF fix: installed rlPyCairo + pycairo + libcairo2-dev in shared Hetzner venv, restarted all 3 tenants, smoke-tested.
- UAT ongoing.

## Feedback captured

- `feedback_hr_attendance_officer_gated.md` — hr_attendance app icon needs group 110 (Officer); default 109 (read-own) does NOT show the dashboard tile.

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/alvidental_client.md`, `session_2026_04_17g.md`, `session_2026_04_17h.md`

## Open threads

- UAT validation across 3 tenants.
- Track any new requisition users / attendance officer changes.

## Next actions

- [ ] Confirm UAT sign-off from each of the 3 tenant admins.
