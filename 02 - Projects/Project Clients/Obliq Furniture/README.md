---
type: client
tags: [client, active, obliq, furniture, dubai, odoo-19]
client: Obliq Furniture Manufacturing LLC
status: live
location: Dubai, UAE
created: 2026-04-22
updated: 2026-04-22
---

# Obliq Furniture

**Furniture manufacturer — Dubai.** Migrated from shared AWS to isolated Hetzner instance on 2026-04-14.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Hetzner isolated VPS (IP per session memory) |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/ecosire.pem` |
| Key modules | `obliq_automation` v19.0.7.0.0 + `private_enterprise` v19.0.3.2.0 |
| Features | BOM cost breakdown (PDF + view), Operations Hub, pricing waterfall, HC customer display fix |

## Recent state (2026-04-22)

- `private_enterprise v19.0.3.2.0` installed 2026-04-21 (expiry 2126-03-28, code `PRIVACY`, publisher cron disabled, 52 active crons).
- `obliq_automation` unaffected by private_enterprise install.
- Migrated off shared AWS on 2026-04-14.

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/obliq_client.md`, `obliq_automation_module.md`, `obliq_financial_consolidation_progress.md`
- Code: `D:\Development\odoo19\server\addons\obliq_automation\` + `D:\EcosireClients\ActiveClients\Obliq\`
- Vault legacy: [[PRJ - Obliq Furniture Manufacturing LLC]]

## Open threads

- Financial consolidation: ALL DONE (POs, invoices, bank recon), 7 follow-ups pending next session.

## Next actions

- [ ] Clarify next feature request cycle with client.
