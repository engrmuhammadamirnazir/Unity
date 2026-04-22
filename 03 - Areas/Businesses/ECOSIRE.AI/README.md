---
type: business
tags: [business, ecosire-ai, saas, seo, aeo, geo, fastapi, celery]
domain: ecosire.ai
status: live
created: 2026-04-22
updated: 2026-04-22
---

# ECOSIRE.AI — Autonomous SEO/AEO/GEO Content Platform

**Live at [ecosire.ai](https://ecosire.ai).** Autonomous site scanner + agent runtime + WordPress plugin. NOT OpenClaw (which is a separate agent-swarm product).

## Code lives at

- **Sibling workspace:** `D:\ECOSIRE.AI\`
- **CLAUDE.md:** `D:\ECOSIRE.AI\CLAUDE.md` (added 2026-04-22)
- **README** (deep architecture): `D:\ECOSIRE.AI\README.md`
- **Memory indexes:** `~/.claude/projects/D--ECOSIRE-AI/memory/MEMORY.md` + `~/.claude/projects/D--ECOSIRE.AI/memory/MEMORY.md`

## Infrastructure

- **EC2 t3.large** — `54.197.92.23`, us-east-1
- **SSH key:** `ecosire.pem` (shared with ECOSIRE.COM)
- **Project path on server:** `/opt/ace/ai-content-engine/ai-content-engine/`
- **11 Docker containers**: postgres, redis, api, celery-worker, celery-beat, dashboard, nginx, authentik×4
- **PostgreSQL 16**, 24 tables, Alembic migrations (0001-0011)
- **Auth:** Authentik OIDC + next-auth v5 + PyJWT

## Tech stack

| Layer | Technology |
|-------|-----------|
| Backend | Python 3.12 / FastAPI / SQLAlchemy 2.0 async / Celery 5 |
| Frontend | Next.js 16 / React 19 / shadcn/ui / Tailwind v4 |
| WP Plugin | React SPA (Vite IIFE) v2.1.0 |
| AI | Claude (Opus/Sonnet/Haiku) + GPT-4o |
| Queue | Celery + Redis |
| Billing | Stripe (subscriptions + usage metering) |

## Three operational modes

- **A — Competitor Intelligence**
- **B — SEO Precision**
- **C — Volume Scaling**

## Key features

1. **Autonomous Site Scanner** — crawl → 7 analyzers → strategy → execute → monitor (weekly rescan Monday 3AM UTC)
2. **Agent Runtime** — declarative `AgentDefinition`, 21 tools, 6 profiles, 8 plugins, budget tracking with degradation at 80%
3. **WordPress Plugin v2.1.0** — React 19 + PHP, 8 pages, self-update system, PHP REST proxy keeps API tokens server-side
4. **Content Hub** — campaign management, article pipeline, keyword tracking
5. **Growth Strategy** — prioritized actions with traffic lift projections

## Critical technical patterns

- All backend code async (asyncpg, AsyncClient).
- `PyJWKClient` MUST have a custom User-Agent — Cloudflare blocks default `Python-urllib/3.12`.
- `tenacity` for retry with exponential backoff on external calls.
- UUID primary keys + `created_at`/`updated_at` on all models.

## Current focus

- See `D:\ECOSIRE.AI\README.md` and memory indexes for per-sprint current state.
- Test suite: 1,517 tests passing (pytest-asyncio, asyncio_mode="auto").

## Separation note

**ECOSIRE.AI ≠ OpenClaw AI.** OpenClaw is the agent-swarm product sourced at `D:\OpenClaw\openclaw\` (and an Odoo-embedded module inside `D:\Development\`). Don't cross-contaminate.

> For deep architecture read `D:\ECOSIRE.AI\README.md`. This note is the **30-second orientation layer**.
