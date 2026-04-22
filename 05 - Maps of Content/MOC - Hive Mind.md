---
type: moc
tags: [moc, hive-mind, claude-code, agents, workspaces, navigation]
aliases: [Hive Mind, Agent Workspace Map, Project Directory]
created: 2026-04-22
updated: 2026-04-22
---

# MOC — Hive Mind (Agent Workspace Map)

> **Unity is the hive brain.** This map tells any Claude Code agent — in any project — where the canonical workspaces live on this machine, what each one does, where its docs are, and how they cross-reference. Read this before doing cross-workspace work.

Renamed from "Muhammad Amir Obsidian Vault" to **Unity** on 2026-04-22. GitHub repo: `github.com/engrmuhammadamirnazir/Unity`.

---

## Canonical Project Workspaces (D: drive)

Each row is an independent Claude Code working directory with its own `CLAUDE.md`. The agent that opens that directory will see only its own files — this table tells you what else exists.

| # | Workspace | Path | Canonical docs | Purpose |
|---|-----------|------|----------------|---------|
| 1 | **Odoo Development** (primary) | `D:\Development\` | `CLAUDE.md` + `~/.claude/projects/D--Development/memory/` (MEMORY.md index) | 215+ Odoo modules across v17/v18/v19, agent fleet v11.1 (27 agents, 101 skills, 16 teams), platform-api-intelligence, EcosireSolutions publishing folder, client-ops for Obliq/Sahara/Oenoteca/Remittance/Alvidental/Diamond/Amalfi |
| 2 | **ECOSIRE.COM** | `D:\ECOSIRE.COM\` | `CLAUDE.md` + `memory/ecosire_website.md` | Turborepo monorepo: NestJS 11 API + Next.js 16 web + Drizzle + PostgreSQL 17. Live at ecosire.com. 5 apps (web/api/docs/odovation/muhammadamir), 7 shared packages. Pricing/HTML assets sync from `D:/Development`. |
| 3 | **ECOSIRE.AI** | `D:\ECOSIRE.AI\` | `README.md` + `memory/ecosire_ai.md` | Autonomous SEO/AEO/GEO SaaS: FastAPI + Next.js 16 + PostgreSQL 16 + Celery + Claude/GPT. Live at ecosire.ai. 11 Docker containers on EC2 t3.large. WP plugin v2.1.0. **NOT** OpenClaw AI. |
| 4 | **ECOSIRE.IO** | `D:\ECOSIRE.IO\` | `CLAUDE.md` | Managed Odoo/ERPNext hosting SaaS — competes with Cloudpepper.io and odoo.sh. Domains: ecosire.io (marketing), app.ecosire.io, api.ecosire.io. Private repo. |
| 5 | **Company legal vault** | `D:\Company\` | `CLAUDE.md` + `memory/company_legal.md` | NOT code. Corporate/legal docs for ECOSIRE (PRIVATE) LIMITED (Pakistan SECP) + ECOSIRE LLC (Wyoming). Letterheads, SECP filings, bank docs, brand assets. **Confidential — never quote values.** |
| 6 | **OpenClaw source** | `D:\OpenClaw\openclaw\` | `AGENTS.md` + per-module `CLAUDE.md` | OpenClaw AI product source (separate from Odoo-embedded OpenClaw module inside `D:/Development`). |
| 7 | **EcosireClients** | `D:\EcosireClients\` | per-client `CLAUDE.md` under `ActiveClients/<Client>/` | Client project workspaces: Sahara Properties, Ai Content Automation, Redxpider, Diamond/STIG, Amalfi Foods, etc. SSH keys + per-client runbooks live under each subfolder. |
| 8 | **Ad Network Website** | `D:\Ad Network Website\` | `docs/` | Notes + spec for an ad-network site project. No code yet. |
| 9 | **Office** | `D:\Office\` | `CLAUDE.md` | Pixel-art virtual office visualization of 21 ECOSIRE agents across 6 departments. `gen.js` produces `pixel-agent-office.html`. |
| 10 | **Unity (this vault)** | `C:\Users\Amir Nazir\Dropbox\Unity\` | `CLAUDE.md` + `.claude/skills/` (kepano/obsidian-skills) | Obsidian hive brain. PARA method. Canonical source for credentials, MOCs, production server runbooks, team directory, client portfolio. Renamed 2026-04-22. |

Also:
- **OdooModules archive** — `D:\OdooModules\` — 69GB library of 5,500+ competitor Odoo modules v12-19 for reference. Read-only study material.
- **ECOSIRE.AI-worktrees** — `D:\ECOSIRE.AI-worktrees\` — git worktrees for parallel ECOSIRE.AI work.

---

## Claude Code Per-Project Memory Directories

Each workspace has an independent memory dir at `C:\Users\Amir Nazir\.claude\projects\<slug>\memory\`. The slug is the workspace path with `\` → `-`. When a vault folder is renamed, **the slug directory must be renamed too** or memory is orphaned (learned 2026-04-22 during the Unity rename).

| Slug | Maps to |
|------|---------|
| `D--Development` | `D:\Development\` |
| `D--ECOSIRE-COM` or `D--ECOSIRE.COM` | `D:\ECOSIRE.COM\` (two slugs exist — merge if needed) |
| `D--ECOSIRE.AI` or `D--ECOSIRE-AI` | `D:\ECOSIRE.AI\` (two slugs exist — merge if needed) |
| `D--ECOSIRE-IO` | `D:\ECOSIRE.IO\` |
| `D--Company` | `D:\Company\` |
| `D--OpenClaw-openclaw` | `D:\OpenClaw\openclaw\` |
| `D--Office` | `D:\Office\` |
| `D--Ad-Network-Website` | `D:\Ad Network Website\` |
| `D--Ai-Content-Automation-Engine` | Old path — migrated into ECOSIRE.AI |
| `D--Ecosire-Customer-Service-Active-Clients*` | Old client folder paths (legacy) |
| `C--Users-Amir-Nazir-Dropbox-Unity` | `C:\Users\Amir Nazir\Dropbox\Unity\` (renamed 2026-04-22 from `*-Muhammad-Amir-Obsidian-Vault`) |
| `C--Users-Amir-Nazir-Dropbox` | Parent Dropbox workspace (has broader-scope memory) |
| `C--Users-Amir-Nazir-Dropbox-ONETECA-FINE-WINES` | Oenoteca client data folder |

---

## Sibling Relationships & Rules

- **Don't edit sibling code from the wrong workspace.** Each has its own repo, tests, CI, deploy. If you're in `D:\Development` and the task is about `ecosire.com`, hand off or open the sibling CLAUDE.md first.
- **Pricing + HTML assets** sync one-way: `D:\Development\` → `D:\ECOSIRE.COM\` via `_update_product_prices.mjs` and related scripts. Module catalog changes need website DB sync.
- **OpenClaw AI ≠ ECOSIRE.AI.** OpenClaw AI is the agent-swarm product sourced at `D:\OpenClaw\openclaw\` (and an Odoo module inside `D:/Development`). ECOSIRE.AI is the SEO/AEO/GEO content platform at `D:\ECOSIRE.AI\`. Keep them straight.
- **Credentials** always live under Unity's `03 - Areas/Credentials & Access/` — never output values, never hard-code, never commit.
- **Any client-facing artifact** (proposals, invoices, letters, engagement docs) must go through the Ecosire letterhead tooling in `D:\Company\Signatures\` (`ecosire_sign_pdf.py`) + the canonical invoice template in `D:\Development\scripts\client_docs\ecosire_invoice_template.py`.

---

## Production Servers (quick lookup)

Full list in [[Ecosire - Production Servers]]. Canonical keys in Unity `03 - Areas/Credentials & Access/`.

| Name | IP | Role | Key |
|------|----|------|----|
| ECOSIRE.COM | 13.223.116.181 | Platform (PM2 + Docker) | ecosire.pem |
| ECOSIRE.AI | 54.197.92.23 | 11-container AI engine | ecosire.pem |
| Sahara Properties | 3.232.201.222 | Bitnami Odoo 19 | `D:/EcosireClients/ActiveClients/Sahara-Properties/sahara.pem` |
| Oenoteca (Hetzner) | per session notes | Isolated Odoo 19 | session memory |
| Obliq (Hetzner) | per session notes | Isolated Odoo 19 | session memory |
| Remittance (prod) | 52.28.45.137 | Bitnami Odoo 19 Enterprise | session memory |
| Demo server | 37.27.2.10 | Subdomain-per-platform demos | `~/.ssh/hetzner_demo` |
| Diamond/STIG | 91.99.75.227 | Hetzner Odoo 19 CE + PG15 | session memory |
| Quicken | 5.78.207.108 | Docker Odoo 19 | `ecosire_main` key |

---

## Agent Skills Installed in This Vault

`kepano/obsidian-skills` cloned to `<vault>/.claude/` on 2026-04-22. Any agent opening the vault sees:

| Skill | Use when |
|-------|---------|
| `obsidian-markdown` | Writing any `.md` with Obsidian-specific syntax (wikilinks, callouts, embeds, properties) |
| `obsidian-bases` | Creating or editing `.base` files (views, filters, formulas, summaries) |
| `json-canvas` | Creating or editing `.canvas` files (nodes, edges, groups) |
| `obsidian-cli` | Interacting with this vault via the Obsidian CLI (plugin/theme dev) |
| `defuddle` | Extracting clean markdown from web pages |

---

## How Agents Should Use Unity

1. **Before starting cross-workspace work:** open this MOC to find the right sibling workspace and its canonical CLAUDE.md.
2. **Before asking for a credential:** read `03 - Areas/Credentials & Access/` — never output values.
3. **Before writing canonical client/server facts:** put them in Unity (not scattered across per-project memories). Per-project memories should only hold work that is local to that codebase.
4. **Before renaming any workspace:** read the 2026-04-22 Unity rename session memory — there are 5 failure modes (stale memory paths, orphaned Claude slug dirs, Dropbox sync lag, vault-internal path refs, GitHub remote URL drift).
5. **When adding a new workspace:** add a row to the table above the same day, and set up a `CLAUDE.md` in the new workspace that links back here: `See [[MOC - Hive Mind]] in Unity vault`.

---

## Cross-Links

- [[CLAUDE.md]] (vault root) — vault conventions, security rules
- [[Home]] — daily navigation
- [[MOC - Ecosire]] — Ecosire corporate/company map
- [[MOC - Projects]] — active project list
- [[MOC - Command Cheatsheets]] — server/git/SSH commands
- [[MOC - Credentials & Access]] — pointer to secure credential area
- [[System Navigation Map]] — machine-wide file/folder/repo inventory
- [[Claude Code Unified Memory]] — consolidated technical patterns and gotchas (older doc; this MOC is newer and more project-oriented)
