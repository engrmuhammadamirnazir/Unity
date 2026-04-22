---
type: client
tags: [client, active, diamond, stig, quicken-referral, odoo-19]
client: Diamond Investment Group (STIG)
status: active
location: Multi-entity (11 entities, 8 companies after cleanup)
created: 2026-04-22
updated: 2026-04-22
---

# Diamond Investment Group (STIG)

**$4K Odoo implementation referred by Quicken Accounting.** 11 entities, phased delivery.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Hetzner Cloud VPS `91.99.75.227` |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/diamond_group` |
| Domain | `erp.stiggroup.com` (Let's Encrypt E7 cert, exp 2026-07-19, auto-renew) |
| Stack | Docker Odoo 19 CE + PG15, Nginx HTTP/2 TLS, UFW + fail2ban, daily backups to Hetzner Storage Box BX11 `stig/` |
| Prod DB | `stig_prod` |
| Referral source | Quicken Accounting |
| Contract value | $4,000 total |

## Recent state (2026-04-22)

- **M1 DELIVERED + PAID $1K** (2026-04-21). Infrastructure baseline: Hetzner VPS → Docker → Odoo 19 CE + PG15 → Nginx TLS → UFW/fail2ban → daily backups replicating to Storage Box.
- **M2 DELIVERED + UAT PASS** (2026-04-22): 3 custom modules + MUK theme + om_account suite live on stig_prod. 8 companies (My Company archived), 3 UAT cascade gates reconcile to penny (AT €700 / US $1050 / PH ₱350). Signed demo evidence PDF ready.
- Private GitHub: `github.com/ecosire/diamond-investment-group-odoo`.
- **$2K M2 invoice on hold** until live demo walkthrough.
- SSL/DNS: `erp.stiggroup.com` → `91.99.75.227` confirmed by client. Certbot issued + renewal timer active.
- Client credentials delivered via secure channel (not email).

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/diamond_investment_group_client.md`, `session_2026_04_17o.md`, `session_2026_04_20e_diamond_ssl.md`, `session_2026_04_17m.md`
- Code (private repo): `github.com/ecosire/diamond-investment-group-odoo`

## Open threads

- Live demo walkthrough with client → unblocks $2K M2 invoice.
- Feedback memory: `feedback_odoo_company_selector_session_cache.md` — after archiving a company, restart + user log-out/log-in required.

## Next actions

- [ ] Schedule live demo walkthrough → send $2K M2 invoice.
- [ ] Confirm daily backup chain to Storage Box BX11 working nightly.
