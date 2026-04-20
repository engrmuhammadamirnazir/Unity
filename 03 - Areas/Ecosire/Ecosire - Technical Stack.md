---
type: reference
category: ecosire, technical
tags: [technical, infrastructure, ecosire, saas]
updated: 2026-04-10
---

# Ecosire — Technical Stack

> Complete technical infrastructure for [[Ecosire Private Limited]] — spanning Odoo development, SaaS platform, and DevOps.

---

## ECOSIRE.COM — SaaS Platform Stack

### Architecture Overview

| Layer | Technology |
|-------|-----------|
| **Monorepo** | Turborepo 2.8 + pnpm 10.28 |
| **Backend** | NestJS 11 (55 modules), Drizzle ORM |
| **Frontend** | Next.js 16 (App Router), React 19.2, Tailwind CSS v4.1, shadcn/ui |
| **Database** | PostgreSQL 17 |
| **Auth** | Authentik (OIDC), JWT guards, HttpOnly cookies |
| **Cache** | Redis 7 |
| **i18n** | next-intl v4.8.3 -- 11 locales, 8,991 keys, 100% translated |
| **Email** | React Email + Nodemailer (Mailpit dev / AWS SES prod) |
| **Docs** | Docusaurus 3.9 |
| **Infra** | Docker, PM2, Nginx, AWS EC2, Cloudflare |

### Live URLs

| Service | URL | Port | Tech |
|---------|-----|------|------|
| Website | ecosire.com | 3000 | Next.js 16 |
| API | api.ecosire.com | 3001 | NestJS 11 |
| Docs | docs.ecosire.com | 3002 | Docusaurus |
| Odovation | odovation.com | 3010 | Next.js |
| Muhammad Amir | muhammadamir.com | 3020 | Next.js |
| Auth | auth.ecosire.com | 9000 | Authentik |
| Status | status.ecosire.com | 3005 | Uptime Kuma |

### Monorepo Structure (`D:\ECOSIRE.COM\`)

```
ecosire.com/
├── apps/
│   ├── api/            # NestJS 11 — 54 modules, 290+ files
│   ├── web/            # Next.js 16 — 216 pages, 19 UI components
│   ├── docs/           # Docusaurus 3.9
│   ├── odovation/      # Brand site
│   └── muhammadamir/   # Personal brand
├── packages/
│   ├── db/             # Drizzle ORM — 60 schemas, 150+ tables
│   ├── types/          # Shared TypeScript types
│   ├── validators/     # Zod validation schemas
│   ├── utils/          # Shared utilities
│   ├── email-templates/# React Email components
│   ├── ui/             # Shared UI components
│   └── config/         # Shared configuration
└── infrastructure/
    ├── docker-compose.dev.yml
    ├── docker-compose.prod.yml
    └── nginx/
```

### Backend Modules (55 NestJS Modules)

**Core**: auth, users, admin, products, billing, licenses, downloads, entitlements, api-keys, email, notifications, support, ai, ecosire-ai, contacts

**Finance**: accounting, sales, purchase

**Operations**: inventory, manufacturing, pos, rental

**HR**: hr, recruitment, time-off, attendance, expense, payroll, appraisal

**Services**: helpdesk, planning, approvals, timesheets

**Productivity**: documents, knowledge

**Marketing**: email-marketing, events, marketing-automation

**Growth Engine**: workflows, scoring, contact-events, sms, whatsapp, conversations, funnels, pages, checkout, calendar, chat, affiliates, loyalty

**Other**: crm, projects, analytics

### Frontend Pages (246 Total)

- **76 Public Pages**: Homepage, about, pricing, blog (205 posts), products, integrations, comparisons, solutions, glossary, support, terms, checkout, auth
- **170 Admin Pages**: Full Odoo 19 Enterprise suite + Growth Engine with CRUD, filters, data tables

### Internationalization (11 Locales)

English, Chinese (Simplified), Spanish, Arabic, Portuguese, French, German, Japanese, Turkish, Hindi, Urdu — 6,620+ translation keys, 100% coverage

---

## Odoo Development Stack

### Core Platform

| Component | Technology |
|-----------|-----------|
| **ERP** | Odoo v16, v17, v18, v19 (Community & Enterprise) |
| **Backend** | Python 3.x |
| **Frontend** | JavaScript, Owl framework |
| **Database** | PostgreSQL @ localhost:5432, user `openpg` |
| **eCommerce** | 201 marketplace connectors + 14 additional products |

### Development Servers

| Version | Path | Port | Database |
|---------|------|------|----------|
| Odoo 19 | `D:\Development\odoo19\server\` | 8072 | odoo19 |
| Odoo 18 | `D:\Development\odoo18\server\` | 8069 | db manager |
| Odoo 17 | `D:\Development\odoo17\server\` | 8070 | db manager |

### Module Architecture (Per Connector)

```
module_name/
├── __manifest__.py          # Metadata, pricing, dependencies
├── models/
│   ├── configuration.py     # API auth (SP-API, OAuth, REST)
│   └── sync_service.py      # Bidirectional sync logic
├── controllers/main.py      # Dashboard API endpoint
├── tools.py                 # REST/OAuth client
├── wizard/                  # Setup wizards
├── views/                   # Configuration, logs, dashboard XMLs
├── security/                # Access control (XML + CSV)
├── data/cron_data.xml       # Automated sync jobs
├── tests/                   # 14 test files per module
├── reports/                 # Sync reports
└── static/
    ├── description/         # App Store page (HTML + screenshots)
    └── src/                 # OWL dashboard (Chart.js, dark mode)
```

### Dashboard Features (All 201 Connectors)

- KPI Cards: Revenue, Orders, Customers, Products, AOV, Sync Health
- Charts: Line (Sales Trend) + Doughnut (Order Status) via Chart.js
- Period Filters: Today, 7D, 30D, 90D, Year, Custom
- Activity Feed, Quick Actions, Auto-Refresh (60s), Dark Mode, Responsive

---

## DevOps & Automation Scripts

### Location: `D:\Development\scripts\`

| Category | Scripts | Purpose |
|----------|---------|---------|
| **Backporting** | backport_to_v18.py, backport_to_v17.py | Automated v19→v18→v17 transformations |
| **Publishing** | publish_all_modules_v3.py | Publish all 201 modules to GitHub |
| **Generation** | generate_new_modules.py | Clone base module to create new platforms |
| **Testing** | run_all_tests.py, validate_store_modules.py | Test orchestration & validation |
| **Fixes** | 40+ specialized fix scripts | M2M, casing, security, config fixes |

---

## Infrastructure Services

### Docker Services

| Service | Port | Purpose |
|---------|------|---------|
| n8n | 5678 | Workflow automation |
| MiniIO | 9000/9001 | S3-compatible object storage |
| NCA Toolkit | — | Dependent on MiniIO |
| PostgreSQL | 5432 | Database |
| Redis | 6379 | Caching |
| Authentik | 9000 | Authentication (OIDC) |
| Mailpit | 8025 | Dev email testing |

### Cloud & Hosting
- **AWS EC2** — Production servers
- **Cloudflare** — DNS, CDN, SSL
- **Odoo.sh** — Client Odoo hosting
- **Dropbox** — File sync & backup
- **Google Workspace** — Drive, Docs, Sheets

---

## AI Agent Fleet (v10.0)

The Odoo development workspace runs a **27-agent AI fleet** powered by Claude Opus:

| Metric | Value |
|--------|-------|
| Agents | 27 (all Claude Opus) |
| Skills | 99 reusable skills |
| Teams | 16 orchestrated teams |
| Hooks | 9 (7 actively wired) |
| Rules | 5 enforcement rules |

**Key Agents:** module-developer, backporter, publisher, error-doctor, security-auditor, test-runner, browser-tester, quality-tester, frontend-designer, documentation-writer, odoo-engineer, database-engineer, performance-auditor, code-architect, codebase-explorer, sales-proposal, accountant, client-ops

**Key Commands:**
- `/quality-gate {module} --level quick|standard|full` — Comprehensive validation
- `/publish-module all` — Publish all modules to GitHub
- New module: `/new-module` or Full Development team
- Error diagnosis: `/error-diagnosis`

**Self-Evolving Features:** Claims system, anti-drift checks, intelligence loop, injection detection, governance gates, autopilot v2.0

---

## Competitor Intelligence

- **69GB Module Library** at `D:\OdooModules\` — 5,500+ competitor modules (v12-v19)
- **Active Study** at `D:\Development\competitorsreferencemodules\`
- **Platform API Intelligence**: 63 platforms, ~6,500 endpoints catalogued at `D:\Development\platform-api-intelligence\`

---

## AI & Automation

- **OpenClaw** -- AI personal assistant framework
  - Multi-channel: WhatsApp, Telegram, Slack, Discord, Google Chat, Signal, Teams
  - Voice wake + Talk Mode, Live Canvas, Skills system
  - Node.js ≥22 runtime
- **n8n** — Workflow automation and integration
- **Ecosire AI** — Content engine, NLP ERP queries
- **Claude Code** — AI-assisted development (27 agents, 99 skills)

---

## Key Documentation Files

| File | Path | Lines |
|------|------|-------|
| Development CLAUDE.md | `D:\Development\CLAUDE.md` | ~650K |
| Platform CLAUDE.md | `D:\ECOSIRE.COM\CLAUDE.md` | 18,185 |
| Sales/Marketing Plan | `D:\ECOSIRE.COM\SALES_MARKETING_MASTER_PLAN.md` | 93,599 |
| Platform README | `D:\ECOSIRE.COM\README.md` | 319 |
| Marketplace Strategy | `D:\R&D\Readme.md` | — |

---

## Related

- [[Ecosire Private Limited]] — Company profile
- [[Ecosire - Odoo Module Library]] — All 201+ modules
- [[ECOSIRE.COM Platform]] — Platform deep-dive
- [[MOC - Odoo Expertise]] — Technical knowledge
