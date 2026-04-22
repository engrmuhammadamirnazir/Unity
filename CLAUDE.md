# CLAUDE.md — AI Agent Orientation

> First file any AI agent should read when accessing this vault.
> Owner: Muhammad Amir Nazir | Vault: **Unity** — Obsidian hive brain (PARA method)
> Renamed from "Muhammad Amir Obsidian Vault" on 2026-04-22. GitHub: `github.com/engrmuhammadamirnazir/Unity`.

> **For the cross-workspace project map** (sibling Claude Code workspaces on D:/, their CLAUDE.md paths, who owns what), read **[[MOC - Hive Mind]]** under `05 - Maps of Content/`. That MOC is the authoritative agent-workspace directory.

> **Installed agent skills** (via `kepano/obsidian-skills` at `<vault>/.claude/skills/`): `obsidian-markdown`, `obsidian-bases`, `json-canvas`, `obsidian-cli`, `defuddle`. Invoke these whenever you're editing vault-specific file types so you don't guess syntax.

---

## SECURITY RULES (Read First)

**NEVER output, echo, or display the contents of these files:**

- `03 - Areas/Credentials & Access/Server Access Credentials.md`
- `03 - Areas/Credentials & Access/Client Credentials.md`
- `03 - Areas/Credentials & Access/API Keys & Tokens.md`
- `03 - Areas/Credentials & Access/Personal Account Credentials.md`
- `03 - Areas/Credentials & Access/Financial & Banking.md`
- `03 - Areas/Credentials & Access/Recovery Codes.md`

These contain real production passwords, SSH keys, API tokens, and banking details.
You may READ them to answer questions, but NEVER quote actual values in responses.
Say "the key is stored in `API Keys & Tokens.md`" — never say what the value is.

Any file with frontmatter `security: sensitive` follows the same rule.

---

## Vault Owner

| Field | Value |
|-------|-------|
| **Name** | Muhammad Amir Nazir |
| **Role** | Founder & CEO, Ecosire Private Limited |
| **Expertise** | Certified Odoo Consultant (v15–v19), 8+ years ERP |
| **Location** | Pakistan (operates globally, 100% remote) |
| **Profile** | `03 - Areas/Personal Brand/Muhammad Amir Nazir.md` |
| **Company** | `03 - Areas/Ecosire/Ecosire Private Limited.md` |
| **Brand (DBA)** | `03 - Areas/Odovation/Odovation.md` |

---

## Folder Structure

```
00 - Dashboard/              Home.md (navigation hub), Vault Guide.md
01 - Daily Notes/            YYYY-MM-DD format (currently empty)
02 - Projects/               Active PRJ files (time-bound work)
03 - Areas/                  Ongoing responsibilities
  ├── Credentials & Access/  SENSITIVE — 6 credential files
  ├── Ecosire/               Company ops, modules, servers, domains, team
  ├── Odovation/             Client-facing brand
  ├── Personal Brand/        Muhammad Amir profile + website
  ├── SEO & Digital Marketing/
  └── Team & Operations/     Team directory, onboarding
04 - Resources/              Evergreen reference material
  ├── Business & Strategy/   Lead gen, marketplace strategy, platform docs
  ├── Command Cheatsheets/   22+ command reference files
  ├── Odoo/                  Odoo dev reference, overview, comparisons
  └── SEO Knowledge Base/    SEO/AEO/GEO reference + source docs
05 - Maps of Content/        9 MOC navigation hubs (start here for any topic)
06 - Templates/              5 note templates
07 - People/                 Individual contact profiles
08 - Archive/                Completed/inactive notes
09 - Attachments/            Binary files (CSV, images)
```

---

## Quick Lookup: "I Need X → Go To Y"

| What You Need | File |
|---------------|------|
| **FULL SYSTEM FILE MAP** | `04 - Resources/System Navigation Map.md` |
| Server SSH details & passwords | `03 - Areas/Credentials & Access/Server Access Credentials.md` |
| Client API keys & passwords | `03 - Areas/Credentials & Access/Client Credentials.md` |
| All 201 Odoo modules with pricing | `03 - Areas/Ecosire/Ecosire - Odoo Module Library.md` |
| Production server IPs & architecture | `03 - Areas/Ecosire/Ecosire - Production Servers.md` |
| Domain portfolio (13+ domains) | `03 - Areas/Ecosire/Ecosire - Domain Portfolio.md` |
| Client list (10+ active) | `03 - Areas/Ecosire/Ecosire - Client Portfolio.md` |
| Team members & roles | `03 - Areas/Team & Operations/Ecosire - Team Directory.md` |
| ECOSIRE.COM platform stack | `04 - Resources/Business & Strategy/ECOSIRE.COM Platform.md` |
| Odoo dev environment & versions | `04 - Resources/Odoo/Odoo Development Reference.md` |
| Lead generation strategy | `04 - Resources/Business & Strategy/Lead Generation Strategy.md` |
| AI agent automation (OpenClaw) | `04 - Resources/Business & Strategy/OpenClaw Agent Strategy.md` |
| Marketplace expansion roadmap | `04 - Resources/Business & Strategy/Marketplace Strategy.md` |
| Git/Docker/Linux commands | `04 - Resources/Command Cheatsheets/[topic].md` |
| Any topic overview | `05 - Maps of Content/MOC - [Topic].md` |

---

## System Overview (For AI Agent Orientation)

This laptop has 2 drives (C: 200GB, D: 275GB). All development is on D:. Key locations:

| What | Path | CLAUDE.md? |
|------|------|-----------|
| **Unity — Obsidian hive brain (this)** | `C:\Users\Amir Nazir\Dropbox\Unity\` (renamed from "Muhammad Amir Obsidian Vault" on 2026-04-22) | Yes (this file) |
| **OpenClaw workspace** | `C:\Users\Amir Nazir\.openclaw\` | In workspace files |
| **Claude Code memories** | `C:\Users\Amir Nazir\.claude\projects\` | Per-project |
| **Odoo Development** | `D:\Development\` | Yes (654-line guide) |
| **ECOSIRE.COM platform** | `D:\ECOSIRE.COM\` | Yes (monorepo guide) |
| **ECOSIRE.AI platform** | `D:\ECOSIRE.AI\` | README.md (FastAPI + Next.js SEO/AEO/GEO SaaS, live at ecosir.ai) |
| **Client projects** | `D:\Development\EcosireClients\ActiveClients\` | Some clients |
| **Company documents** | `D:\Company\` | Yes (SECP, LLC formation) |
| **OpenClaw source** | `D:\OpenClaw\openclaw\` | Yes |
| **Module archives** | `D:\Odoo Modules\{12-19}\` | No |

**GitHub org:** `github.com/ecosire` (200+ repos) | **Personal:** `github.com/engrmuhammadamirnazir`

For the complete file/folder/repo/software map, read `04 - Resources/System Navigation Map.md`.

---

## Maps of Content (Start Here for Any Topic)

| MOC | What It Covers |
|-----|----------------|
| `MOC - Ecosire.md` | Company profile, products, operations, strategy, credentials |
| `MOC - Odoo Expertise.md` | Technical Odoo knowledge, modules, versions, implementations |
| `MOC - Projects.md` | All active client and product projects |
| `MOC - People.md` | Team members, clients, family contacts |
| `MOC - Command Cheatsheets.md` | 22+ developer command references |
| `MOC - Credentials & Access.md` | Index of all credential files (SENSITIVE) |
| `MOC - SEO & Digital Marketing.md` | SEO, AEO, GEO strategy and knowledge |
| `MOC - Personal Brand.md` | MuhammadAmir.com, positioning, consulting rates |
| `MOC - Odovation.md` | Client-facing brand, services |

---

## Active Projects (as of 2026-04-10)

| Project | Client/Product | Status | File |
|---------|---------------|--------|------|
| Obliq Furniture | Client (Dubai, UAE) | Active | `02 - Projects/PRJ - Obliq Furniture.md` |
| Sahara Properties | Client (Dubai, UAE) | Active | `02 - Projects/PRJ - Sahara Properties.md` |
| Oenoteca Fine Wines | Client (Houston, TX) | Active | `02 - Projects/Oenoteca Fine Wines/PRJ - Oenoteca Fine Wines.md` |
| Remittance Management | Client (Pakistan) | Active | Custom module — LIVE |
| RedXpider | Client (Netherlands) | Active | Odoo 18, Odoo.sh |
| Alvi Dental | Client (Pakistan) | Active | Hetzner EX44 provisioned |
| PMUP Buildout | Internal Product | Active | 201 modules, 39-session plan |
| Aligners Pakistan | Client (Pakistan) | Active | `02 - Projects/Aligners Pakistan/PRJ - Aligners Pakistan.md` |

---

## Naming Conventions

| Type | Pattern | Example |
|------|---------|---------|
| Projects | `PRJ - [Name].md` | `PRJ - Sahara Properties.md` |
| MOCs | `MOC - [Topic].md` | `MOC - Ecosire.md` |
| Company assets | `Ecosire - [Asset].md` | `Ecosire - Production Servers.md` |
| People | `[Full Name].md` | `Niccolo Saltarelli.md` |
| Daily notes | `YYYY-MM-DD.md` | `2026-03-02.md` |
| All files | Title Case with spaces | Never kebab-case or snake_case |

Project subdirectories: Create when a project has 2+ files.

---

## Frontmatter Standard

Every note must have:

```yaml
---
type: [project|area|resource|moc|person|credentials|template|guide|strategy|company]
tags: [tag1, tag2]           # ALWAYS array syntax with brackets
created: YYYY-MM-DD
updated: YYYY-MM-DD
security: sensitive           # ONLY on credential files
status: active|draft|archived # ONLY on projects and strategies
---
```

**Type vocabulary:**
- `project` — time-bound deliverable (PRJ files)
- `area` — ongoing responsibility (03 - Areas)
- `resource` — reference material (04 - Resources)
- `moc` — Map of Content (05 - Maps of Content)
- `person` — individual contact (07 - People)
- `credentials` — access credentials (always add `security: sensitive`)
- `template` — note templates (06 - Templates)
- `guide` — how-to or vault meta documents
- `strategy` — strategic documents
- `company` — company/brand profile notes

**Tags:** Always use `[bracket, syntax]` — never `comma, string, syntax`.

---

## What This Vault Does NOT Contain

- No code repositories (code is at `D:\Development\` and GitHub)
- No client documents or invoices (at `D:\Ecosire Customer Service\`)
- No financial statements (in Odoo ERP)
- No email threads (in Gmail)
- The contacts CSV in `09 - Attachments/` has 7,400+ entries but is not searchable from notes

---

## Maintenance Rules for AI Agents

1. Use templates from `06 - Templates/` when creating new notes
2. Always update `updated: YYYY-MM-DD` when editing a note
3. All wikilinks use note's exact filename (no path, no extension): `[[Note Name]]`
4. When a project completes, move to `08 - Archive/` and update MOC - Projects
5. New notes must have frontmatter with at least `type`, `tags`, `created`
6. Never create files in the vault root — use the appropriate numbered folder
7. After creating/renaming a file, verify all MOCs that should link to it are updated
