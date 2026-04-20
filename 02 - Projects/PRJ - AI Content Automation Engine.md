---
type: project
tags: [project, ecosire, ai, saas, content-automation, fastapi, nextjs]
status: active
created: 2026-03-02
---

# PRJ — AI Content Automation Engine

> AI-powered SaaS platform for automated WordPress content generation and SEO optimization.
> Source: `D:\ECOSIRE.AI\` (canonical README at `D:\ECOSIRE.AI\README.md`)

---

## Overview

Multi-tenant SaaS platform that automates WordPress content creation using AI. Three operational modes for different content strategies, powered by Claude AI (primary) and GPT-4o (secondary).

| Field | Value |
|-------|-------|
| **Product** | Ecosire AI -- Multi-engine Visibility Platform |
| **Live URL** | https://ecosire.ai |
| **Auth URL** | https://auth.ecosire.ai |
| **Server** | 54.197.92.23 (AWS EC2 t3.large, us-east-1) |
| **SSH Key** | `ecosire.pem` |
| **Status** | Active -- 22 build sessions + mobile app + agent fleet (21 agents, 21 skills) |

---

## Content Modes

| Mode | Name | Description |
|------|------|-------------|
| **A** | Competitor Intelligence | Analyze competitor content and generate superior alternatives |
| **B** | SEO Precision | Create content optimized for specific keywords and search intent |
| **C** | Volume Scaling | Bulk content generation for rapid content library building |

---

## Tech Stack

### Backend
| Component | Technology |
|-----------|-----------|
| Language | Python 3.12+ |
| Framework | FastAPI |
| ORM | SQLAlchemy 2.0 (fully async) |
| Task Queue | Celery 5 + Redis 7 |
| Database | PostgreSQL 16 (23 tables, migration 0011) |
| Auth | Authentik OIDC (PyJWT + JWKS) |
| AI | Claude (Sonnet/Opus/Haiku) primary, GPT-4o secondary |
| Testing | pytest-asyncio (1,517 tests passing) |

### Frontend (Dashboard)
| Component | Technology |
|-----------|-----------|
| Framework | Next.js 16, React 19, TypeScript |
| Styling | Tailwind v4 + shadcn/ui |
| Data | @tanstack/react-query v5 |
| State | Zustand |
| Charts | Recharts v3.7.0 |
| Auth | next-auth@5.0.0-beta.30 |
| Graphs | @xyflow/react + @dagrejs/dagre |

### Infrastructure
| Component | Technology |
|-----------|-----------|
| Server | AWS EC2 t3.large (2 vCPU, 8GB RAM, 62GB gp3) |
| OS | Ubuntu 24.04.3 LTS |
| Containers | Docker Compose (11 containers) |
| SSL | Cloudflare Origin Certificate (valid until 2041) |
| DNS | Cloudflare (orange cloud proxy ON) |

---

## Core Features

### 1. AMGI Agents (6 AI Agents)
AI agents that handle different aspects of content creation and optimization.

### 2. Agent Runtime
Unified runtime system replacing the original BaseAgent hierarchy:
- **AgentDefinition** — Frozen dataclass configurations
- **AgentRuntime** — Core execution engine
- **BudgetTracker** — Token/cost management (degradation at 80%)
- **ToolRegistry** — 21 tools across 6 profiles
- **HookEngine** — 8 lifecycle events
- **MemoryManager** — pgvector-based memory (falls back to ORM on production)
- **SubagentCoordinator** — Multi-agent orchestration
- **WorkflowEngine** — 3 predefined workflows

Stats: 18 tasks, 20 commits, +15,456 lines, 349 new tests

### 3. Autonomous Site Scanner
Full website analysis pipeline:

```
Crawl → 7 Parallel Analyzers → StrategyGenerator → Dashboard → StrategyExecutor → Monitor
```

**7 Analysis Modules:**
1. Content Analyzer
2. Technical SEO Analyzer
3. Authority Analyzer
4. Schema Analyzer
5. CRO (Conversion Rate Optimization) Analyzer
6. Quality Analyzer
7. Keyword Analyzer

- 9 API endpoints under `/scanner`
- 208 scanner-specific tests
- Auto-rescan: Weekly (Monday 3AM UTC)

### 4. SEO Knowledge Base
- 905-line integrated reference (11 sections)
- Centralized in `seo_knowledge.py`
- Powers all AI agents and analyzers

### 5. WordPress Plugin v2.1.0
- Dual authentication (API key + JWT)
- 8 React-powered admin pages
- PHP class architecture with REST API
- Cross-platform ZIP distribution
- Auto-update mechanism

### 6. Dashboard CX Transformation
Complete dashboard rebuild (7 sessions):
- Entity relationship graph visualization
- AI assistant integration
- Mobile-responsive design
- Theme customization
- Real-time data with React Query

---

## Production Architecture (11 Containers)

| Container | Image | Port |
|-----------|-------|------|
| ace-postgres | postgres:16-alpine | 5432 |
| ace-redis | redis:7-alpine | 6379 |
| ace-api | custom (FastAPI) | 8000 |
| ace-celery-worker | custom (Celery) | — |
| ace-celery-beat | custom (Celery) | — |
| ace-dashboard | custom (Next.js) | 3000 |
| ace-nginx | nginx:1.27-alpine | 80, 443 |
| ace-authentik | authentik:2024.12.3 | 9000 |
| ace-authentik-worker | authentik:2024.12.3 | — |
| ace-authentik-db | postgres:16-alpine | 5432 |
| ace-authentik-redis | redis:7-alpine | 6379 |

---

## Database Schema (23 Tables)

Key models include: campaigns, articles, sites, keywords, site scans, scan findings, growth strategies, agent memories, agent plugins, workflow runs, agent runs, tasks, WordPress sites, and more.

---

## API Endpoints

### Agent Runtime (12 endpoints)
Under `/agents/` — agent definitions, runs, memories, workflows

### Scanner (9 endpoints)
Under `/scanner/` — site scans, findings, strategies, execution

---

## Key Technical Patterns

- All backend code fully async (asyncpg, AsyncClient)
- `tenacity` for AI client retry logic
- `pydantic-settings` for configuration
- UUID primary keys with `Mapped`/`mapped_column` style (SQLAlchemy 2.0)
- All StrEnum values UPPERCASE
- Campaign DELETE is permanent (cascade delete-orphan)
- Docker network isolation: API (`docker_default`) and Authentik (`docker_internal`) on separate networks

---

## Known Gotchas

- **Cloudflare blocks Python User-Agent**: PyJWKClient MUST have `headers={"User-Agent": "ai-content-engine/1.0"}` — Cloudflare blocks default `Python-urllib/3.12` with 403
- **Dashboard healthcheck**: Use `http://127.0.0.1:3000/` (NOT localhost — IPv6 fails in Alpine)
- **Docker compose**: Always needs `--env-file ../.env` flag
- **pgvector**: NOT installed on production (Alpine-based pg16) — memory search falls back to ORM

---

## Related

- [[Ecosire Private Limited]] — Parent company
- [[Ecosire - Production Servers]] — Server details
- [[ECOSIRE.COM Platform]] — Main platform (shares Authentik)
- [[MOC - Projects]] — Project index
