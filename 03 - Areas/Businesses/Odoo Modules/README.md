---
type: business
tags: [business, odoo-modules, app-store, ecosire-solutions]
status: live
created: 2026-04-22
updated: 2026-04-22
---

# Odoo Modules — The App Store line

**215 modules across Odoo v17 / v18 / v19.** Ecosire's biggest product surface by SKU count. Sold on the Odoo App Store + `ecosire.com` storefront. Single Odoo Partner account: **ECOSIRE**.

## Code lives at

- **Primary development:** `D:\Development\odoo19\server\addons\` (v19 first — 215 modules)
- **Backports:** `D:\Development\odoo18\server\addons\` + `D:\Development\odoo17\server\addons\`
- **Publishing staging:** `D:\Development\EcosireSolutions\{Display Name}\{module}\`
- **CLAUDE.md:** `D:\Development\CLAUDE.md` (the primary workspace)
- **Memory index:** `~/.claude/projects/D--Development/memory/MEMORY.md`

## Pricing tiers

| Tier | USD | Count | Notes |
|------|-----|-------|-------|
| Regional platforms | **$249** | 40 | Wildberries, Daraz, Trendyol, JumiaPay, etc. |
| Mid-tier platforms | **$349** | 46 | Noon, Shopee, PrestaShop, etc. |
| Major platforms | **$499** | 23 | Shopify, Amazon, eBay, Walmart + some adjacent tools (Power BI Connector, OpenClaw AI, SaaS Business Management, Property Management, etc.) |
| Vinculum | **$599** | 1 | Vinculum Hub — enterprise connector |

Plus Branding (8 sub-modules), License Client, Obliq Automation, Subscriptions, and the adjacent tools listed in Tier 3.

Total addressable portfolio: **~215 modules**.

## Workflow (from D:/Development CLAUDE.md)

1. Develop in `odoo19/server/addons/{module}/`
2. Test on Odoo 19
3. Backport to v18, then v17
4. Test all 3 versions
5. **Copy to `EcosireSolutions/{Display Name}/{module}/`** (MANDATORY — never skip)
6. Push to GitHub FROM `EcosireSolutions/` (single source of truth) on `19.0`, `18.0`, `17.0`, `main` branches
7. Submit to Odoo App Store

## Key assets

- **Platform API Intelligence**: `github.com/ecosire/platform-api-intelligence` (private) — 63 platforms, ~6,500+ endpoints researched. MANDATORY to read before developing any connector.
- **Competitor module library**: `D:\OdooModules\` — 69GB, 5,500+ competitor modules (v12-19), read-only study reference.
- **Agent fleet** (27 agents, 101 skills, 16 teams) that accelerate module creation, backporting, publishing, quality gates.

## Cross-workspace dependencies

- **ECOSIRE.COM storefront** pulls pricing from this workspace via `_update_product_prices.mjs`. Any price change needs a live DB sync.
- **License enforcement** via `ecosire_license_client` module — every store module depends on it.

## Current focus

- See `D:\Development\CLAUDE.md` and `~/.claude/projects/D--Development/memory/MEMORY.md` for active sprints / deployments.
- Module publishing pipeline healthy.
- Premium pump in progress for some platforms (noon v19.0.2 completed 2026-04-18).

> Primary workspace for all module work is `D:\Development\`. This note is the **30-second orientation layer**.
