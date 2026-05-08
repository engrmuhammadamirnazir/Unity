---
type: client
tags: [client, active, diamond, stig, quicken-referral, odoo-19]
client: Diamond Investment Group (STIG)
status: active
location: Multi-entity (11 entities, 8 companies after cleanup)
created: 2026-04-22
updated: 2026-04-24T19:45Z
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
- **2026-04-23 spec lock + tier correction** per Eddy's binding WhatsApp: AT=Platinum 35%, US=Gold 30%, PH=Silver 25% (prior seed had AT/US swapped). 3 UAT gates re-run, all PASS at sub level + commission-line level. 9 user accounts provisioned (8 CEOs + `admin-eddy` super-admin).
- **2026-04-24 M2 INVENTORY SCOPE EXPANSION LIVE** — Eddy confirmed via email that M2 must add physical-goods tracking + serialized diamonds + buyback reversal + GMV KPI for STIG International Corp.'s IPO / SEC S-1 filing. Absorbed into existing $4K budget. New module `stig_inventory v19.0.1.0.0` deployed to stig_prod (24 files, 1,582 LOC): FZC/USA/AUT/PHI warehouses auto-provisioned, shared inter-company Transit location, master Diamond product (tracking=serial, GIA cert = serial name), 5 sample certificates seeded, `sale.order.action_confirm()` now auto-fires inter-company stock chain, `stig.buyback.contract` extended with reverse-picking chain, Certificate Provenance QWeb PDF + GMV Dashboard (pivot/graph with XLSX export for SEC exhibits).
- **3 demo scenarios seeded on stig_prod** for Amir + Eddy to click through: Scenario 1 SO/SUB-AT/2026/00016 Vienna Boutique EUR 30K SHIPPED (reference), Scenario 2 SO/SUB-US/2026/00004 NYC Diamond House USD 22.5K (delivery READY for UAT to validate), Scenario 3 BBK/00009 active buyback (UAT to trigger). Stock state clean: GIA-2026-0001/0002 in FZC/Stock, 0003 in USA/Stock waiting, 0004/0005 at Customers awaiting buyback return.
- **6-page letterhead UAT Guide PDF** delivered at `03-Deployment/ECOSIRE_Diamond_M2_Inventory_UAT_Guide_2026-04-24.pdf` — unsigned (user-guide policy), 10-item sign-off checklist + signature block on page 6. Generator `deployment/generate_m2_inventory_uat_guide.py` is reusable.
- Private GitHub: `github.com/ecosire/diamond-investment-group-odoo` at `a5eab23` (2026-04-24 commits: `82fe83c` module + `a5eab23` docs).
- **$2K M2 invoice still on hold** until UAT sign-off on the inventory scope (and SO-vs-direct-invoice architecture question still outstanding from 2026-04-23 — though the new SO chain effectively answers it: SO is the canonical source and the 7-line cascade + inter-company stock both spawn from it).

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/diamond_investment_group_client.md`, `session_2026_04_17o.md`, `session_2026_04_20e_diamond_ssl.md`, `session_2026_04_17m.md`
- Code (private repo): `github.com/ecosire/diamond-investment-group-odoo`

## Open threads

- Client to run the 10-item UAT checklist (page 6 of `ECOSIRE_Diamond_M2_Inventory_UAT_Guide_2026-04-24.pdf`) → email sign-off → unblocks $2K M2 invoice.
- SSH key rotation before go-live — client's private key was shared, audit-bad.
- FIRST24 (3 banking entities) inclusion decision — would require VPS resize to CPX31 (8 GB).
- CEO password rotation reminder for first-login handoff.
- Feedback memory: `feedback_odoo_company_selector_session_cache.md` — after archiving a company, restart + user log-out/log-in required.
- Feedback memory: `feedback_odoo19_res_users_group_ids_rename.md` — Odoo 19 renamed `res.users.groups_id` → `group_ids`.
- **NEW** feedback: `feedback_odoo19_stock_module_field_renames.md` — stock module field renames in v19 (move_ids, description_picking, product_uom_id, property_valuation). Fleet-wide.
- **NEW** feedback: `feedback_stock_lot_cross_company_shared.md` — shared-lot pattern (`company_id=False`) for cross-company serialized inventory.

## Next actions

- [ ] Client runs 10-item UAT checklist on the 3 seeded scenarios → sign-off.
- [ ] On sign-off, send $2K M2 invoice ECO-INV-2026-0042 (already drafted).
- [ ] Rotate SSH key before go-live.
- [ ] (Stretch / M3) Certificate Provenance XLSX export, Kimberley Process origin validation, multi-certificate aggregate SEC S-1 exhibit template.
- [x] Confirm daily backup chain to Storage Box BX11 working nightly. (verified 2026-04-23)
