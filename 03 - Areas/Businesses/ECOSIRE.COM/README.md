---
type: business
tags: [business, ecosire-com, website, platform, nestjs, nextjs]
domain: ecosire.com
status: live
created: 2026-04-22
updated: 2026-04-22
---

# ECOSIRE.COM — Platform & Product Storefront

**Live at [ecosire.com](https://ecosire.com).** Turborepo monorepo running a NestJS 11 API + Next.js 16 web + Drizzle ORM + PostgreSQL 17. 5 apps, 7 shared packages.

## Code lives at

- **Sibling workspace:** `D:\ECOSIRE.COM\`
- **CLAUDE.md** (architecture + conventions): `D:\ECOSIRE.COM\CLAUDE.md`
- **Memory index:** `~/.claude/projects/D--ECOSIRE-COM/memory/MEMORY.md`

## Infrastructure

- **EC2 t3.xlarge** — `13.223.116.181`, Ubuntu 24.04, `/opt/ecosire/app`
- **SSH key:** `ecosire.pem` (see Unity Server & Key Inventory)
- **Process manager:** PM2 (5 apps: web, api, docs, odovation, muhammadamir)
- **Reverse proxy:** Nginx with Cloudflare SSL (Full Strict)
- **Deploy:** `git pull → pnpm install --frozen-lockfile → npx turbo build → pm2 restart all`
- **Auth:** Native `@ecosire/auth-core` (Authentik decommissioned 2026-04-14)
- **DB:** PostgreSQL 17 · 66 Drizzle schema files · multi-tenant via `organizationId`
- **Prod org UUID:** `c27bdd43-201a-4690-9a16-3b71cfd001c2`

## Scale (April 2026)

- **5 apps**: ecosire-web (3000), ecosire-api (3001), ecosire-docs (3002), odovation (3010), muhammadamir (3020) + 2 thin auth backends (3031, 3032)
- **249 pages**, 310+ TS backend files, 65+ schema files, 8,991 translation keys × 11 locales
- **877 blog posts × 11 locales = 9,647 MDX files**

## Key revenue hooks

- **Module storefront** — pricing tables pulled from `packages/db/src/seeds/products.ts` + `apps/web/src/data/integrations.ts` (pricing page). Sync pricing changes FROM `D:/Development` via `_update_product_prices.mjs`.
- **Service platforms**: Odoo, Shopify, OpenClaw, GoHighLevel, Accounting.
- **Growth Engine**: 11 apps (workflows, sms, whatsapp, inbox, funnels, pages, checkout, calendar, chat, affiliates, loyalty).

## Cross-workspace dependencies

- **Pricing + HTML assets sync FROM `D:/Development`** (the Odoo module workspace). Any module added to D:/Development must be pushed to the ECOSIRE.COM product catalog via DB update.
- **Letterhead PDFs + `ecosire_sign_pdf.py`** come from `D:/Company/Signatures/`.

## Current focus

- See `D:\ECOSIRE.COM\CLAUDE.md` and memory index for per-sprint current state.
- SEO/AEO/GEO posture: 21 AI crawlers explicitly allowed, IndexNow keys registered, JSON-LD on 43 pages, multilingual sitemap.

## Open threads (from memory, may be stale)

- Blog post i18n coverage — any new post MUST have keys in all 11 locale JSON files.
- `seed file ≠ live database` — price changes need SQL UPDATE, not just seed edit.

> For deep architectural context read `D:\ECOSIRE.COM\CLAUDE.md` directly. This note is the **30-second orientation layer**.
