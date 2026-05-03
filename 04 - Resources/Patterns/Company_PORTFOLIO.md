# ECOSIRE Holding — Business Portfolio

> Canonical, top-level view of every business operated under ECOSIRE Holdings. Single source of truth for: what each business is, who owns it (legal entity), status (Active / Pre-launch / Paused), brand, target audience, and pending items. Last revised: 2026-05-04.

Every agent in any D:/ workspace MUST read this when writing proposals, drafting outreach, or making strategic decisions, so cross-business context is preserved (e.g., a proposal to a B2B retailer should consider Odoo Modules + ECOSIRE.COM SaaS + ADNET advertising; a proposal to a content publisher should consider ECOSIRE.AI + ADNET).

---

## Legal entities (the two holding companies)

| Entity | Jurisdiction | Status | Use for |
|--------|--------------|--------|---------|
| **ECOSIRE (PRIVATE) LIMITED** | Pakistan (SECP-registered, NTN-tax-registered) | Active | Default for Asia / EU / MENA / Africa clients |
| **ECOSIRE LLC** | Wyoming, USA | Active | Default for US / Canada / LatAm clients |

Brand assets (single canonical letterhead): `D:/Company/ECOSIRE (PRIVATE) LIMITED/05 - Brand & Identity/Letterhead/letterhead_full.png`. Same letterhead is used by both entities; the difference is invoice line + bank details. Full legal vault at `D:/Company/`.

---

## Business lines (7 total)

### 1. Odoo Module Suite + Custom Implementation
- **Workspace:** `D:/Development/`
- **Status:** **Active** — primary revenue line
- **Brand:** ECOSIRE Solutions (App Store)
- **Audience:** Odoo end-users worldwide, Odoo partners, store owners (Shopify/eBay/Amazon/Walmart/etc.)
- **Offering:** 215+ Odoo modules across v17/v18/v19 (App Store catalog) + custom Odoo implementation services + support plans
- **Pricing:** App Store modules at $249 / $349 / $499 / $599 (vinculum). Implementation projects custom-quoted (typical $2K–$15K).
- **Repo:** github.com/ecosire/<module-name> (one repo per module, all public except internal tooling)
- **Pending:** continued module pumps to v19, App Store submission backlog

### 2. ECOSIRE.COM — Digital Storefront + Odoo SaaS Platform + Module Docs Site
- **Workspace:** `D:/ECOSIRE.COM/`
- **Status:** **Active** — live at ecosire.com
- **Brand umbrella:** ECOSIRE (parent) with sub-brands Odovation (Odoo-focused brand) and MuhammadAmir (personal brand)
- **Audience:** SMBs needing hosted Odoo Enterprise, digital-product buyers, brand-followers, module end-users
- **Offering:** Digital storefront selling Odoo modules + SaaS subscriptions, hosted Odoo 19 Enterprise (multi-tenant), multi-brand auth, native auth-core (no Authentik), **public module documentation site at `https://docs.ecosire.com/` (Docusaurus, sourced from `apps/docs/`)** — this is the canonical end-user doc surface for every Odoo module built in `D:/Development/`
- **Tech stack:** Turborepo 2.8 + pnpm 10.28 / NestJS 11 + Next.js 16 + Drizzle + PostgreSQL 17 / Docusaurus (port 3002 in dev) / PM2 + Nginx / AWS EC2 t3.xlarge / Cloudflare
- **Pricing:** subscription tiers (see live site)
- **Repo:** private monorepo (engrmuhammadamirnazir/ecosire.com)
- **Pending:** ongoing apps/modules expansion (74+ NestJS modules), backfilling docs site for older modules
- **Cross-workspace contract with D:/Development:** module CODE built in Development, end-user DOCS written/updated in ECOSIRE.COM/apps/docs/. `_update_product_prices.mjs` syncs pricing/catalog to the storefront DB.

### 3. ECOSIRE.AI — Autonomous SEO / AEO / GEO Content Platform
- **Workspace:** `D:/ECOSIRE.AI/`
- **Status:** **Active** — live at ecosire.ai
- **Brand:** ECOSIRE.AI
- **Audience:** content-marketing teams, SaaS founders, agencies needing autonomous SEO content production
- **Offering:** Autonomous SEO/AEO/GEO content generation pipeline (FastAPI + Celery + Claude/GPT)
- **Pricing:** SaaS tiered (see live site)
- **Repo:** private
- **Pending:** scale-out of autonomous pipeline + agency-tier features

### 4. ECOSIRE.IO — Managed Cloud / Hosting Platform
- **Workspace:** `D:/ECOSIRE.IO/`
- **Status:** **Active** (private launch) — direct competitor to Cloudpepper.io and odoo.sh
- **Brand:** ECOSIRE Cloud
- **Audience:** Odoo agencies, ERP customers needing managed-host without DIY DevOps
- **Offering:** Managed Odoo + ERPNext hosting, automated DevOps tooling, white-label partner portals, AI-powered optimization
- **Differentiation:** AI-powered optimization (ecosire.ai engine), integrated e-commerce connectors, multi-ERP support, lower operational costs
- **Pricing:** lower than Cloudpepper / odoo.sh by design
- **Repo:** github.com/ecosire/ecosire.io (private)
- **Pending:** public launch, customer onboarding flow

### 5. ADNET — Per-Install Ad Network Platform
- **Workspace:** `D:/Ad Network Website/`
- **Status:** **Active development** — flagship instance live at adsnetwork.ecosire.com (or in development)
- **Brand:** ADNET (flagship) + whitelabel product for agencies/networks
- **Audience:** advertisers buying CPI installs, mobile publishers, agencies wanting whitelabel CPI network
- **Offering:** Cost-Per-Install ad network — advertiser dashboard (Stripe-funded campaigns), publisher dashboard (CSV reports + payouts), admin panel, Fastify edge tracking service (MMP-compatible: AppsFlyer/Adjust/Branch), 7-rule deterministic anti-fraud, payout engine (PayPal/Wise/wire), HMAC-signed webhooks, full marketing site, native auth, whitelabel-ready (`tenant.config.ts`)
- **Tech stack:** pnpm 10.28 + Turborepo 2 + TypeScript 5.5 + Node 22 LTS / NestJS 11 + Drizzle + Fastify 5 + Next.js 15 + BullMQ
- **Pricing:** TBD; whitelabel licensing for agencies
- **Repo:** github.com/engrmuhammadamirnazir/ad-network-platform (private)
- **Pending:** flagship instance launch + first whitelabel customer

### 6. Dairy Business (Bahawalpur)
- **Workspace:** `D:/Dairy Business/`
- **Status:** **Pre-launch / Planning** — only business plan + financial model exist; no operations / code yet
- **Brand:** TBD
- **Audience:** local market (Bahawalpur, Pakistan) — milk, dairy products
- **Offering:** Dairy farm + processing operation
- **Documents:** `Dairy_Business_Plan_Bahawalpur.docx`, `Dairy_Financial_Model.xlsx`
- **Pending:** finalize business plan, secure funding/land, launch operations
- **Note:** non-tech business; intentionally under the same holding so it can leverage Odoo modules (manufacturing + inventory + accounting) for its own operations once running

### 7. OpenClaw — AI Agent Swarm Platform
- **Workspace:** `D:/OpenClaw/openclaw/`
- **Status:** **Active development** — agent swarm source for AI orchestration
- **Brand:** OpenClaw
- **Audience:** AI/ML teams needing multi-agent orchestration, internal Ecosire fleet (the `D:/Development/.claude/agents/` fleet uses OpenClaw patterns)
- **Offering:** AI agent swarm framework (also embedded as an Odoo module — see "OpenClaw AI" in Development module catalog)
- **Tech:** see `D:/OpenClaw/openclaw/AGENTS.md`
- **Pricing:** TBD
- **Repo:** private (engrmuhammadamirnazir/openclaw)
- **Pending:** product packaging, public release decision

---

## Cross-business dependencies

Several businesses share infrastructure or sell into each other:

- **ECOSIRE.AI ↔ ECOSIRE.IO:** ECOSIRE.IO uses the AI engine from ECOSIRE.AI for optimization. Cross-billing internal.
- **ECOSIRE.IO ↔ Development:** ECOSIRE.IO will eventually host Odoo customers from Development's customer pipeline.
- **ECOSIRE.COM ↔ Development:** ECOSIRE.COM's storefront sells modules built in Development. Pricing/module catalog/HTML assets sync from `D:/Development` via `_update_product_prices.mjs` — DB sync required for price changes.
- **Development ↔ EcosireClients:** all client artifacts produced by any agent in any workspace land in `D:/EcosireClients/<Active|Prospective>/<Client>/`.
- **Dairy Business ↔ Development:** once dairy operations start, they will use Odoo modules from Development (Manufacturing, Inventory, Accounting, multi-warehouse, payroll). Future internal customer.
- **OpenClaw ↔ Development:** OpenClaw agent patterns power the Development `.claude/agents/` fleet (27 agents). Some agents are shipped as Odoo modules.
- **All businesses ↔ D:/Company:** legal entity ownership. Letterhead, banking, tax, regulatory all flow from there.

---

## Pending-launch / attention items

These are projects that need attention based on current state:

| Project | What's pending | Priority hint |
|---------|----------------|---------------|
| **Dairy Business** | Finalize business plan, funding, land lease, operations launch | High — earliest stage, biggest unknowns |
| **ADNET flagship** | Public launch of `adsnetwork.ecosire.com`, first paying advertisers | High — closest to revenue |
| **ADNET whitelabel** | First whitelabel customer / contract | Medium — sales motion |
| **ECOSIRE.IO public launch** | Pricing public, customer onboarding flow tested end-to-end | Medium-High — competes with established players |
| **Module docs.ecosire.com backfill** | Many Odoo modules have been pump-upgraded (Waves A–M) and deployed to `<slug>.demo.ecosire.com` on Hetzner (37.27.2.10), but their public docs pages at docs.ecosire.com are still pending. Documentation-writer or module-developer must complete the backfill in `D:/ECOSIRE.COM/apps/docs/docs/<category>/<module>.md`. | Medium-High — affects module credibility on App Store and customer onboarding |
| **OpenClaw public release** | Product packaging, public/private decision | Low — internal tool driving internal value first |

These should appear in proposals indirectly (e.g., when a prospect asks about ad networks, ADNET upsell becomes available; dairy farms become future internal-customer of Odoo Manufacturing modules).

---

## How proposal-writing agents use this file

When the `sales-proposal` agent (D:/Development) or any documentation/strategic agent in any workspace produces a proposal:

1. Read this file to identify which ECOSIRE business lines apply to the prospect's needs.
2. Default to the relevant single business (e.g., Odoo client → Odoo Module Suite + Implementation), but flag cross-sells where they fit:
   - **Asks about marketing/SEO?** → mention ECOSIRE.AI
   - **Wants Odoo but can't manage hosting?** → mention ECOSIRE.IO managed hosting
   - **Buys digital products / wants to resell?** → mention ECOSIRE.COM platform
   - **Mobile app / acquisition / install funnel?** → mention ADNET (advertiser side)
   - **Has audience / publisher inventory?** → mention ADNET (publisher side)
   - **Agriculture / dairy in Pakistan?** → flag for Dairy Business future cross-pollination
3. Pick the right **legal entity** (ECOSIRE PRIVATE LIMITED for Asia/EU/MENA/Africa, ECOSIRE LLC for US/Canada/LatAm) and use the corresponding invoice line + bank details from `D:/Company/<entity>/03 - Banking/`.
4. Use the canonical letterhead from `D:/Company/ECOSIRE (PRIVATE) LIMITED/05 - Brand & Identity/Letterhead/letterhead_full.png` (single source for both entities).

---

## Cross-references

- **Client folder structure + document generator pattern:** `D:/EcosireClients/README.md`
- **Legal vault (incorporation, tax, banking, brand):** `D:/Company/`
- **Cross-workspace hive brain:** `C:/Users/Amir Nazir/Dropbox/Unity/05 - Maps of Content/MOC - Hive Mind.md`
- **Per-workspace project memory:** `~/.claude/projects/<workspace-slug>/memory/`

Maintain this file: when a new business is added, a brand changes, a launch happens, or a status flips (Pre-launch → Active, Active → Paused, etc.), update here AND propagate via `hive-mind-sync` to Unity Session Log. End-session skill should refresh this file's "Pending-launch / attention items" table whenever those items move.
