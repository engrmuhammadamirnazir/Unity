# Ecosire.ai - Comprehensive Project Audit, Enhancement & Deployment Prompt

> **Purpose**: Full-spectrum audit of the Ecosire.ai platform using the 22-agent Claude Code team. Covers bug detection/removal, feature verification, documentation accuracy, UI/UX quality, enhancement opportunities, testing, and production deployment.

---

## PROJECT CONTEXT

Ecosire AI is a multi-tenant SaaS content automation and SEO optimization platform. It crawls WordPress sites, analyzes SEO/content gaps using 7 specialized analyzers, generates optimized articles via AI, auto-fixes technical SEO issues, and publishes autonomously.

### Architecture
- **Backend**: Python 3.12 / FastAPI / SQLAlchemy 2.0 async / Celery 5.4+ / Redis 7 / PostgreSQL 16
- **Frontend Dashboard**: Next.js 16 / React 19 / TypeScript / TailwindCSS v4 / shadcn/ui
- **WordPress Plugin**: React 19 SPA (Vite 6 IIFE) inside WP admin, 11 PHP classes, 30+ REST proxy routes
- **Mobile App**: React Native / Expo / NativeWind (tabs: Dashboard, Sites, Content, Assistant, Settings)
- **Edge Workers**: Cloudflare Worker, Vercel Edge Middleware, Nginx sub_filter
- **SDKs**: Python (httpx), Node.js (TypeScript), PHP (Guzzle)
- **AI**: Claude (Opus/Sonnet/Haiku) + GPT-4o, 6 AMGI agents, 21 tools, 8 plugins
- **Auth**: Authentik OIDC + next-auth v5 + PyJWT
- **Billing**: Stripe subscriptions + usage metering
- **Infrastructure**: 11 Docker containers on AWS EC2 t3.large, Cloudflare SSL, nginx reverse proxy
- **Database**: 24 tables, UUID PKs, JSONB columns, Alembic migrations (0001-0012), pgvector (conditional)

### Project Structure
```
ai-content-engine/
├── backend/                    # FastAPI (28+ endpoints, 40+ services, 1519 tests)
│   ├── app/
│   │   ├── api/v1/endpoints/   # API routes
│   │   ├── models/             # SQLAlchemy 2.0 models (24 tables)
│   │   ├── schemas/            # Pydantic v2 schemas
│   │   ├── services/           # Business logic (40+ services)
│   │   │   └── scan_analyzers/ # 7 analysis modules
│   │   ├── agents/runtime/     # AMGI Agent Runtime (6 agents, 21 tools, 8 plugins)
│   │   ├── workers/            # Celery tasks
│   │   └── integrations/       # WordPress, AI clients
│   ├── alembic/versions/       # DB migrations (0001-0012)
│   └── tests/                  # 1,519 pytest-asyncio tests
├── dashboard/                  # Next.js 16 + React 19 (30+ routes, 200+ components)
│   └── src/
│       ├── app/                # Page routes (landing + dashboard)
│       ├── components/         # UI components (200+)
│       ├── lib/                # API client, auth, store, utils
│       └── types/              # TypeScript types/enums
├── wordpress-plugin/ecosire-ai/ # WP Plugin v2.3.0
│   ├── includes/               # 11 PHP classes
│   ├── src/                    # React 19 SPA (8 pages)
│   └── admin/                  # REST proxy + React mount
├── mobile/                     # React Native / Expo app (6 screens)
├── edge/                       # Cloudflare, Vercel, Nginx configs
├── sdks/                       # Python, Node, PHP client SDKs
├── docker/                     # Docker Compose + nginx
└── docs/                       # Design docs, SEO knowledge base, plans
```

---

## AGENT TEAM ROSTER & PHASE ASSIGNMENTS

Your `.claude/agents/` directory contains 22 specialized agents. Below is the exact mapping of each agent to their audit responsibilities.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    ECOSIRE.AI AUDIT - AGENT ASSIGNMENTS                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  PHASE 1: BACKEND AUDIT                                                │
│    Lead:    backend-dev.md        → Services, models, workers, config   │
│    Support: api-architect.md      → API endpoints, schemas, contracts   │
│    Support: data-engineer.md      → Database models, migrations, queries│
│    Support: bug-hunter.md         → Bug detection across backend code   │
│                                                                         │
│  PHASE 2: FRONTEND AUDIT                                               │
│    Lead:    frontend-dev.md       → Dashboard pages, components, flows  │
│    Support: bug-hunter.md         → Bug detection across frontend code  │
│                                                                         │
│  PHASE 3: UI/UX & RESPONSIVE AUDIT                                     │
│    Lead:    design-auditor.md     → Layout, spacing, consistency audit  │
│    Lead:    ux-designer.md        → UX flows, mobile responsive, a11y  │
│                                                                         │
│  PHASE 4: WORDPRESS PLUGIN AUDIT                                       │
│    Lead:    wp-specialist.md      → PHP classes, REST proxy, React SPA  │
│                                                                         │
│  PHASE 5: MOBILE APP AUDIT                                             │
│    Lead:    mobile-dev.md         → Expo app, screens, navigation      │
│                                                                         │
│  PHASE 6: SDK & INTEGRATION AUDIT                                      │
│    Lead:    ai-integrator.md      → SDKs, edge workers, tracker.js     │
│                                                                         │
│  PHASE 7: SECURITY AUDIT                                               │
│    Lead:    security-auditor.md   → OWASP Top 10, auth, encryption     │
│                                                                         │
│  PHASE 8: SEO & AI AGENT AUDIT                                        │
│    Lead:    seo-specialist.md     → SEO knowledge base, analyzers      │
│    Support: ai-integrator.md      → AMGI agents, tools, plugins        │
│                                                                         │
│  PHASE 9: CODE QUALITY & REVIEW                                       │
│    Lead:    code-reviewer.md      → Code patterns, duplication, quality │
│    Support: performance-optimizer.md → N+1 queries, bundle size, perf  │
│                                                                         │
│  PHASE 10: DOCUMENTATION AUDIT                                         │
│    Lead:    doc-writer.md         → README, CLAUDE.md, guides, docs    │
│    Support: researcher.md         → Verify accuracy, cross-reference   │
│                                                                         │
│  PHASE 11: ENHANCEMENT ANALYSIS                                        │
│    Lead:    researcher.md         → Feature gaps, competitor analysis   │
│    Support: project-manager.md    → Prioritization, roadmap planning   │
│    Support: performance-optimizer.md → Performance improvement ideas   │
│                                                                         │
│  PHASE 12: TESTING & VALIDATION                                        │
│    Lead:    qa-engineer.md        → Test suite, coverage, regression   │
│    Lead:    fullstack-tester.md   → Integration tests, E2E flows      │
│                                                                         │
│  PHASE 13: INFRASTRUCTURE & DEVOPS                                     │
│    Lead:    devops-ace.md         → Docker, nginx, AWS, CI/CD          │
│                                                                         │
│  PHASE 14: RELEASE & DEPLOY                                            │
│    Lead:    release-manager.md    → Commits, tagging, deploy, verify   │
│    Support: project-manager.md    → Final checklist, sign-off          │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## PHASE 1: BACKEND AUDIT

**Agents**: `backend-dev` (lead), `api-architect`, `data-engineer`, `bug-hunter`

### 1A. API Endpoint Verification (`api-architect`)

Audit every endpoint in `backend/app/api/v1/endpoints/`:

- **Sites**: CRUD, PATCH update, list with filters, tenant isolation
- **Campaigns**: CRUD, status transitions (active/paused), mode switching (A/B/C)
- **Articles**: CRUD, bulk approve, CSV export, status pipeline
- **Keywords**: CRUD, priority update, status pipeline
- **Scanner**: Start scan, get findings, bulk update findings, growth strategy
- **Tasks**: List, delete (terminal only), retry
- **Changesets**: List, stage, publish, rollback
- **Crawl Jobs**: List, cancel, delete, clear completed, list pages
- **Notifications**: List, mark read
- **Activity**: List, clear
- **Search**: Cross-entity search (?q=&type=)
- **Dashboard**: Attention items aggregation
- **Billing**: Stripe integration, subscription management
- **Settings**: AI keys, API keys, team, templates, voice
- **Assistant**: Conversations, messages, streaming

For each endpoint verify:
- [ ] Request validation (Pydantic schemas match actual payloads)
- [ ] Response models return correct fields and HTTP status codes
- [ ] Error paths (404, 422, 403, 500) return proper error responses
- [ ] Tenant isolation — no cross-tenant data leaks (every query filters by `tenant_id`)
- [ ] Pagination works (offset/limit or cursor-based)
- [ ] Sorting and filtering work as documented

### 1B. Service Layer Audit (`backend-dev`)

Review all 40+ services in `backend/app/services/`:

| Service | Key Checks |
|---------|-----------|
| `site_scanner.py` + 7 `scan_analyzers/` | Each analyzer produces correct findings, scoring is accurate |
| `content_pipeline.py`, `mode_a_pipeline.py` | Content generation flows complete without errors |
| `publishing_engine.py`, `publishing_service.py` | WordPress REST API publishing works, error handling |
| `fix_executor.py`, `fix_generators.py` | Auto-fix pipeline generates and applies fixes correctly |
| `strategy_generator.py`, `strategy_executor.py` | Growth strategy generation and execution |
| `strategy_assistant.py`, `strategy_assistant_stream.py` | AI strategy assistant streaming |
| `quality_gate.py` | Quality scoring logic produces accurate 100-point scores |
| `duplicate_detector.py` | Dedup logic catches duplicates without false positives |
| `ai_assistant.py` | AI assistant conversations work with Claude/GPT |
| `analytics_service.py`, `attribution_engine.py` | Analytics calculations are accurate |
| `usage_service.py` | Usage metering tracks correctly per plan tier |
| `tenant_provisioning.py` | New tenant setup creates all required resources |
| `secrets_service.py` | Fernet encryption/decryption of API keys works |
| `rate_limiter.py`, `publish_throttle.py` | Redis rate limiting enforces limits |
| `event_publisher.py`, `webhook_dispatcher.py` | Events and webhooks fire correctly |
| `batch_processor.py` | Batch operations handle large sets without timeout |
| `campaign_advisor.py` | Campaign recommendations are actionable |
| `crawl_service.py` | Crawl jobs complete and store results |
| `feedback_service.py` | Feedback loop data aggregation is accurate |
| `image_service.py` | Image generation/optimization works |
| `prompt_engine.py`, `default_prompts.py` | Prompt templates produce good AI output |
| `action_executor.py` | Action execution pipeline works |
| `change_approval_policy.py` | Change approval rules are enforced |

### 1C. Database Model Audit (`data-engineer`)

Review all 24+ models in `backend/app/models/`:

- [ ] Verify all relationships, foreign keys, cascades are correct
- [ ] Check for missing indexes on frequently queried columns (tenant_id, status, created_at)
- [ ] Verify JSONB column schemas are consistent and documented
- [ ] Check enum values match between Python StrEnums and database values (all UPPERCASE)
- [ ] Verify Alembic migrations (0001-0012) are consistent with current models
- [ ] Check migration 0010 pgvector conditional logic works
- [ ] Verify no orphaned records possible (cascade deletes configured)
- [ ] Check UUID PK generation is correct

Models to audit: `tenant`, `campaign`, `content`, `wordpress`, `scanner`, `crawl`, `task`, `changeset`, `notification`, `activity`, `api_key`, `assistant`, `batch`, `brand_voice`, `prompt_template`, `analytics`, `feedback`, `evaluation`, `agent_memory`, `agent_plugin`, `agent_run`, `workflow_run`, `sheets_sync`

### 1D. Celery Worker Audit (`backend-dev`)

Review `backend/app/workers/`:

- [ ] All task definitions have proper retries and error handling
- [ ] Task routing to correct queues (high/default/low)
- [ ] Periodic tasks (beat schedule) fire at correct intervals
- [ ] No memory leaks in long-running workers
- [ ] Task result expiration is configured
- [ ] Dead letter handling for permanently failed tasks

### 1E. Bug Detection (`bug-hunter`)

Scan the entire backend for:

- [ ] Unhandled exceptions that could cause 500 errors
- [ ] Race conditions in concurrent operations
- [ ] Off-by-one errors in pagination
- [ ] Incorrect async/await usage (missing await, blocking calls)
- [ ] Resource leaks (unclosed DB sessions, HTTP connections)
- [ ] Incorrect error propagation (swallowed exceptions)
- [ ] Logic errors in conditionals and loops
- [ ] Incorrect type coercions

**Deliverable**: `AUDIT_BACKEND_REPORT.md`

---

## PHASE 2: FRONTEND DASHBOARD AUDIT

**Agents**: `frontend-dev` (lead), `bug-hunter`

### 2A. Page-by-Page Verification

Check every route in `dashboard/src/app/`:

**Landing/Marketing Pages**:
- [ ] `/` — Home page (hero, features, CTA, footer)
- [ ] `/product` — Product page
- [ ] `/pricing` — Pricing cards, plan comparison, Stripe checkout links
- [ ] `/solutions` — Solutions page
- [ ] `/case-studies` — Case studies page
- [ ] `/technology` — Technology page

**Auth Pages**:
- [ ] `/sign-in` — Authentik OIDC sign-in flow works
- [ ] `/sign-up` — Registration flow works

**Dashboard Pages**:
- [ ] `/dashboard` — Command Center: stat cards, needs attention (60s refetch), activity timeline, usage meter, quick actions
- [ ] `/dashboard/content-hub` — Master-detail: CampaignPanel (w-72) + Tabs (Articles/Keywords/Jobs/Settings), URL state via `?campaign=<id>`
- [ ] `/dashboard/content-hub/[id]` — Article Editor: two-panel layout, auto-save (1500ms debounce), approve/publish/delete
- [ ] `/dashboard/sites` — Site list, rescan all, add site dialog, click-to-detail
- [ ] `/dashboard/sites/[id]` — 5 tabs: Overview (HealthScoreCard + RadarChart), Findings (DataTable + filters + bulk), Strategy (Critical/Quick Wins/Growth), Crawl (start/cancel/delete/poll), History (chart + trends)
- [ ] `/dashboard/automation` — Pipeline viz + 3 tabs: Tasks (filter/retry/cancel), Changesets (stage/publish/rollback), Crawl Jobs (filter/clear)
- [ ] `/dashboard/analytics` — 4 tabs: Overview (stat cards + LineChart + PieChart + CSV), Usage (subscription + meters), Content (metrics + BarChart + trend), Pipeline (4-stage viz + HealthBadge + 30s refresh)
- [ ] `/dashboard/entity-graph` — @xyflow/react + dagre graph visualization
- [ ] `/dashboard/assistant` — AI chat interface, conversation management
- [ ] `/dashboard/settings` — 5 tabs: AI Keys (BYOAK + ProviderCard), API Keys (create/revoke), Team (invite/role/remove), Templates (variable insertion), Voice (tone/reading level/persona)
- [ ] `/dashboard/billing` — Stripe billing portal, plan management
- [ ] `/dashboard/guide` — Onboarding/user guide content

**Redirect Pages** (verify redirects work):
- [ ] `/dashboard/campaigns/*` → `/dashboard/content-hub`
- [ ] `/dashboard/content/*` → `/dashboard/content-hub`
- [ ] `/dashboard/growth` → `/dashboard/sites`
- [ ] `/dashboard/tasks` → `/dashboard/automation`
- [ ] `/dashboard/changesets` → `/dashboard/automation`
- [ ] `/dashboard/crawl` → `/dashboard/automation`
- [ ] `/dashboard/orchestrator` → `/dashboard/automation`
- [ ] `/dashboard/feedback-loop` → `/dashboard/analytics?tab=pipeline`

### 2B. Shared Component Audit

- [ ] `DataTable<T>` — sorting, bulk select, expandable rows, BulkActionBar, pagination
- [ ] `CommandPalette` — Cmd+K trigger, debounced search, grouped results, keyboard nav
- [ ] `ConfirmDialog` — default + destructive variants, focus trap
- [ ] `PaginationControls` — page/perPage/total calculations
- [ ] `NotificationCenter` — bell + badge, popover, 30s polling, mark read
- [ ] `ShortcutsHelpDialog` — Shift+? trigger, shortcut display
- [ ] Chart components (Recharts) — LineChart, BarChart, PieChart, RadarChart data binding
- [ ] Sidebar — 5 nav items + 2 bottom, active states, mobile drawer
- [ ] Header — user menu, theme toggle (dark/light), notification bell
- [ ] All `@/components/ui/` shadcn primitives render correctly

### 2C. Data Flow & State Audit

- [ ] `apiClient` (lib/api-client.ts) handles all API calls, auth headers, error responses
- [ ] React Query: correct cache keys, stale times, refetch intervals, optimistic updates
- [ ] Error handling: toast notifications via sonner for all error/success paths
- [ ] Loading states: skeleton screens on initial load, spinners on mutations
- [ ] URL state: query params for tabs, filters, campaign selection
- [ ] `useKeyboardShortcuts` hook works cross-platform (Cmd/Ctrl)

### 2D. TypeScript Audit

- [ ] All types in `src/types/index.ts` match backend Pydantic schemas exactly
- [ ] No `any` types in production code
- [ ] Enum values match backend StrEnums (all UPPERCASE)
- [ ] `npx next build` succeeds with zero type errors
- [ ] `npx next lint` passes with zero warnings

**Deliverable**: `AUDIT_FRONTEND_REPORT.md`

---

## PHASE 3: UI/UX & RESPONSIVE AUDIT

**Agents**: `design-auditor` (lead), `ux-designer` (lead)

### 3A. Desktop Layout (design-auditor)

Test at: 1920px, 1440px, 1280px, 1024px

- [ ] Sidebar + content area layout is correct at all breakpoints
- [ ] Data tables don't overflow or lose columns
- [ ] Charts resize proportionally
- [ ] Modals/dialogs are centered and appropriately sized
- [ ] Form layouts (settings 5-tab page, article editor, campaign edit) look clean
- [ ] Command Palette overlay works correctly
- [ ] Landing pages hero section, pricing grid, feature sections scale properly

### 3B. Tablet Layout (design-auditor)

Test at: 768px, 1024px

- [ ] Sidebar collapses to hamburger/drawer
- [ ] Content reflows to single column where needed
- [ ] Touch targets >= 44px
- [ ] Data tables get horizontal scroll
- [ ] Charts remain readable

### 3C. Mobile Layout (ux-designer)

Test at: 375px (iPhone SE), 390px (iPhone 14), 414px (iPhone Plus)

- [ ] Hamburger menu navigation works
- [ ] All dashboard pages stack vertically
- [ ] Charts readable on small screens (or replaced with summary cards)
- [ ] Form inputs don't overflow
- [ ] Action dropdowns/menus work on touch
- [ ] Command Palette works on mobile
- [ ] Article editor usable on mobile
- [ ] Pricing page cards stack vertically

### 3D. UX Best Practices (ux-designer)

- [ ] Consistent spacing and typography (TailwindCSS v4 design tokens)
- [ ] Dark mode works on EVERY page/component without visual artifacts
- [ ] Loading states: skeletons on page load, spinners on actions
- [ ] Empty states: helpful messages + CTA on every empty list/table
- [ ] Error states: actionable messages (not generic "Something went wrong")
- [ ] Success feedback: toast notifications + visual state changes
- [ ] Destructive actions ALWAYS have ConfirmDialog
- [ ] Navigation: breadcrumbs, back buttons, sidebar active states
- [ ] Consistent iconography (lucide-react everywhere)
- [ ] Color system: proper use of primary, secondary, destructive, muted

### 3E. Performance UX (design-auditor)

- [ ] No layout shifts (CLS) during page load
- [ ] Images/icons load without flicker
- [ ] Transitions/animations are smooth (no jank)
- [ ] Pagination/infinite scroll doesn't cause jank
- [ ] Theme toggle (dark/light) transitions smoothly

**Deliverable**: `AUDIT_UIUX_REPORT.md`

---

## PHASE 4: WORDPRESS PLUGIN AUDIT

**Agent**: `wp-specialist` (lead)

### 4A. PHP Backend — All 11 Classes

| Class | Key Checks |
|-------|-----------|
| `class-ecosire-auth.php` | AES-256-CBC encryption, API key validation, nonce verification |
| `class-ecosire-api-client.php` | REST proxy to FastAPI, error handling, timeout config |
| `class-ecosire-admin.php` | Menu registration, React SPA mount, capability checks |
| `class-ecosire-rest-proxy.php` | 30+ route handlers, proper forwarding, auth header injection |
| `class-ecosire-page-sync.php` | WordPress page sync, conflict resolution |
| `class-ecosire-schema-renderer.php` | JSON-LD injection, breadcrumb schema, valid output |
| `class-ecosire-analytics.php` | Analytics collection, data accuracy |
| `class-ecosire-change-tracker.php` | Change detection, diff storage |
| `class-ecosire-cron.php` | WP-Cron schedules, task execution |
| `class-ecosire-notifications.php` | Admin notifications, dismiss handling |
| `class-ecosire-updater.php` | Self-update system, version checking |

### 4B. React SPA — All 8 Pages

- [ ] Dashboard page loads and displays data
- [ ] Sites page lists connected sites
- [ ] Content page shows articles/campaigns
- [ ] Scanner page triggers and displays scans
- [ ] Analytics page shows charts and metrics
- [ ] Settings page saves configuration
- [ ] Auto-Fix page shows/executes fixes
- [ ] Schema page manages JSON-LD schemas

### 4C. Security

- [ ] Nonce validation on ALL AJAX/REST calls
- [ ] `current_user_can()` checks on admin-only operations
- [ ] `sanitize_text_field()`, `esc_html()`, `esc_attr()` on all outputs
- [ ] No direct SQL queries (use `$wpdb->prepare()` if any)
- [ ] XSS prevention (no raw `echo` of user input)
- [ ] CSRF protection via WP nonces

### 4D. Build

- [ ] `build-plugin.sh` produces valid output
- [ ] Vite config generates correct IIFE bundle
- [ ] `ecosire-ai.zip` contains all required files, correct paths
- [ ] `fix-zip-paths.py` handles backslash conversion

**Deliverable**: `AUDIT_WORDPRESS_REPORT.md`

---

## PHASE 5: MOBILE APP AUDIT

**Agent**: `mobile-dev` (lead)

### 5A. Screens

- [ ] `app/(auth)/login.tsx` — Authentication flow
- [ ] `app/(tabs)/index.tsx` — Dashboard tab (stats, quick actions)
- [ ] `app/(tabs)/sites.tsx` — Sites list and detail
- [ ] `app/(tabs)/content.tsx` — Content/articles view
- [ ] `app/(tabs)/assistant.tsx` — AI chat interface
- [ ] `app/(tabs)/settings.tsx` — User settings

### 5B. Navigation & Layout

- [ ] Tab navigation works (`(tabs)/_layout.tsx`)
- [ ] Auth layout protects authenticated routes
- [ ] Root layout (`_layout.tsx`) handles initialization
- [ ] Deep linking configuration in `app.json`
- [ ] NativeWind/Tailwind styles render correctly
- [ ] Safe area insets respected on all screens

### 5C. API & State

- [ ] API client in `lib/` connects to backend
- [ ] Auth token management (storage, refresh, expiry)
- [ ] Error handling and offline states
- [ ] Loading indicators on all async operations

### 5D. Build Verification

- [ ] `npx expo start` runs without errors
- [ ] EAS build config (`eas.json`) is valid
- [ ] Assets (icons, splash) are correct dimensions

**Deliverable**: `AUDIT_MOBILE_REPORT.md`

---

## PHASE 6: SDK & INTEGRATION AUDIT

**Agent**: `ai-integrator` (lead)

### 6A. Python SDK (`sdks/python/`)
- [ ] Async client (httpx) works
- [ ] Sync wrappers handle event loop detection
- [ ] Retry logic (4 attempts with backoff)
- [ ] `EcosireConnectionError` (not shadowing built-in)
- [ ] Typed parameters on all methods
- [ ] All API methods match backend endpoints

### 6B. Node.js SDK (`sdks/node/`)
- [ ] ESM + CJS dual export works
- [ ] AbortController timeout (clearTimeout in finally)
- [ ] Retry logic (4 attempts)
- [ ] TypeScript types are correct
- [ ] All API methods match backend endpoints

### 6C. PHP SDK (`sdks/php/`)
- [ ] Guzzle client works
- [ ] Retry middleware configured
- [ ] Exception classes imported correctly
- [ ] `json_decode` null validation
- [ ] All API methods match backend endpoints

### 6D. JS Tracker (`backend/app/static/tracker.js`)
- [ ] Core Web Vitals: LCP, CLS, INP (not deprecated FID)
- [ ] Scroll depth milestones (no duplicates)
- [ ] Engagement time tracking
- [ ] `sendBeacon` with correct Content-Type (`application/json` Blob)
- [ ] `crypto.randomUUID()` with v4 UUID fallback
- [ ] `sanitizeUrl()` strips query strings
- [ ] Flushing guard prevents concurrent empty flushes
- [ ] < 1.5KB gzipped

### 6E. Edge Workers (`edge/`)
- [ ] Cloudflare Worker: siteKey guard, XSS prevention via `encodeURIComponent`
- [ ] Nginx: `proxy_set_header Accept-Encoding ""` for sub_filter
- [ ] Vercel: STUB warning is clear

**Deliverable**: `AUDIT_INTEGRATIONS_REPORT.md`

---

## PHASE 7: SECURITY AUDIT

**Agent**: `security-auditor` (lead)

### 7A. OWASP Top 10 Check

| # | Category | What to Check |
|---|----------|--------------|
| A01 | Broken Access Control | Tenant isolation on EVERY query, JWT validation on all protected routes, role-based access |
| A02 | Cryptographic Failures | Fernet encryption for secrets, SHA-256 API key hashing, AES-256-CBC in WP plugin, no weak algorithms |
| A03 | Injection | SQLAlchemy ORM only (no raw SQL), parameterized queries, no eval/exec/subprocess |
| A04 | Insecure Design | Rate limiting on all public endpoints, publish throttle, scan limits |
| A05 | Security Misconfiguration | CORS origins restrictive, debug=false in production, no default credentials |
| A06 | Vulnerable Components | Check all Python, npm, PHP dependencies for known CVEs |
| A07 | Auth Failures | OIDC validation, JWT expiry, session management, brute force protection |
| A08 | Data Integrity Failures | Verify Alembic migrations are tamper-proof, no unsigned data |
| A09 | Logging Failures | structlog captures security events, no PII in logs |
| A10 | SSRF | Verify WordPress REST API calls validate URLs, no arbitrary URL fetching |

### 7B. Additional Security Checks

- [ ] No hardcoded API keys, passwords, or tokens in source code
- [ ] `.env.example` doesn't contain real secrets
- [ ] `.gitignore` excludes all sensitive files (.env, *.pem, credentials)
- [ ] DOMPurify used for XSS prevention in dashboard
- [ ] CSP headers recommended for production
- [ ] HSTS headers configured
- [ ] API key rotation mechanism exists
- [ ] Audit logging for admin actions

**Deliverable**: `AUDIT_SECURITY_REPORT.md`

---

## PHASE 8: SEO & AI AGENT AUDIT

**Agents**: `seo-specialist` (lead), `ai-integrator` (support)

### 8A. SEO Knowledge Base (`seo-specialist`)

- [ ] `backend/app/seo_constants.py` values are 2026-current
- [ ] `agents/runtime/knowledge/seo_knowledge.py` matches constants
- [ ] `docs/seo/SEO_KNOWLEDGE_BASE.md` matches code
- [ ] All 7 scan analyzers import thresholds from single source of truth
- [ ] E-E-A-T scoring criteria are accurate
- [ ] Core Web Vitals thresholds match Google's current standards
- [ ] Schema.org JSON-LD templates are valid

### 8B. AMGI Agent Runtime (`ai-integrator`)

- [ ] All 6 agent definitions load correctly: Content, Technical, Authority, Quality, GeoAEO, CRO
- [ ] All 21 tools in tool registry execute properly
- [ ] All 8 plugins in plugin loader work
- [ ] BudgetTracker enforces token limits
- [ ] ToolRegistry handles tool selection and compaction
- [ ] HookEngine fires pre/post hooks
- [ ] MemoryManager (pgvector) stores and retrieves memories
- [ ] WorkflowEngine runs all 3 workflows
- [ ] SubagentCoordinator orchestrates multi-agent tasks
- [ ] RunRegistry tracks agent runs
- [ ] Feature flag `USE_AGENT_RUNTIME=False` correctly disables runtime

### 8C. Content Generation Quality

- [ ] Mode A (Competitor Monitoring) produces competitive analysis
- [ ] Mode B (SEO Precision) targets specific keywords
- [ ] Mode C (Volume Scaling) generates bulk content
- [ ] Quality gate scoring is calibrated (100-point scale)
- [ ] Duplicate detection catches similar content
- [ ] Generated articles pass E-E-A-T criteria

**Deliverable**: `AUDIT_SEO_AI_REPORT.md`

---

## PHASE 9: CODE QUALITY & REVIEW

**Agents**: `code-reviewer` (lead), `performance-optimizer` (support)

### 9A. Code Quality (`code-reviewer`)

- [ ] No code duplication across services (DRY principle)
- [ ] Consistent error handling patterns (all services use same approach)
- [ ] Consistent logging patterns (structlog everywhere)
- [ ] No dead code (unused imports, unreachable branches, commented-out code)
- [ ] No TODO/FIXME/HACK comments without associated tickets
- [ ] Consistent naming conventions (snake_case Python, camelCase TS)
- [ ] Proper separation of concerns (no business logic in API layer)
- [ ] No circular imports

### 9B. Performance (`performance-optimizer`)

**Backend**:
- [ ] N+1 query detection (use `selectinload`/`joinedload` where needed)
- [ ] Missing database indexes on hot query paths
- [ ] Celery task efficiency (batch processing where possible)
- [ ] Redis cache utilization (cache frequently accessed data)
- [ ] Connection pool configuration (async engine pool size)

**Frontend**:
- [ ] Bundle size analysis (`npx next build` output)
- [ ] Code splitting and lazy loading (dynamic imports)
- [ ] Unnecessary re-renders (missing React.memo, useMemo, useCallback)
- [ ] Image optimization (next/image usage)
- [ ] React Query stale time optimization

### 9C. Linting

```bash
# Backend
cd ai-content-engine/backend && ruff check . && ruff format --check .

# Frontend
cd ai-content-engine/dashboard && npx next lint
```

- [ ] Zero lint errors on backend
- [ ] Zero lint errors on frontend
- [ ] Consistent code formatting

**Deliverable**: `AUDIT_CODE_QUALITY_REPORT.md`

---

## PHASE 10: DOCUMENTATION AUDIT

**Agents**: `doc-writer` (lead), `researcher` (support)

### 10A. Core Documentation

| Document | Checks |
|----------|--------|
| `README.md` (root) | Architecture diagram, tech stack versions, project structure, commands all accurate |
| `ai-content-engine/README.md` | Same as above, verify it exists and matches |
| `CLAUDE.md` | Project structure matches filesystem, conventions current, migration head correct, all commands work |
| `CHANGELOG.md` | All audit changes documented, existing entries accurate, dates correct |
| `.env.example` | All required variables listed, no real secrets, descriptions helpful |

### 10B. Docs Directory

| Document | Checks |
|----------|--------|
| `WORDPRESS_PLUGIN_USER_GUIDE.md` | Matches plugin v2.3.0 features, installation steps work |
| `TESTING_GUIDE.md` | Testing instructions work, commands correct |
| `deployment.md` | Deployment steps current, SSH commands work |
| `TRACKER_AND_SDKS.md` | SDK docs match actual code |
| `SEO_KNOWLEDGE_BASE.md` | Matches `seo_knowledge.py` and `seo_constants.py` |
| All design docs in `docs/plans/` | Mark completed items, note deviations from plan |

### 10C. User-Facing Documentation

- [ ] Dashboard `/guide` page content reflects all current features
- [ ] Onboarding flow instructions are complete and accurate
- [ ] API documentation (OpenAPI/Swagger) auto-generates correctly
- [ ] WordPress plugin `readme.txt` is accurate for WP plugin directory

### 10D. Code Documentation

- [ ] API endpoint docstrings match actual behavior
- [ ] Complex business logic has explanatory comments
- [ ] No misleading or outdated comments
- [ ] Service function signatures have clear parameter descriptions

**Deliverable**: Updated docs + `AUDIT_DOCUMENTATION_REPORT.md`

---

## PHASE 11: ENHANCEMENT ANALYSIS

**Agents**: `researcher` (lead), `project-manager`, `performance-optimizer`

### 11A. Feature Gaps (`researcher`)

- [ ] Identify incomplete/feature-flagged features (e.g., `USE_AGENT_RUNTIME=False`)
- [ ] Review competitor platforms (Surfer SEO, Clearscope, MarketMuse, Frase) for feature gaps
- [ ] Identify missing SaaS table stakes (team collaboration, audit trail, webhooks, API versioning)
- [ ] Suggest AI agent improvements (better prompts, new tools, workflow enhancements)
- [ ] Identify automation opportunities (more auto-fix rules, smarter scheduling)
- [ ] Review mobile app for missing features vs dashboard

### 11B. UX Improvements (`researcher`)

- [ ] Onboarding conversion funnel optimization
- [ ] Dashboard information density improvements
- [ ] Batch operation UX (bulk actions across entities)
- [ ] Notification and alerting improvements
- [ ] Keyboard shortcut coverage gaps

### 11C. Infrastructure Improvements (`performance-optimizer`)

- [ ] Docker multi-stage build optimization
- [ ] nginx caching, compression, HTTP/2 configuration
- [ ] CDN optimization for static assets
- [ ] Database connection pooling tuning
- [ ] Monitoring and alerting (Prometheus, Grafana, Sentry)
- [ ] Backup and disaster recovery strategy

### 11D. Monetization (`project-manager`)

- [ ] Pricing tier analysis (free/starter/pro/enterprise)
- [ ] Usage metering accuracy and billing fairness
- [ ] Upsell opportunities in the UI
- [ ] Churn prevention features (usage alerts, success metrics)

### 11E. Prioritized Roadmap

Create `ENHANCEMENT_OPPORTUNITIES_REPORT.md` with:

```
| Priority | Enhancement | Effort | Impact | Category |
|----------|------------|--------|--------|----------|
| P0 | Critical - must do now | S/M/L | High | Bug/Security |
| P1 | High - do this sprint | S/M/L | High | Feature/Perf |
| P2 | Medium - next sprint | S/M/L | Medium | Feature/UX |
| P3 | Low - backlog | S/M/L | Low | Nice-to-have |
```

**Deliverable**: `ENHANCEMENT_OPPORTUNITIES_REPORT.md`

---

## PHASE 12: TESTING & VALIDATION

**Agents**: `qa-engineer` (lead), `fullstack-tester` (lead)

### 12A. Backend Tests (`qa-engineer`)

```bash
cd ai-content-engine/backend && python -m pytest tests/ -v --tb=short
```

- [ ] ALL 1,519+ tests pass
- [ ] Run with coverage: `python -m pytest tests/ -v --cov=app --cov-report=html --cov-report=term-missing`
- [ ] Coverage target: >85%
- [ ] Identify untested code paths, add missing tests
- [ ] Fix any flaky tests (async timing, random failures)
- [ ] Verify test fixtures and mocks are accurate

### 12B. Frontend Build (`qa-engineer`)

```bash
cd ai-content-engine/dashboard && npx next build
```

- [ ] Zero TypeScript errors
- [ ] Zero build warnings (or document acceptable ones)
- [ ] Bundle size within acceptable limits

### 12C. Linting (`qa-engineer`)

```bash
cd ai-content-engine/backend && ruff check .
cd ai-content-engine/dashboard && npx next lint
```

- [ ] Zero lint errors on both

### 12D. WordPress Plugin Build (`qa-engineer`)

```bash
cd ai-content-engine/wordpress-plugin/ecosire-ai && bash build-plugin.sh
```

- [ ] Build succeeds
- [ ] Output files are correct

### 12E. Integration Tests (`fullstack-tester`)

End-to-end user flows:
- [ ] **Onboarding**: Sign up -> Add site -> Connect WordPress -> First scan
- [ ] **Content**: Create campaign -> Generate article -> Quality gate -> Approve -> Publish to WordPress
- [ ] **Scanner**: Run scan -> View findings -> Execute strategy -> Auto-fix -> Re-scan
- [ ] **Billing**: Subscribe -> Track usage -> Hit limits -> Upgrade plan
- [ ] **WordPress Plugin**: Install -> Connect API key -> Sync pages -> View analytics -> Auto-fix -> Manage schema
- [ ] **AI Assistant**: Start conversation -> Ask question -> Get actionable response
- [ ] **Settings**: Add AI key -> Create API key -> Invite team member -> Create template -> Set brand voice

### 12F. Regression Tests (`fullstack-tester`)

- [ ] All bugs from CHANGELOG.md haven't regressed
- [ ] Edge cases: empty states, max limits, concurrent operations, rapid clicking
- [ ] Cross-browser: Chrome, Firefox, Safari, Edge
- [ ] Network: slow connection, intermittent failures, timeout handling

**Deliverable**: `AUDIT_TEST_RESULTS.md` with pass/fail, coverage %, remaining issues

---

## PHASE 13: INFRASTRUCTURE & DEVOPS AUDIT

**Agent**: `devops-ace` (lead)

### 13A. Docker Configuration

- [ ] `docker-compose.dev.yml` starts all services correctly
- [ ] `docker-compose.prod.yml` is optimized for production
- [ ] Dockerfiles use multi-stage builds where applicable
- [ ] Layer caching is optimized (dependency install before code copy)
- [ ] Health checks configured for all services
- [ ] Resource limits set (memory, CPU)
- [ ] Restart policies configured

### 13B. Nginx Configuration

- [ ] Reverse proxy routes are correct (API, dashboard, Authentik)
- [ ] SSL/TLS configuration is secure (TLS 1.2+, strong ciphers)
- [ ] Gzip compression enabled for text/JS/CSS/JSON
- [ ] Static file caching headers set
- [ ] Rate limiting at nginx level
- [ ] Request size limits configured

### 13C. CI/CD (`.github/`)

- [ ] GitHub Actions workflows are correct
- [ ] Build, test, lint steps configured
- [ ] Deployment pipeline works
- [ ] Secret management via GitHub Secrets

### 13D. Monitoring & Observability

- [ ] Application logs are structured (structlog)
- [ ] Error tracking configured
- [ ] Performance metrics available
- [ ] Alerting for critical failures

**Deliverable**: `AUDIT_INFRASTRUCTURE_REPORT.md`

---

## PHASE 14: RELEASE & DEPLOY

**Agents**: `release-manager` (lead), `project-manager` (support)

### 14A. Commit Strategy

- Group related fixes into logical commits
- Conventional commit format: `fix:`, `feat:`, `docs:`, `refactor:`, `style:`, `test:`, `chore:`
- Each commit atomic and independently deployable
- NEVER commit: `.env`, credentials, debug code, `node_modules`

### 14B. Pre-Deploy Checklist

- [ ] All backend tests pass (1,519+)
- [ ] Frontend builds without errors (`npx next build`)
- [ ] Lint passes (ruff + next lint)
- [ ] WordPress plugin builds successfully
- [ ] Mobile app compiles
- [ ] All documentation updated (README, CLAUDE.md, CHANGELOG, docs/)
- [ ] CHANGELOG.md has new audit section
- [ ] No TODO/FIXME/HACK left from audit work
- [ ] All `.env.example` variables documented
- [ ] Alembic migrations forward-compatible
- [ ] No secrets in committed code (scan with `git log --all -p | grep -i "sk-"`)

### 14C. Deployment

```bash
# Production deploy to AWS EC2
PEM="path/to/ecosire.pem"

# Pull latest
ssh -i "$PEM" ubuntu@54.197.92.23 "cd /opt/ace/ai-content-engine/ai-content-engine && git pull"

# Build and deploy all containers
ssh -i "$PEM" ubuntu@54.197.92.23 "cd /opt/ace/ai-content-engine/ai-content-engine/docker && \
  docker compose --env-file ../.env -f docker-compose.prod.yml build api dashboard celery-worker celery-beat && \
  docker compose --env-file ../.env -f docker-compose.prod.yml up -d && \
  docker compose --env-file ../.env -f docker-compose.prod.yml exec api alembic upgrade head && \
  docker exec ace-nginx nginx -s reload"
```

### 14D. Post-Deploy Verification

- [ ] API health check: `curl https://api.ecosir.ai/health` returns 200
- [ ] Dashboard loads: `https://ecosir.ai` renders correctly
- [ ] Authentication works: can sign in via Authentik OIDC
- [ ] Scanner works: can trigger a scan on a test site
- [ ] Content generation: can generate a test article
- [ ] WordPress plugin: can connect from a WP site
- [ ] Celery workers: tasks are being processed (check `docker logs ace-celery-worker`)
- [ ] No error spikes: `docker logs ace-api --tail 100` shows no 500s
- [ ] SSL certificate valid: check Cloudflare SSL status
- [ ] Redis connected: cache operations working

### 14E. Release

- [ ] Create git tag: `git tag -a v3.0.0 -m "Comprehensive audit release"`
- [ ] Push tag: `git push origin v3.0.0`
- [ ] Update CHANGELOG.md with release date
- [ ] Create `RELEASE_NOTES.md` with all fixes, enhancements, breaking changes
- [ ] Notify stakeholders

**Deliverable**: Production deployment confirmed + `RELEASE_NOTES.md`

---

## EXECUTION PLAN

### Parallel Execution (Phases 1-10 can run simultaneously)

```
Wave 1 (Parallel - All Independent Audits):
├── Phase 1:  backend-dev + api-architect + data-engineer + bug-hunter
├── Phase 2:  frontend-dev + bug-hunter
├── Phase 3:  design-auditor + ux-designer
├── Phase 4:  wp-specialist
├── Phase 5:  mobile-dev
├── Phase 6:  ai-integrator
├── Phase 7:  security-auditor
├── Phase 8:  seo-specialist + ai-integrator
├── Phase 9:  code-reviewer + performance-optimizer
└── Phase 10: doc-writer + researcher

Wave 2 (After Wave 1 - Needs Findings):
├── Phase 11: researcher + project-manager + performance-optimizer
└── Phase 13: devops-ace

Wave 3 (After All Fixes Applied):
└── Phase 12: qa-engineer + fullstack-tester

Wave 4 (After Tests Pass):
└── Phase 14: release-manager + project-manager
```

### Reporting Format (Every Agent)

```markdown
## [Phase Name] Audit Report — [Agent Name]

### Summary
- Total issues found: X
- Critical: X | High: X | Medium: X | Low: X
- Fixed: X | Deferred: X | Won't fix: X

### Issues Found
| # | Severity | File:Line | Description | Status |
|---|----------|-----------|-------------|--------|
| 1 | CRITICAL | path/to/file.py:123 | Description | FIXED |

### Changes Made
- [commit hash] fix: description of change

### Remaining Recommendations
1. Recommendation with rationale
```

### File Ownership (Conflict Prevention)

| Agent | Owns |
|-------|------|
| backend-dev, api-architect, data-engineer | `backend/` |
| frontend-dev | `dashboard/` |
| wp-specialist | `wordpress-plugin/` |
| mobile-dev | `mobile/` |
| ai-integrator | `sdks/`, `edge/`, `backend/app/static/tracker.js` |
| seo-specialist | `backend/app/seo_constants.py`, `agents/runtime/knowledge/` |
| devops-ace | `docker/`, `.github/` |
| doc-writer | `docs/`, `README.md`, `CLAUDE.md`, `CHANGELOG.md` |
| Shared files | Primary owner edits, others propose via report |

---

## SUCCESS CRITERIA

1. **Zero Critical/High bugs** remaining in any component
2. **All 1,519+ backend tests pass** with >85% coverage
3. **Frontend builds** with zero TypeScript/lint errors
4. **Every dashboard page works** on desktop (1920-1024px) and mobile (375-414px)
5. **Dark mode works** on every page without visual artifacts
6. **WordPress plugin builds** and works end-to-end
7. **Mobile app compiles** and all 6 screens render correctly
8. **All 3 SDKs** have correct types, retry logic, and error handling
9. **Security audit passes** OWASP Top 10 with no critical findings
10. **Documentation is 100% accurate** — every feature, command, config documented
11. **Enhancement roadmap** is prioritized (P0-P3) and actionable
12. **Production deployment succeeds** with all post-deploy checks passing

---

*Generated for Ecosire.ai on 2026-03-07. Tailored to the exact 22-agent team, codebase structure, tech stack, and feature set.*
