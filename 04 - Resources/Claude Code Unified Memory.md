---
type: resource
tags: [claude-code, memory, unified, development, reference]
created: 2026-03-02
updated: 2026-04-22
---

# Claude Code Unified Memory

> **2026-04-22 update:** For the current **project/workspace map** (who lives where, what each workspace does, which sibling touches which), read [[MOC - Hive Mind]] instead — it's kept fresh. This note still holds the older **consolidated technical patterns and gotchas** (ECOSIRE.COM, Odoo, AI Content Engine, servers, Odoo internals), which are still useful but not guaranteed current past March 2026. When a pattern conflicts with reality, trust the per-project CLAUDE.md.

> Consolidated knowledge from all Claude Code project workspaces across this machine.
> This note prevents knowledge loss by backing up all project-specific learnings, patterns,
> gotchas, and configurations into a single Obsidian reference.

---

## Source Projects (11 workspaces)

| # | Project Path | Memory Files | Key Domain |
|---|-------------|-------------|------------|
| 1 | `C:\Users\Amir Nazir\Dropbox\` (this vault) | MEMORY.md | OpenClaw agents, vault conventions |
| 2 | `D:\Ai-Content-Automation-Engine\` | MEMORY.md | AI Content Engine SaaS specs |
| 3 | `D:\Development\` | MEMORY.md + 3 topic files | Odoo dev, 33 modules, version diffs |
| 4 | `D:\ECOSIRE.COM\` | MEMORY.md + 3 topic files | ECOSIRE.COM platform, i18n, SEO |
| 5 | `D:\Ecosire Customer Service\...\Ai Content Automation\` | MEMORY.md + 4 topic files | AI Content Automation Engine (live) |
| 6 | `D:\Ecosire Customer Service\...\Sahara Properties\` | MEMORY.md | Sahara Properties Odoo 19 |
| 7 | `D:\Development\odoo19\server\addons\` | MEMORY.md | Odoo 19 addons conventions |
| 8 | `C:\Users\Amir Nazir\` (home) | (empty) | — |

**Claude Code memory base:** `C:\Users\Amir Nazir\.claude\projects\{path-slug}\memory\`

---

## Cross-Links to Existing Vault Notes

These vault notes already contain related knowledge:

- [[ECOSIRE.COM Platform]] — Platform architecture, modules, i18n
- [[OpenClaw Agent Strategy]] — 10 agents, 33 skills, cron jobs
- [[Ecosire - Production Servers]] — 3 production servers
- [[Ecosire - Technical Stack]] — Full tech stack overview
- [[Ecosire - Odoo Module Library]] — 42 Odoo modules
- [[MOC - Command Cheatsheets]] — Server management commands
- [[MOC - Odoo Expertise]] — Odoo development knowledge

---

## Claude Code Agent Teams Automation (2026-03-05)

### GitHub Backup
- **Repo:** https://github.com/engrmuhammadamirnazir/claude.git (PRIVATE)
- **What:** Full backup of `C:\Users\Amir Nazir\.claude\` (memories, agents, skills, sessions, settings)
- **Auto-backup:** Windows Task Scheduler runs daily at 11:59 PM via `backup-daily.sh`
- **Purpose:** Shared knowledge across multiple laptops, disaster recovery

### Agent Team Setup (D:\Development\.claude\)
- **22 custom agents** (ALL Opus model) with self-evolving memory
- **73 custom skills** (slash commands)
- **5 path-scoped rules**, **9 team compositions**, **8+ shared knowledge files**

| Agent | Model | Purpose |
|-------|-------|---------|
| module-developer | Opus | Creates new Odoo modules |
| backporter | Sonnet | Backports v19 to v18/v17 |
| module-validator | Sonnet | Validates module quality |
| test-runner | Haiku | Runs and analyzes tests |
| publisher | Sonnet | Publishes to GitHub |
| opportunity-scout | Sonnet | Market research |

### Self-Evolving Memory System
- Each agent reads memory BEFORE starting + writes learnings AFTER completing
- **Project memory:** `D:\Development\.claude\agent-memory\{agent-name}\MEMORY.md`
- **Shared knowledge:** `D:\Development\.claude\agent-memory\shared-knowledge\KNOWLEDGE.md`
- Categories: common issues, module patterns, version gotchas, publishing lessons, test failures, market intel

### Key Skill Commands
| Command | Purpose |
|---------|---------|
| `/new-module [platform]` | Create complete new store module |
| `/backport-module [name]` | Backport to v18 + v17 |
| `/validate-module [name]` | Validate across versions |
| `/full-pipeline [platform]` | Complete create+backport+validate+test+publish |
| `/setup-agent-teams [path]` | Generate agent teams for ANY project |

---
## 1. System Layout

### Machine & Drives
- **OS:** Windows 11 Pro (10.0.22631)
- **C: drive (200GB):** User profile, Dropbox vault, OpenClaw workspace, Claude Code config
- **D: drive (275GB):** All development — Odoo, ECOSIRE.COM, client projects, module archives
- **Python:** `C:/Users/Amir Nazir/AppData/Local/Programs/Python/Python312/python.exe`
- **Node.js:** `C:/Program Files/nodejs/node.exe`
- **Shell:** Git Bash (use Unix syntax, not Windows)
- **GitHub org:** `github.com/ecosire` (50+ repos)
- **Personal GitHub:** `github.com/engrmuhammadamirnazir`

### Key Directories

| What | Path |
|------|------|
| Unity (Obsidian hive brain) | `C:\Users\Amir Nazir\Dropbox\Unity\` (renamed from "Muhammad Amir Obsidian Vault" 2026-04-22) |
| OpenClaw config | `C:\Users\Amir Nazir\.openclaw\openclaw.json` |
| OpenClaw workspace | `C:\Users\Amir Nazir\.openclaw\workspace\` |
| OpenClaw source | `D:\OpenClaw\openclaw\` |
| Claude Code memories | `C:\Users\Amir Nazir\.claude\projects\` |
| Odoo 19 dev | `D:\Development\odoo19\server\addons\` |
| Odoo 18 dev | `D:\Development\odoo18\server\addons\` |
| Odoo 17 dev | `D:\Development\odoo17\server\addons\` |
| Publishing folder | `D:\Development\Ecosire Solutions\{Display Name}\{module}\` |
| ECOSIRE.COM monorepo | `D:\ECOSIRE.COM\` |
| AI Content Engine | `D:\Ecosire Customer Service\Active Clients\Ai Content Automation\` |
| Sahara Properties | In vault + D: drive |
| Module archives | `D:\Odoo Modules\{12-19}\` |
| Client projects | `D:\Ecosire Customer Service\Active Clients\` |

---

## 2. OpenClaw Agent Swarm

See [[OpenClaw Agent Strategy]] for full details. Key memory:

### Status (2026-03-02)
- **10 agents**, **33 skills** (~22K lines), **66 cron jobs**
- Gateway: `ws://127.0.0.1:18789`, token in openclaw.json
- Gateway startup: `OPENCLAW_GATEWAY_PORT=18789 OPENCLAW_GATEWAY_TOKEN={token} "C:/Program Files/nodejs/node.exe" "D:/OpenClaw/openclaw/dist/index.js" gateway --port 18789`

### Agents

| Agent | ID | Skills | Role |
|-------|----|--------|------|
| Zain Shahzad | main | 12 | Chief of Staff, coordinator |
| Hamza Ali | hamza | 15 | Senior Odoo Developer |
| Saad Raza | saad | 18 | Business Development |
| Hira Nawaz | hira | 14 | Content & Community |
| Kashif Omar | kashif | 13 | Operations Analyst (internal) |
| Waqar Ahmed | waqar | 10 | QA & Security (internal) |
| Bilal Farooq | bilal | 14 | Accounting & Bookkeeping |
| Fatima Malik | fatima | 15 | Shopify eCommerce |
| Usman Tariq | usman | 17 | GoHighLevel CRM |
| Nadia Hassan | nadia | 16 | AI Agent Services |

### Skill Categories
- **15 original skills** (company-knowledge, confidentiality-protocol, lead-qualification-bant, etc.)
- **6 domain skills** (accounting-operations, shopify-ecommerce, gohighlevel-crm, openclaw-agent-services, obsidian-second-brain, claude-code-development)
- **3 universal skills** (ALL 10 agents): pmp-project-management, professional-sdlc, information-security-ops
- **9 certification skills** (1 per agent): odoo-certified-developer (Hamza), certified-sales-professional (Saad), digital-marketing-certified (Hira), business-analytics-certified (Kashif), qa-security-certified (Waqar), cpa-accounting-certified (Bilal), shopify-partner-certified (Fatima), crm-automation-certified (Usman), ai-solutions-architect (Nadia)

### Known Issues
- Discord not configured
- Telegram blocked by ISP
- 5/6 Moltbook accounts registered; 4 new agent accounts NOT YET REGISTERED
- `claude-code-development` enhanced with Opus model preference (`claude-opus-4-6`), TDD, ADRs

---

## 3. ECOSIRE.COM Platform

See [[ECOSIRE.COM Platform]] for architecture. Key memory:

### Scale (2026-03-09)
- **Monorepo:** Turborepo 2.8 + pnpm 10.28
- **NestJS 11:** 55 backend modules, 290+ TypeScript files
- **Next.js 16:** 246 pages (170 admin + 76 public), 205 blog posts
- **DB:** PostgreSQL 17, 60 Drizzle schema files, 150+ tables
- **Auth:** Authentik OIDC → JWT (15m) + refresh (7d) → HttpOnly cookies
- **i18n:** 11 locales (en, zh, es, ar, pt, fr, de, ja, tr, hi, ur), 8,991 translation keys
- **5 apps:** ecosire-web (3000), ecosire-api (3001), ecosire-docs (3002), odovation (3010), muhammadamir (3020)
- **5 service platforms:** Odoo, Shopify, OpenClaw, GoHighLevel, Accounting
- **Growth Engine:** 11 apps (workflows, sms, whatsapp, inbox, funnels, pages, checkout, calendar, chat, affiliates, loyalty)

### Critical Patterns
- Workspace packages must be **pre-built to CommonJS** via tsc before NestJS can consume them
- `@ecosire/db` uses **lazy Proxy** — no eager DB connections at module load
- `forRoutes('*path')` not `forRoutes('*')` for NestJS 11 middleware
- **NEVER use `sql.raw()`** — always parameterized `sql` template literals
- **Next.js 16:** `proxy.ts` replaces `middleware.ts`
- **Turbopack** is default bundler in Next.js 16 — no flag needed
- en.json keys MUST use **nested** structure, NOT flat dot-separated (breaks next-intl)
- ALL pages use `generateMetadata()` (NOT static metadata) for locale-aware hreflang
- lucide-react: NEVER `import *` — use explicit named imports + icon map
- NEVER pass React component refs as props from server to client components

### Production Server
- **EC2:** t3.large, Ubuntu 24.04, IP: 13.223.116.181
- **SSH:** `ssh -i ecosire.pem ubuntu@13.223.116.181` (user is `ubuntu`, NOT `ecosire`)
- **App root:** `/opt/ecosire/app` (NOT /home/ubuntu)
- **Deploy:** `git pull → pnpm install --frozen-lockfile → npx turbo build → drizzle-kit push → pm2 restart all`
- **SSH from Git Bash:** Always `powershell -Command "ssh ... 2>&1 | Out-String"`
- **www redirect:** Cloudflare Redirect Rule (NEVER Nginx — causes loops)
- Cloudflare SSL: Full (Strict), NEVER split server blocks

### Authentik Auth (ecosire.com)
- **Admin:** `akadmin` / `engr.mohammadamirnazir@gmail.com` at `auth.ecosire.com`
- **OAuth2:** pk=1, client_id=`ecosire-web`, confidential
- Secrets set via API may have hashing mismatch — regenerate via Django ORM
- API tokens: `cat script.py | docker exec -i ecosire-authentik /lifecycle/ak shell`
- **Brand:** Light theme via custom.css, `branding_custom_css` via Django ORM (file mount doesn't inject CSS)

### Security Hardening
- HttpOnly cookies (`ecosire_auth` + `ecosire_refresh`), tokens never in URL/localStorage
- SSRF protection in `ecosire-ai.service.ts` — blocks private IPs, requires HTTPS in prod
- DOMPurify for admin AI content HTML
- CSP: `unsafe-eval` removed from script-src
- Rate limits: support/tickets (5/min), crm/capture (10/min), ai/ask (20/min)
- Nginx: X-Frame-Options DENY, X-Content-Type-Options nosniff, Permissions-Policy on all blocks

### SEO / AEO / GEO
- 21 AI crawlers explicitly allowed in robots.txt
- IndexNow: Bing, Yandex, Naver, Seznam — key: `99276d2a741841a48f7dbb76313bf9d0`
- JSON-LD: 86 schemas across 43 pages (Organization, WebSite, FAQPage, Product, BreadcrumbList, Service)
- LLM files: `/llms.txt`, `/llms-full.txt`, `/.well-known/llms.txt`
- Multilingual sitemap with `localizedEntry()` helper for all 11 locales

### MuhammadAmir.com
- **URL:** https://muhammadamir.com — personal brand site
- **App:** `apps/muhammadamir/` (Next.js 16, port 3020)
- **Scale:** 41 pages, 12 services, 8 projects, 8 blog posts, 7 testimonials
- **Animation:** motion v12 (FadeIn, StaggerContainer, AnimatedCounter)
- **Certifications:** Odoo V15 Functional (#0000236179) + V16 Functional (#0000236181)
- No `generateStaticParams` on detail pages — conflicts with next-intl

### Product Pricing Update Process
When updating product prices, ALL 5 sources must be checked:
1. `packages/db/src/seeds/products.ts` — DB seed (canonical)
2. `apps/web/src/data/integrations.ts` — frontend plans + "Starting Price" stat
3. `apps/web/messages/{locale}.json` — translation files
4. Blog MDX content (informational)
5. **LIVE DATABASE** — seed file does NOT auto-update! Must run SQL UPDATE via docker exec

---

## 4. Odoo Development

### Environment

| Version | Path | Port | DB |
|---------|------|------|-----|
| Odoo 19 (primary) | `D:\Development\odoo19\server\` | 8072 | odoo19 |
| Odoo 18 | `D:\Development\odoo18\server\` | 8069 | (db manager) |
| Odoo 17 | `D:\Development\odoo17\server\` | 8070 | — |

- **PostgreSQL:** localhost:5432, user: openpg, pass: g4xkLPLtD=mN
- **Workflow:** Develop in odoo19 → finalize → push to Ecosire Solutions → GitHub → App Store
- **Git:** Each module has own repo at `github.com/ecosire/{module_name}` with version branches (19.0, 18.0, 17.0)
- **Git Rule:** `main` branch ALWAYS mirrors latest version branch (currently 19.0)

### Store Management Modules (33 total)
- All 33 validated (Python, XML, CSV, manifests pass)
- All have platform-specific API auth, OWL dashboards (Chart.js, 6 KPIs), active license enforcement
- All exist across v19, v18, v17 with Ecosire Solutions copies
- All published to GitHub across 19.0, 18.0, 17.0, main branches
- Chart.js bundled locally at `static/lib/chart.umd.min.js` (not CDN)
- Dashboard CSS: unique 2-letter prefix per module (am_, eb_, tk_, etc.)
- 4 modules lack test suites: prestashop, shopee, tiktok, wix
- 29 modules have test suites (15 files each)

### ECOSIRE Branding Standard
- All manifests: author/company/maintainer = 'ECOSIRE', website = 'https://www.ecosire.com'
- Always use `www.ecosire.com` (with www)
- License: LGPL-3 on all modules
- Pricing: $99 (15), $149 (11), $199 (4), $399 (2), $499 (Amazon), $499 (SaaS Business Management)

### Odoo Version Differences (19 vs 18 vs 17)

| Feature | Odoo 19 | Odoo 18 | Odoo 17 |
|---------|---------|---------|---------|
| Constraints | `models.Constraint(...)` | `_sql_constraints = [...]` | `_sql_constraints = [...]` |
| List view tag | `<list>` | `<list>` | `<tree>` |
| view_mode | `list,form` | `list,form` | `tree,form` |
| Controller JSON | `type='jsonrpc'` | `type='json'` | `type='json'` |
| `_inherit` format | List: `['model']` | String OK: `'model'` | String OK: `'model'` |
| `group_operator` | `aggregator="sum"` | `aggregator="sum"` | `group_operator="sum"` |
| Kanban card | `<card>` | `<card>` | `<t t-name="kanban-box">` |
| Settings views | `<app>/<block>/<setting>` | `<app>/<block>/<setting>` | `<div class="app_settings_block">` |
| OWL | 2.x | 2.x | 2.x |
| `name_get()` | Deprecated → `_compute_display_name()` | Same | Still standard |
| `read_group()` | Deprecated → `_read_group()` | Same | Still standard |

### Backporting 19→18
1. `models.Constraint(...)` → `_sql_constraints = [(...)]`
2. `type='jsonrpc'` → `type='json'`
3. `_inherit = ['model']` → `_inherit = 'model'`
4. Remove `@api.private` decorators
5. `from odoo.fields import Domain` → `from odoo.osv import expression`
6. `import xlsxwriter` → `from odoo.tools.misc import xlsxwriter`
7. Update manifest version `19.0.x` → `18.0.x`

### Backporting 18→17
1. `<list>` → `<tree>` in XML
2. `view_mode: list,form` → `tree,form` in BOTH XML and Python
3. `aggregator=` → `group_operator=`
4. `<card>` → `<t t-name="kanban-box">`
5. `<app>/<block>/<setting>` → `<div class="app_settings_block">`
6. Update manifest version `18.0.x` → `17.0.x`

### Odoo 19 Breaking Changes (DON'T REPEAT)
- `name_get()` REMOVED → use `_compute_display_name()` with `@api.depends`
- `category_id` REMOVED from `res.groups` → use `res.groups.privilege`
- `.o_action_manager` clips overflow → `overflow-y: auto !important` in SCSS
- Use `min-height: 100%` NOT `100vh` for dashboard
- XML data file load order matters → tiered manifest ordering
- `res.partner.mobile` removed → use `phone`
- `_apply_ir_rules()` removed → use `self._search(domain, bypass_access=True)`

### Odoo App Store HTML Rules
- `<head>`, `<style>`, `<script>` tags ALL stripped — use inline styles only
- No external fonts — use system fonts
- No CSS variables, no animations
- Images as relative paths from `static/description/`

### License Enforcement
- All 33 modules connected via `ecosire_license_client` mixin + `@require_license` decorators
- Enforcement on config/sync/cron methods
- SaaS Business Management has CRUD protection (`_ecosire_protect_create = True`)

---

## 5. AI Content Automation Engine

### Overview
- **URL:** https://ecosire.ai (dashboard + API)
- **Server:** 54.197.92.23 (AWS EC2 t3.large, us-east-1)
- **PEM:** `ecosire.pem` (project root, NEVER commit)
- **Project path on server:** `/opt/ace/ai-content-engine/ai-content-engine/`
- **11 Docker containers** (postgres, redis, api, celery-worker, celery-beat, dashboard, nginx, authentik x4)

### Tech Stack
- **Backend:** Python 3.12+ / FastAPI / SQLAlchemy 2.0 (async) / Celery 5 / Redis 7
- **Frontend:** Next.js 16 / shadcn/ui / Tailwind v4 / TanStack Query v5
- **AI:** Claude (Sonnet/Opus/Haiku) primary, GPT-4o secondary
- **DB:** PostgreSQL 16, 23 tables, migration 0011
- **Auth:** Authentik OIDC, next-auth 5.0.0-beta.30
- **Tests:** 1,517 passing (pytest-asyncio, asyncio_mode="auto")

### Three Operational Modes
- **A:** Competitor Intelligence
- **B:** SEO Precision
- **C:** Volume Scaling

### Key Features
1. **Autonomous Site Scanner** — crawl → 7 analyzers → strategy → execute → monitor
2. **Agent Runtime** — declarative AgentDefinition configs, 21 tools, 6 profiles, 8 plugins, budget tracking
3. **WordPress Plugin v2.0.8** — React 19 + PHP, 8 pages, self-update system, clean uninstall
4. **Content Hub** — campaign management, article pipeline, keyword tracking
5. **Growth Strategy** — prioritized actions with traffic lift projections

### Scanner Architecture
- **7 Analyzers:** content, technical, authority, schema, CRO, quality, keyword
- **All import thresholds from:** `agents/runtime/knowledge/seo_knowledge.py` (single source of truth)
- **Celery tasks:** crawl → parallel analysis → finalize → strategy → execute
- **Weekly rescan:** Monday 3AM UTC

### Agent Runtime Architecture
```
AgentDefinition → AgentRuntime → BudgetTracker + ToolRegistry + HookEngine
                                + MemoryManager + SubagentCoordinator + WorkflowEngine
```
- 6 agent definitions (content, technical, authority, geoaeo, cro, quality)
- Feature flag: `USE_AGENT_RUNTIME=False` (default)
- Budget degradation at 80% switches to cheaper model

### WordPress Plugin Key Patterns
- PHP REST proxy keeps API tokens server-side; React only calls WP REST endpoints
- CSS isolation: `#ecosire-ai-root` with Tailwind `eco-` prefix
- Auth: API token sent as `X-API-Key` header (NOT `Authorization: Bearer`)
- Paginated responses: `{items: [...]}` format — use `extractArray<T>()` helper
- Auto-updater checks `GET /plugin/info` every 12h
- PHP 8.0 minimum (graceful deactivation on older)
- Version bump: 4 files (ecosire-ai.php header, ECOSIRE_VERSION constant, readme.txt, backend plugin.py)

### Authentik Auth (ecosire.org)
- Admin: `https://auth.ecosire.ai/if/admin/`
- Application: `ai-content-engine`, OIDC Issuer: `https://auth.ecosire.ai/application/o/ai-content-engine/`
- **CRITICAL:** `PyJWKClient` MUST have custom User-Agent — Cloudflare blocks default `Python-urllib/3.12`
- API + Authentik on different Docker networks — use external URL for JWKS

### Key Technical Patterns
- All backend code fully async (asyncpg, AsyncClient)
- `tenacity` for retry with exponential backoff
- Models use UUID primary keys + created_at/updated_at
- `pydantic[email]` required (not just `pydantic`)
- All StrEnum values UPPERCASE (`PlanTier.FREE = "FREE"`)
- Celery tasks use `run_async()` helper to bridge sync↔async
- API tests use in-memory SQLite via `aiosqlite`
- WordPressSite test fixtures require `api_username` + `api_password_encrypted=b"encrypted"`

---

## 6. Sahara Properties (Client Project)

### Server
- **IP:** 3.232.201.222, SSH: bitnami, PEM: `sahara.pem`
- **Bitnami Odoo 19** — 8GB RAM, 60GB Disk
- **Domain:** saharaproperties.ecosire.com
- **Custom addons:** `/bitnami/odoo/addons` (NOT core addons path)
- **DB:** bitnami_odoo, user: bn_odoo
- **Python pip:** `/opt/bitnami/odoo/venv/bin/pip` (NEVER system pip)

### Custom Module: Property Management System
- **Single module:** `property_management_system`
- **Features:** Electricity recovery, security deposits, analytic profitability, PDC import, bounced cheques, 3-tier approval
- **Cost:** $500 (part of $1,500 Phase 1)
- **Client:** 100+ tenants, 2 accountants + 1 Financial Manager

### Custom Module: Private Enterprise
- **Single module:** `private_enterprise` — privacy/firewall for Odoo Enterprise
- **Three-layer HTTP interception:** requests.Session monkey-patch → AbstractModel override → IAP monkey-patch
- Blocks 40+ Odoo phone-home domains
- `post_init_hook`: 100yr expiration, disable warranty cron
- `uninstall_hook`: restores everything

### Server Gotchas
- `unzip` NOT pre-installed on Bitnami → `sudo apt-get install -y unzip`
- Custom modules in `/bitnami/odoo/addons/` NEVER core addons
- Python deps: ALWAYS use venv pip
- `log_level = warn` default → use `_logger.warning()` for important messages

---

## 7. Production Servers Summary

| Server | IP | Purpose | SSH Key | Project Path |
|--------|----|---------|---------|-------------|
| ECOSIRE.COM | 13.223.116.181 | Platform (5 PM2 apps + Docker) | ecosire.pem | `/opt/ecosire/app` |
| ECOSIRE.AI | 54.197.92.23 | AI Engine (11 Docker containers) | ecosire.pem | `/opt/ace/ai-content-engine/ai-content-engine/` |
| Sahara Properties | 3.232.201.222 | Client Odoo 19 (Bitnami) | sahara.pem | `/bitnami/odoo/addons/` |
| Oenoteca | 3.20.73.143 | Client Odoo 19 (Bitnami) | ustelekomecosire.pem | `/opt/bitnami/odoo/` |

See [[Ecosire - Production Servers]] for additional servers and full SSH key inventory.

---

## 8. Common Gotchas & Debugging

### Windows/Bash
- PowerShell env vars in bash: `Set-Item -Path Env:VAR -Value 'val'`
- SSH from Git Bash: always `powershell -Command "ssh ... 2>&1 | Out-String"`
- PowerShell `$_` gets escaped by bash — use alternate approaches
- Parentheses in SSH commands eaten by PowerShell→SSH→bash layers — use base64 piping

### Docker/Cloudflare
- **Cloudflare blocks default Python User-Agent** — ANY Python HTTP client through Cloudflare MUST set custom User-Agent
- **After container recreation:** always `nginx -s reload` (upstream DNS cache)
- **API and Authentik on different Docker networks** — cannot use Docker DNS, must use external URL

### Odoo
- `name_get()` removed in Odoo 19 → `_compute_display_name()`
- `category_id` removed from `res.groups` → `res.groups.privilege`
- `_inherit = 'model'` (string) only works in v18/v17, must be `['model']` (list) in v19
- Controller override: inherit from actual class (e.g., `Session`), NOT generic `http.Controller`
- AbstractModel override via `_inherit` = auto-cleaned on uninstall (safest pattern)

### ECOSIRE.COM
- `seed file ≠ live database` — updating products.ts does NOT change production DB
- `drizzle-kit push` only pushes schema, not data — must run SQL UPDATE for price changes
- `NEXT_PUBLIC_*` vars inlined at build time — must be in env during build
- `'use client'` + `generateStaticParams` conflicts with next-intl

### OpenClaw
- Gateway startup requires env vars: OPENCLAW_GATEWAY_PORT, OPENCLAW_GATEWAY_TOKEN
- `openclaw` CLI not always in bash PATH — use full paths
- `openclaw.json` updates: use Python atomic read-modify-write to avoid race conditions

---

## 9. Key Deployment Commands

### ECOSIRE.COM
```bash
# Deploy (from Windows)
cd "D:/ECOSIRE.COM" && git push origin main
powershell -Command "ssh -i 'D:\ECOSIRE.COM\ecosire.pem' ubuntu@13.223.116.181 'cd /opt/ecosire/app && git pull origin main && pnpm install --frozen-lockfile && npx turbo build && pm2 restart all' 2>&1 | Out-String"

# With schema changes (add before pm2 restart):
# export DATABASE_URL=postgresql://ecosire:{pass}@localhost:5432/ecosire && cd /opt/ecosire/app/packages/db && npx drizzle-kit push
```

### AI Content Engine
```bash
PEM="D:\Ecosire Customer Service\Active Clients\Ai Content Automation\aicontentgeneration.pem"
ssh -i "ecosire.pem" ubuntu@54.197.92.23
# On server:
cd /opt/ace && git pull
cd ai-content-engine/docker
docker compose --env-file ../.env -f docker-compose.prod.yml build api dashboard celery-worker celery-beat
docker compose --env-file ../.env -f docker-compose.prod.yml up -d
docker compose --env-file ../.env -f docker-compose.prod.yml exec api alembic upgrade head
docker exec ace-nginx nginx -s reload
```

### Sahara Properties (Bitnami Odoo 19)
```bash
# Services
sudo /opt/bitnami/ctlscript.sh {status|start|stop|restart}
# Custom addons go in /bitnami/odoo/addons/
# Python deps: sudo /opt/bitnami/odoo/venv/bin/pip install <package>
```

### OpenClaw Gateway
```bash
OPENCLAW_GATEWAY_PORT=18789 OPENCLAW_GATEWAY_TOKEN={token} \
  "C:/Program Files/nodejs/node.exe" \
  "D:/OpenClaw/openclaw/dist/index.js" \
  gateway --port 18789
```

---

## 10. Odoo Internal Architecture (Private Enterprise Knowledge)

### Phone-Home Domains (40+ blocked by Private Enterprise)
- **Core:** services.odoo.com, accounts.odoo.com, www.odoo.com, nightly.odoo.com, redirect.odoo.com, upgrade.odoo.com
- **IAP:** iap.odoo.com, iap-sandbox.odoo.com, iap-services.odoo.com
- **API Services:** social.api, gmail.api, outlook.api, sms.api, website.api (.odoo.com)
- **Partner/GeoIP/IoT:** partner-autocomplete, geoip, iot-proxy (.odoo.com)
- **Banking:** odoofin.com (production, sandbox)
- **L10n EDI (20+):** l10n-ar-afip, l10n-br, l10n-cl-edi, l10n-mx-edi, etc. (.api.odoo.com)

### Config Parameters (Enterprise)
- `database.expiration_date` — checked by subscription UI
- `database.expiration_reason` — 'trial'|'demo'|'valid'|false
- `database.enterprise_code` — subscription code
- `database.uuid` — sent in most phone-home calls

### Enterprise Subscription JS
- File: `web_enterprise/static/src/webclient/home_menu/enterprise_subscription_service.js`
- `buy()` → redirect to odoo.com/enterprise/upgrade
- `renew()` → redirect to odoo.com/enterprise/renew
- `ExpiredSubscriptionBlockUI` blocks entire UI when daysLeft <= 0

---

## Maintenance

**Update this note when:**
- New projects are started or completed
- New servers are provisioned
- Major architectural changes occur
- New gotchas are discovered
- Claude Code memory files are updated with significant new knowledge

**Last full sync:** 2026-03-09 (all 11 project memories consolidated)
