---
type: reference
tags: [ecosire, platform, saas, nestjs, nextjs]
created: 2026-03-02
updated: 2026-04-12
---

# ECOSIRE.COM Platform

> Full-stack SaaS monorepo powering ecosire.com, odovation.com, and muhammadamir.com.
> Source: `D:\ECOSIRE.COM\`

---

## Overview

ECOSIRE.COM is a comprehensive SaaS platform built as a Turborepo monorepo with NestJS backend, Next.js frontend, PostgreSQL database, Redis caching, and Authentik authentication. It serves as the central hub for all Ecosire digital operations.

---

## Architecture

| Layer | Technology | Details |
|-------|-----------|---------|
| Monorepo | Turborepo 2.8 | pnpm 10.28 workspace |
| Backend | NestJS 11 | 56 modules, 310+ TypeScript files |
| Frontend | Next.js 16 | App Router, React 19.2, 249 pages (176 admin + 73 public) |
| UI | Tailwind v4.1 + shadcn/ui | 19 shared components, TanStack Table |
| Database | PostgreSQL 17 (port 5433 local) | Drizzle ORM, 65+ schema files |
| Auth | Authentik | OIDC, JWT, HttpOnly cookies (`ecosire_auth`, `ecosire_refresh`) |
| Cache | Redis 7 | Session, Celery broker, pub/sub |
| i18n | next-intl v4.8.3 | 11 locales, ~12,543 keys (nested namespaces, not flat) |
| Email | React Email + Nodemailer | AWS SES (prod), Mailpit (dev), non-blocking sends |
| Docs | Docusaurus 3.9 | API & user documentation |
| Tests | Vitest + Playwright + k6 | 1,301 unit + 206 integration + 92 security + 416 E2E + 5 k6 scripts |

---

## Applications (7 URLs)

| App | URL | Port | Purpose |
|-----|-----|------|---------|
| web | ecosire.com | 3000 | Main website & admin panel |
| api | api.ecosire.com | 3001 | REST API (Swagger documented) |
| docs | docs.ecosire.com | 3002 | Documentation |
| odovation | odovation.com | 3010 | Client-facing brand |
| muhammadamir | muhammadamir.com | 3020 | Personal brand |
| auth | auth.ecosire.com | 9000 | Authentication (Authentik) |
| status | status.ecosire.com | 3005 | Uptime monitoring (Uptime Kuma) |

---

## Backend Modules (55)

### Core (15)
auth, users, admin, products, billing, licenses, downloads, entitlements, api-keys, email, notifications, support, ai, ecosire-ai, contacts

### Finance (3)
accounting, sales, purchase

### Operations (4)
inventory, manufacturing, pos, rental

### HR (7)
hr, hr-recruitment, hr-time-off, hr-attendance, hr-expense, hr-payroll, hr-appraisal

### Services (4)
helpdesk, planning, approvals, timesheets

### Productivity (2)
documents, knowledge

### Marketing (3)
email-marketing, events, marketing-automation

### Growth Engine (14)
workflows, scoring, contact-events, sms, whatsapp, conversations, funnels, pages, checkout, calendar, chat, affiliates, loyalty, analytics

### Other (2)
crm, projects

---

## Frontend Pages (249)

### Public (73 Pages)
- Homepage, about, pricing, contact
- Blog: 877 English MDX posts × 11 locales = 9,647 MDX files (100% translated)
- Products, services, integrations
- Comparisons, use-cases, solutions
- Glossary, research, support
- Terms, privacy, status
- Cart, checkout, auth

### Admin Panel (176 Pages)
Full Odoo 19 Enterprise suite mirror with:
- CRUD operations for all modules
- Data tables with filters and sorting
- Status badges and dialog forms
- Growth Engine management UI

---

## Growth Engine (11 Apps)

A full sales & marketing automation platform:

**Phase 1 — Foundation**: Workflows, Lead Scoring, Contact Events, SMS, WhatsApp, Unified Inbox

**Phase 2 — Capture & Convert**: Funnels, Pages, Checkout

**Phase 3 — Engage & Retain**: Calendar, Chat, Affiliates, Loyalty

**Phase 4 — Intelligence**: Analytics

---

## Internationalization

100% translated across 11 locales:

| Language | Code |
|----------|------|
| English | en |
| Chinese (Simplified) | zh |
| Spanish | es |
| Arabic | ar |
| Portuguese | pt |
| French | fr |
| German | de |
| Japanese | ja |
| Turkish | tr |
| Hindi | hi |
| Urdu | ur |

---

## Key Documentation

| Document | Purpose |
|----------|---------|
| `D:\ECOSIRE.COM\CLAUDE.md` | Canonical AI development instructions (~400 lines, source of truth) |
| `SALES_MARKETING_MASTER_PLAN.md` | Growth Engine spec |
| `README.md` | Platform overview |
| `ARCHITECTURE.md` | System patterns |
| `GROWTH-ENGINE.md` | Feature docs |
| `scripts/deploy-production.sh` | 12-step zero-downtime deploy |
| `scripts/pre-deploy-check.sh` | 6-step quality gate |

---

## Production Server

| Field | Value |
|-------|-------|
| Server | AWS EC2 t3.large (2 vCPU, 8 GB RAM) |
| IP | 13.223.116.181 |
| OS | Ubuntu 24.04 LTS |
| SSH Key | `ecosire.pem` |
| SSH User | `ubuntu@` (NOT `ecosire@`) |
| Deploy Path | `/opt/ecosire/app/` |
| Architecture | Hybrid — Docker (DB/Auth/Cache) + PM2 (5 Node apps) + Nginx |
| SSL | Cloudflare Origin Certificates |
| DNS | Cloudflare (all 7 domains) |
| Storage | 40 GB gp3 EBS |

See [[Ecosire - Production Servers]] for full deployment commands and architecture.

---

## Security Hardening

- **Auth**: HttpOnly cookies, Authentik OIDC, JWT validation
- **Nginx**: Security headers (HSTS, X-Frame-Options, CSP — no `unsafe-eval`)
- **SQL**: Never `sql.raw()` — always parameterized queries via Drizzle ORM
- **XSS**: DOMPurify for user content, JsonLd XSS prevention
- **SSRF**: URL validation on all external fetches
- **Open Redirect**: Whitelist-based redirect validation
- **Rate Limiting**: API-level rate limits
- **Encryption**: Enforced encryption key for sensitive data

---

## SEO / AEO / GEO / LLM Readiness

- Canonical URLs on all pages
- `robots.ts` and `sitemap.ts` with dynamic generation (5,837 static pages)
- OG Image generation for social sharing
- **86 JSON-LD schemas** across 43 pages
- LLM-ready files (`llms.txt`, `llms-full.txt`)
- `security.txt` compliant
- **21 AI crawlers allowed** (GPTBot, Claude-Web, etc.)
- IndexNow protocol for instant indexing
- `generateMetadata()` on ALL pages
- Multilingual SEO across 11 locales

---

## Product Pricing (Website)

| Tier | Price | Platforms |
|------|-------|-----------|
| Flagship | $499 | Amazon |
| Tier 1 | $199 | eBay, Magento, WooCommerce |
| Tier 2 | $149 | Shopify, Etsy, Lazada, Walmart, Mercado Libre |
| Tier 3 | $99 | Bol.com, Allegro, Cdiscount, eMAG |
| German | $129 | Otto, Zalando, Kaufland, idealo |
| Freemium | $0 | Daraz |

**Price Update Checklist** (5 sources to update):
1. DB seed file: `packages/db/src/seeds/products.ts`
2. Frontend integrations: `apps/web/src/data/integrations.ts`
3. Translation files: `apps/web/messages/{locale}.json`
4. Blog content (if prices mentioned)
5. **LIVE DATABASE** (most critical — seed file does NOT auto-update live DB)

---

## Critical Architecture Notes

- Workspace packages must be pre-built to CommonJS before NestJS can import them
- `@ecosire/db` uses lazy Proxy pattern for database connection
- `dotenv` preloaded in `apps/api/src/main.ts`
- Email templates export render functions (not raw components)
- `en.json` MUST use nested structure (not flat dot-separated keys)
- `NEXT_PUBLIC_*` vars inlined at build time — must rebuild on env change
- Next.js 16 `proxy.ts` replaces `middleware.ts`
- Turbopack is the default bundler
- `lucide-react` requires explicit named imports only

---

## Permanent Project Rules

1. All-language updates for all new/modified pages
2. i18n for all apps (11 locales)
3. Commit after testing
4. MANDATORY update docs after EVERY deployment
5. Translation keys follow nested pattern
6. SEO/AEO/GEO/LLM audit on every new page

---

## Full Audit (2026-03-07)

- **Score**: Overall 5/10 (177 findings: 15 Critical, 52 High, 67 Medium, 43 Low)
- **17/17 immediate fixes** completed and deployed to production
- **Sprint N+1**: DTOs, Redis caching, DB transactions, CI gates, error tracking
- **Sprint N+2**: AuthenticatedRequest, i18n split, org columns

---

## Related

- [[Ecosire Private Limited]] — Parent company
- [[Ecosire - Technical Stack]] — Full tech overview
- [[Ecosire - Production Servers]] — All server details
- [[Marketplace Strategy]] — Business expansion plan
- [[Growth Engine]] — Detailed feature breakdown
- [[Ecosire - Domain Portfolio]] — All domains
