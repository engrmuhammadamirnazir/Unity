---
type: area
tags: [ecosire, odoo, modules, proprietary]
created: 2026-03-02
updated: 2026-04-10
---

# Ecosire — Private & Custom Modules

> Proprietary Odoo modules not sold on the App Store, plus custom client modules.

---

## Private/Internal Modules

| Module | Technical Name | Purpose | Versions | Price |
|--------|---------------|---------|----------|-------|
| Private Enterprise | `private_enterprise` | Blocks Odoo phone-home (3-layer HTTP interception) | 17, 18, 19 | Internal |
| Ecosire License Client | `ecosire_license_client` | License verification (required dependency for all modules) | 17, 18, 19 | Free |
| Branding Management | `ecosire_branding_*` | 8 sub-modules for UI customization & license enforcement | 17, 18, 19 | Internal |
| Simplify Access Management | `simplify_access_management` | Simplified user access controls | 19 | Internal |
| POS Kiosk Limiter | — | Limits POS kiosk attributes | 17 | Internal |
| Subscriptions | `ecosire_subscriptions` | Subscription-based service management | 19 | Internal |

## Published (Not on App Store)

| Module | Technical Name | Purpose | Versions | Price |
|--------|---------------|---------|----------|-------|
| Power BI Connector | `powerbi_connector` | Odoo ↔ Power BI with Azure service principal (203 rows pushed, 3 explorations, keepalive cron) | 17, 18, 19 | $499 |
| SaaS Business Management | `saas_business_management` | Multi-tenant SaaS management | 19 | $499 |
| Property Management System | `property_management_system` | Real estate management (projects, blocks, units, deposits, PDCs, leases, mgmt fees) v19.0.3.10.0 | 19 | $500 |
| OpenClaw AI | `openclaw` | AI agent connector (WhatsApp, Telegram, Slack) | 17, 18, 19 | $250 |

## Custom Client Modules

| Module | Technical Name | Client | Purpose | Version |
|--------|---------------|--------|---------|---------|
| Obliq Automation | `obliq_automation` | Obliq Furniture | BOM cost breakdown (PDF + view), HC customer display, Operations Hub, pricing waterfall | v19.0.7.0.0 |
| Obliq Statement Auto-Import | `obliq_statement_auto_import` | Obliq Furniture | Direct-upload ADCB Bank/CC + Paymob (.xls/.xlsx/.csv) w/ 5-layer dedup, CC-payoff auto-tag, SHA-256 upload log, post-install backfill wizard. LIVE on Hetzner 2026-04-25; 1,257 BSLs tagged. | v19.0.1.0.0 |
| Remittance Management | `remittance_management` | Salueman Arif | Cross-company consolidation, compliance, rate-lock, counterparty validation | v19.0.5.0.2 |
| Material Purchase Requisitions | `ecosire_material_purchase_requisitions` | Internal | Clean-room v19 rebuild of Probuse module, schema-compatible with 3,549 legacy records | 19 |

> Custom client modules go to `github.com/engrmuhammadamirnazir` (PRIVATE), NOT ecosire org.

---

## Private Enterprise — Details

Three-layer HTTP interception to block Odoo's phone-home:

1. `requests.Session.request()` monkey-patch — blocks Python HTTP calls to 40+ Odoo domains
2. `publisher_warranty.contract` AbstractModel override — intercepts warranty calls
3. `iap_tools.iap_jsonrpc` monkey-patch — intercepts IAP service calls

Deployed at: Sahara Properties, Obliq, Oenoteca, Remittance, and other client instances.

---

## Power BI Connector — Details

- **Status**: LIVE on demo server
- **Features**: Azure service principal auth, real-time data push, keepalive cron
- **Verified**: 203 rows pushed, 3 exploration types working
- **Price**: $499 on App Store

---

## Related

- [[Ecosire - Odoo Module Library]] — Public marketplace modules (201)
- [[Ecosire - Technical Stack]] — Full infrastructure
- [[MOC - Ecosire]] — Knowledge map
