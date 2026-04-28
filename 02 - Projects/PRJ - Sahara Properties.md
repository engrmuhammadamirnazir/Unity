---
type: project
tags: [project, ecosire, odoo, real-estate, client]
status: active
created: 2026-03-02
start-date: 2026-02-28
updated: 2026-04-29
---

# PRJ — Sahara Properties

> Odoo 19 property management system for Sahara Properties.
> Source: `D:\Ecosire Customer Service\Active Clients\Sahara Properties\`

---

## Overview

| Field              | Value                                           |
| ------------------ | ----------------------------------------------- |
| **Client**         | Sahara Properties                               |
| **Service**        | Custom Odoo 19 ERP + Property Management Module |
| **Server IP**      | 3.232.201.222                                   |
| **SSH User**       | bitnami                                         |
| **PEM Key**        | `D:/EcosireClients/ActiveClients/Sahara-Properties/sahara.pem` (see [[Server & Key Inventory]]) |
| **URL**            | https://saharaproperties.ecosire.com            |
| **Admin Password** | See [[Client Credentials]]                                  |
| **Started**        | February 28, 2026                               |
| **Duration**       | ~2 weeks                                        |
| **Module Cost**    | $500                                            |

---

## Server Details

| Field | Value |
|-------|-------|
| Stack | Bitnami Odoo 19 |
| RAM | 8 GB |
| Disk | 60 GB |
| SSL | Let's Encrypt (expires Jun 6, 2026) |
| Custom Addons | `/bitnami/odoo/addons` |
| Core Addons | `/opt/bitnami/odoo/lib/odoo/addons` |
| Odoo Config | `/opt/bitnami/odoo/conf/odoo.conf` |
| Odoo Logs | `/opt/bitnami/odoo/log/odoo-server.log` |
| Venv Pip | `/opt/bitnami/odoo/venv/bin/pip` |

---

## Custom Module: Property Management System

**Technical Name:** `property_management_system`

### Features
1. **Electricity Recovery Invoicing** — Automated utility billing
2. **Security Deposit Lifecycle** — Full deposit tracking and management
3. **Analytic Profitability** — Property-level profit analysis
4. **PDC Import** — Post-dated cheque bulk import
5. **Bounced Cheque Handling** — Automated bounce processing
6. **Maker-Verifier-Approver** — 3-tier approval workflow

### Design Preferences
- Enterprise-minimal (flat cards, no gradients, no glass-morphism)
- Primary color: `#714B67` (Odoo purple)
- 8px grid spacing
- System fonts (no custom fonts)
- Left-border accent on KPI cards
- Mobile-first responsive design

---

## Deployed Modules

| Module | Type | Purpose |
|--------|------|---------|
| property_management_system | Custom | Core property management |
| simplify_access_management | Custom | Simplified user access |
| advanced_web_domain_widget | 3rd Party | Enhanced domain filters |
| auto_database_backup | 3rd Party | Automated DB backups (ECOSIRE branded) |
| private_enterprise | Custom | Enterprise subscription override protection |

---

## Private Enterprise Module

**Technical Name:** `private_enterprise`

Three-layer HTTP interception to block Odoo's phone-home calls:

1. **`requests.Session.request()` monkey-patch** — Blocks ALL Python HTTP calls to 40+ Odoo domains
2. **`publisher_warranty.contract` override** — AbstractModel `_inherit` override
3. **`iap_tools.iap_jsonrpc` monkey-patch** — Intercepts IAP service calls

### Blocked Odoo Domains (40+)
Core: `services.odoo.com`, `www.odoo.com`, `upgrade.odoo.com`
IAP: `iap.odoo.com`, `iap-services.odoo.com`
Banking: `odoofin.com`, `www.odoofin.com`
API Services: `accounts.odoo.com`, `nightly.odoo.com`
Partner/GeoIP/IoT + 20+ L10n EDI domains

---

## Odoo 19 Lessons Learned

Issues encountered and resolved during this project:

| Issue | Solution |
|-------|----------|
| `name_get()` removed in Odoo 19 | Use `_compute_display_name()` |
| `category_id` removed from `res.groups` | Use `privilege_id` instead |
| `_sql_constraints` deprecated | Use `models.Constraint()` |
| `<i>` FA class in kanban needs `title` | Always add `title` attribute |
| `.o_action_manager` clips overflow | Use `min-height: 100%` not `100vh` |
| XML data file load order matters | Ensure dependency order in `__manifest__.py` |

---

## Production Data (as of 2026-03-08)

| Item | Count |
|------|-------|
| Projects | 12 |
| Tenants | ~162 |
| Units | 156 |
| Meters | 196 |
| PDCs | 577 |
| Security Deposits | 70 |
| Bank Journals | 4 (ENBD, ADIB, Ruya, DIB) |
| Knowledge Articles | 11 |

---

## Phase 2: Landlord Accounting (PLANNED)

- **Status**: Planning (pending client meeting)
- **Scope**: Master lease tracking (MOU + Ejari), prepaid rent accounting, landlord PDCs, landlord security deposits, maintenance
- **5 new models**: landlord.lease, payment.line, amortization, renewal, maintenance
- **Target Version**: 19.0.3.0.0

## Phase 3: Rent Invoicing (PLANNED)

- **Status**: DRAFT plan, waiting for client feedback
- **Scope**: 2 new models (lease, rent schedule) + invoice generation + PDC integration
- **Target Version**: 19.0.4.0.0

---

## Opening Balance (draft — pending Zahoor posting approval)

| Field | Value |
|-------|------:|
| Move | `account.move id=2` on `saharaproperties` (prod). `testingsahara` not yet synced for this pass. |
| Date | 2026-01-01 |
| State | **draft** |
| Lines | **90** (was 36; 36 - 1 PDC lump + 55 tenant AR lines) |
| Total Dr = Cr | **AED 22,934,192.51** (unchanged) |
| Last updated | 2026-04-29 (PDC→AR per-tenant reclass + 5 new related-party accts) |

Structure after 2026-04-29 reclass: AR account 110000 now holds **56 partner-tagged Dr lines totaling AED 3,205,492** (3,196,067 reclass across 55 tenants + 9,425 Tashkhis tagged with partner_id=33). The unpartnered `PDC Receivable Dr 3,196,067` lump line is removed. Per-project tenant subtotals: D1 14×667,570 / D2 7×849,750 / Taxi 9×825,184 / BO 17×713,153 / ASAS 4×110,000 / Sajaa 3×30,410. 144 Project decided as "Not Included" — Tashkhis 9,425 is its sole residual. Earlier 2026-04-23 afternoon structure (gross/net asset split, Utility Payable FEWA, Advance Tenant +2,250) all persists.

5 new related-party collection accounts (`asset_current`, `reconcile=True`) — Sahara also receives tenant rent into these:
| Code | Name | id | Journal |
|---|---|---|---|
| 110700 | Due from CEO Mansoor | 301 | CMAN (id=20) |
| 110710 | Due from Tasrie ADCB Account | 302 | CTAB (id=21) |
| 110720 | Due from Tasrie HR and Business Consultancy | 303 | CTHR (id=22) |
| 110730 | Due from Al Maraem General Maint. LLC | 304 | CALM (id=23) |
| 110740 | Due from Daleel General Trading LLC | 305 | CDAL (id=24) |

All 5 journals are **cash-type** (not bank — no IBAN forced since Sahara doesn't own them). Receipts flow `Dr 110xxx Due from X / Cr 110000 AR (tenant)`; subsequent remittance to a real Sahara bank closes with `Dr 100001 ENBD / Cr 110xxx`.

3 new `res.partner`: Al Maraem General Maint. LLC (id=259), Daleel General Trading LLC (id=260), Al Qazal Tailoring & Classic Tailoring (id=261, tenant Taxi WH11 AED 56,250).

Backup: `Sahara-Properties/backups/pre_opening_tb_reclass_2026_04_29.dump` (8.8 MB pg_dump custom). Rollback (only safe before any other writes hit prod after 02:03 Asia/Karachi 2026-04-29): `pg_restore -U bn_odoo -d saharaproperties --clean --if-exists <dump>` from prod server.

Pending for Zahoor (carried forward):
1. **Click Post** on `move_id=2` (still draft after reclass — agent did NOT auto-post per safety rule)
2. Suleman Investment +43,500 over-allocation on D2 (OB 3,868,500 vs Capital sheet 3,825,000)
3. PDC Receivable per-project variance (DB 5.28M vs Excel 3.20M closing — likely state mismatch / dup imports, needs per-tenant pass)
4. Activate the 11 DRAFT asset/depreciation records once OB is posted — the 2 contra-asset OB lines become the baseline for future depreciation bookings
5. Replicate today's reclass on `testingsahara` once prod is signed off

---

## Deployment History

- **2026-04-29 (night, ~02:00–02:03 Asia/Karachi)**: Zahoor's WhatsApp update implemented on `saharaproperties` prod via accountant agent (delegated under "use best judgment / assign codes yourself"). **5 new asset accounts** 110700–110740 + **5 cash journals** CMAN/CTAB/CTHR/CALM/CDAL + **3 new partners** (Al Maraem 259, Daleel 260, Al Qazal Tailoring 261). **Opening JE `move_id=2` mutated in place** (lucky break — still draft from 2026-04-23, no separate adjustment JE needed): removed unpartnered `PDC Receivable Dr 3,196,067` lump, added 55 partner-tagged tenant AR lines on 110000 (D1×14 / D2×7 / Taxi×9 / BO×17 / ASAS×4 / Sajaa×3 = 3,196,067), tagged Tashkhis line partner_id=33. AR 110000 sum after = AED 3,205,492.00 across 56 partner-tagged lines, 100% partner-tagged. Move balance unchanged AED 22,934,192.51 Dr=Cr, 90 lines, still **draft**. 144 Project decided unilaterally as "Not Included" per PDC sheet label — Tashkhis 9,425 is sole residual. Pre-write backup at `pre_opening_tb_reclass_2026_04_29.dump`. Plan + posted reports at `Sahara-Properties/docs/Opening_TB_Reclass_{Plan,Posted}_2026_04_29.md`. Scripts at `Sahara-Properties/scripts/opening_tb_reclass_2026_04_29/`. Reusable patterns captured: **Atomic Draft-JE Mutation Pattern** (line_ids commands on draft move) + **In-line Partner Creation via Override Manifest** (CREATE: directive). `testingsahara` not synced for this pass — to replicate after Zahoor signs off.
- **2026-04-23 (afternoon)**: OB draft updated AGAIN per Zahoor's re-uploaded xlsx (mtime 13:36, 4h after the morning pass). Gross/net asset split applied: LHI 10.65M → 12.75M gross + Accum Dep (130010) Cr 2.09M contra-asset; LAC 1.92M → 2.21M gross + Accum Amort (130110) Cr 294.8K contra-asset; new AR line (110000) Dr 9,425 for Al Tashkhis Garage (D1); Advance Tenant (200220) Cr 144,057 → 146,307; RE Adj (310100) 2,040,733.38 → 2,033,559.13. Grand total 20.54M → **22.93M**, 33 → 36 lines, both DBs still draft. pg_dumps in `/home/bitnami/backups/` timestamped `20260423_084754` (test) / `20260423_084940` (prod). Answers Zahoor's pending question #4 (gross vs net asset presentation).
- **2026-04-23 (morning)**: OB draft updated per Zahoor's first revised xlsx (mtime 09:37) — added `200230 Utility Payable` Cr 122,736.51 (FEWA, 5-project analytic split) + bumped Retained Earnings Adjustment Dr to 2,040,733.38; grand total 20.42M → 20.54M; both DBs, still draft. pg_dumps in `/home/bitnami/backups/` timestamped `20260423_045014`. SUPERSEDED same day by afternoon pass.
- **2026-04-22 (late eve)**: res.partner dedup — 247→184 partners on prod, testingsahara re-cloned from post-merge prod. Reusable pipeline at `D:/EcosireClients/ActiveClients/Sahara-Properties/scripts/partner_dedup/`.
- **2026-04-22 (late eve)**: PMS v19.0.6.0.5 LIVE on both DBs — span-based narration guard for consolidated invoice lines spanning 12+ months (replaces failed v6.0.4 payment_frequency guard). Published to `github.com/ecosire/Property-Management-System` (main + 19.0 @ `1357c4d`).
- **2026-04-20 to 04-22**: Sahara PMS v5.23 → v6.0.5 (11 sub-releases) — bulk reading wizard, meter-rent dedup, invoice smart buttons, VAT fallback, discount reconstruction, tenant PDC smart buttons, narration guards.
- **2026-04-14**: Migrated from shared AWS to isolated AWS Bitnami `3.232.201.222` (decoupled from former shared-server setup with Obliq/Oenoteca).
- **2026-03-08**: M2/M3 data import -- 577 PDCs, 4 bank journals, 28 new partners
- **2026-03-06**: Security deposits imported (70 records), new fields (ejari_ref, fewa_account, etc.)
- **2026-03-04**: PMS v19.0.3.0.0 deployed (landlord accounting features)
- **2026-03-03**: PMS Phase 0-6 complete, Knowledge module + 9 articles, v19.0.2.2.0
- **2026-02-28**: Initial setup -- Bitnami Odoo 19, SSL, enterprise modules (662->1423), PMS module

---

## Related

- [[Ecosire Private Limited]] — Service provider
- [[Ecosire - Client Portfolio]] — Client list
- [[Ecosire - Production Servers]] — Server reference
- [[MOC - Projects]] — Project index
