---
type: project
tags: [project, ecosire, odoo, real-estate, client]
status: active
created: 2026-03-02
start-date: 2026-02-28
updated: 2026-04-23
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
| Move | `account.move id=2` on both `saharaproperties` + `testingsahara` |
| Date | 2026-01-01 |
| State | **draft** |
| Lines | 33 |
| Total Dr = Cr | **AED 20,543,270.51** |
| Last updated | 2026-04-23 per `Opening Trial Balance 2026.xlsx` from Zahoor Butt |

Pending for Zahoor (carried from 2026-04-06 notes):
1. Post the OB (still draft)
2. Suleman Investment +43,500 over-allocation on D2 (OB 3,868,500 vs Capital sheet 3,825,000 — unchanged in new xlsx)
3. PDC Receivable per-project variance (DB 5.28M vs Excel 3.20M closing)

---

## Deployment History

- **2026-04-23**: OB draft updated per Zahoor's revised `Opening Trial Balance 2026.xlsx` — added `200230 Utility Payable` Cr 122,736.51 (FEWA, 5-project analytic split) + bumped Retained Earnings Adjustment Dr to 2,040,733.38; grand total 20.42M → 20.54M; both DBs, still draft. pg_dumps in `/home/bitnami/backups/` timestamped `20260423_045014`.
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
