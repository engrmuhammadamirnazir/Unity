---
type: client
tags: [client, active, diamond, stig, quicken-referral, odoo-19]
client: Diamond Investment Group (STIG)
status: active
location: Multi-entity (11 entities, 8 companies after cleanup)
created: 2026-04-22
updated: 2026-04-24
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

## Recent state (2026-04-24)

- **M1 DELIVERED + PAID $1K** (2026-04-21). Infrastructure baseline: Hetzner VPS → Docker → Odoo 19 CE + PG15 → Nginx TLS → UFW/fail2ban → daily backups replicating to Storage Box.
- **M2 DELIVERED + UAT PASS** (2026-04-22): 3 custom modules + MUK theme + om_account suite live on stig_prod. 8 companies (My Company archived), 3 UAT cascade gates reconcile to penny.
- **2026-04-23 spec lock + tier correction** per Eddy's binding WhatsApp: AT=Platinum 35%, US=Gold 30%, PH=Silver 25% (prior seed had AT/US swapped). 3 UAT gates re-run, all PASS at sub level + commission-line level. New evidence PDF `ECOSIRE_Diamond_M2_Demo_Evidence_2026-04-23.pdf`.
- **2026-04-23 user provisioning:** all 9 accounts live on `stig_prod` (8 CEOs scoped per spec + `admin-eddy` super-admin). Login = email-prefix; password = login (per Eddy's "Password = Username" — flag rotation at first login). Smoke-tested 3 logins all return HTTP 303 + correct selector scope.
- **Pre-demo backup taken** 2026-04-23 18:57 UTC — both local and Storage Box have `stig_prod_2026-04-23_1857.pgdump` as a clean rollback point. Tagged verification cascade kept on stig_prod: SO/SUB-AT/2026/00004 (chain run #16) for client inspection.
- **Open architectural question** raised pre-Friday: should the 7-line cascade keep current direct-invoice creation, or move to a proper SO → Delivery → Invoice cycle? Asked Eddy for his accountants' preference; refactor on hold pending his answer (~2h for procurement-only SO cycle, ~1d for full SO cycle).
- Private GitHub: `github.com/ecosire/diamond-investment-group-odoo` at `efe040b`.
- **$2K M2 invoice still on hold** until live demo walkthrough.
- Friday client demo on calendar (slot TBC).

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/diamond_investment_group_client.md`, `session_2026_04_17o.md`, `session_2026_04_20e_diamond_ssl.md`, `session_2026_04_17m.md`
- Code (private repo): `github.com/ecosire/diamond-investment-group-odoo`

## Open threads

- Live demo walkthrough with client → unblocks $2K M2 invoice.
- Eddy to answer SO-vs-direct-invoice architecture question (asked 2026-04-24).
- CEO password rotation reminder for first-login handoff.
- Feedback memory: `feedback_odoo_company_selector_session_cache.md` — after archiving a company, restart + user log-out/log-in required.
- Feedback memory: `feedback_odoo19_res_users_group_ids_rename.md` — Odoo 19 renamed `res.users.groups_id` → `group_ids`; fleet-wide patch needed for any user-provisioning script ported from v17/v18.

## Next actions

- [ ] Receive Eddy's architecture preference → execute chosen option.
- [ ] Schedule live demo walkthrough → send $2K M2 invoice.
- [x] Confirm daily backup chain to Storage Box BX11 working nightly. (verified 2026-04-23: 10 paired snapshots since 2026-04-17, end-to-end cron → local → Storage Box)
