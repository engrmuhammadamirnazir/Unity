# EcosireClients — Canonical Client + Company + Document Reference

> Single source of truth for client folder structure, document generator pattern, letterhead, ECOSIRE.COM platform offerings, and D:/Company legal structure. Every agent in `D:/Development`, `D:/ECOSIRE.COM`, `D:/ECOSIRE.AI`, and any future workspace MUST read this file before any client-facing output (proposals, contracts, documents, status letters). Last revised: 2026-05-04.

## Why this exists

Client-facing work spans multiple workspaces and folders. Without a single reference, drift happens fast: stale paths, mismatched letterhead, inconsistent generator code, ECOSIRE.COM offerings missing from proposals. This file consolidates everything an agent needs to produce a correct, consistent client deliverable in any workspace.

## Top level

```
D:/EcosireClients/
├── ActiveClients/        ← live engagements (signed contract, work in progress, deployed, support)
├── ProspectiveClients/   ← active sales pipeline (pre-contract)
├── Archive/              ← inactive / churned / lost
└── Internal-Ecosire-App/ ← Ecosire treated as its own internal client
```

Naming convention for every client folder: **PascalCase-Hyphen**, e.g. `Sahara-Properties`, `Suleman-Remittance`, `Amalfi-Foods`. No spaces. No lowercase. No underscores.

---

## ActiveClients/<Client-Slug>/

For clients with a signed engagement and work in progress.

```
ActiveClients/<Client-Slug>/
├── README.md             ← one-pager: status, primary contact, server, modules deployed, payment status
├── docs/                 ← all client-facing documents (deliverables)
│   ├── plans/            ← implementation plans, sprint plans
│   ├── specs/            ← design specs, requirements docs, gap analyses
│   ├── deploy/           ← deployment runbooks, post-deploy verification
│   └── *.{pdf,docx}      ← deliverables (clarification questions, change orders, status letters)
├── quotations/           ← proposals, change-order quotes, renewal offers (PDF + DOCX pair)
├── memos/                ← internal correspondence and notes about this client
├── shared/               ← documents received FROM the client (use lowercase, never "New Documents Shared By...")
├── credentials.md        ← reference-only: file pointers to Unity vault. NEVER paste values here
└── <module-source>/      ← actual code if maintained per-client (e.g. remittance_management/)
```

**Rules:**
- `docs/` is for documents WE produce for the client. `shared/` is for documents the client sent us.
- Every client-facing document MUST ship as PDF + DOCX with identical filename stems and the canonical ECOSIRE letterhead. See feedback memory `feedback_client_docs_in_client_folder_with_editable_version.md` for the full pattern.
- The generator script (`_generate_<doc-purpose>_<date>.py`) lives next to the deliverables it produces, so the document is reproducible from one place.
- `credentials.md` references Unity vault paths only. Never output credential values.

---

## ProspectiveClients/<Client-Slug>/

For pre-contract leads in the active sales pipeline. Uses a numbered staged structure so the lifecycle is visible from the directory listing.

```
ProspectiveClients/<Client-Slug>/
├── 00-Lead.md            ← one-pager: source, fit score, requirements summary, status
├── 01-Contract/          ← proposals, NDAs, MSAs, signed offer
│   └── _superseded/      ← prior versions (move replaced files here, never delete)
├── 02-Discovery/         ← requirements, demos, gap analysis, clarification documents
├── 03-Deployment/        ← post-signature setup plan, runbooks, prerequisites
├── 04-Credentials/       ← reference-only: file pointers to Unity vault
├── 05-DBDumps/           ← raw imports from client's existing system (legacy data)
└── 06-Migration/         ← upgraded + cleaned databases ready for cutover
```

**Rules:**
- `sales-proposal` agent writes new prospect proposals to `01-Contract/`.
- When a prospect signs and converts to active: move `<Client-Slug>/` from `ProspectiveClients/` to `ActiveClients/`. The numbered subdirs translate naturally (01-Contract → quotations/; 02-Discovery → docs/specs/; 03-Deployment → docs/deploy/; 04-06 → keep as-is or merge into the active-client equivalents).
- Old version of any document goes into `_superseded/`, never deleted, never overwritten in place.

---

## Archive/<Client-Slug>/

Churned / lost / paused clients. Read-only after archiving.

**Rules:**
- Folder name follows the same PascalCase-Hyphen convention as active/prospective.
- Add `archived_YYYY-MM-DD.md` at the root explaining why archived (lost to competitor, project paused, scope canceled, etc.).
- Existing archives in non-canonical naming (lowercase, Title Case With Spaces, ALL-CAPS) are listed below as renames pending — do not touch without user approval.

**Pending Archive renames (deferred):**
- `Ahad's Customer` → `Ahad-Customer`
- `Blue` → keep
- `bsdteck` → `BSDTeck`
- `carlos` → `Carlos`
- `cc` → `CC`
- `Customers` → `Customers` (keep, it's a generic dump)
- `dynasty` → `Dynasty`
- `ecosire` → `Ecosire-Old`
- `Fleet Management` → `Fleet-Management`
- `iqa` → `IQA`
- `Odoo DTS Connector` → `Odoo-DTS-Connector`
- `OENECAWINES` → `Oenoteca-Wines-Old`
- `Sky ERP SaaS` → `Sky-ERP-SaaS`
- `Update` → keep or rename if context known
- `Vinit` → keep
- `VoipBilling.Net` → `VoipBillingNet`
- `xrero` → `Xrero`

---

## Internal-Ecosire-App/

Ecosire's own internal "client". Currently sparse (just `credentials.md` and `security_model.md`). Treat the same as an ActiveClient: `docs/`, `quotations/` (won't have any), `memos/`.

---

## Document generator — canonical pattern

Every client-facing PDF + DOCX pair is produced by a single Python generator script colocated with the deliverables it produces (filename pattern `_generate_<doc-purpose>_<YYYY-MM-DD>.py` so the underscore prefix sorts above the deliverables and reads as "tooling, not deliverable"). One content model feeds both `build_pdf()` and `build_docx()` so the two formats stay in lock-step — never hand-author one and try to keep the other in sync.

**Reference implementation (use as template — both Development AND ECOSIRE.COM):**
- `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/_generate_clarification_questions_2026_05_04.py` — proven 2026-05-03, 9-page A4, full-page ECOSIRE letterhead behind text on every page, "Answer:" labels with underline rows for Q&A docs.

**Required ingredients (every generator must include all of these):**
- **Page size:** A4 (595.27 × 841.89 pt). NOT US Letter.
- **Letterhead source:** `D:/Company/ECOSIRE (PRIVATE) LIMITED/05 - Brand & Identity/Letterhead/letterhead_full.png` — full-bleed PNG at native aspect 0.707. NEVER split header/footer crops (loses the side decorations).
- **PDF letterhead:** `onPage` callback drawing `letterhead_full.png` at `(0, 0, PAGE_W, PAGE_H)` plus a `c.drawRightString(PAGE_W - MARGIN_R, 60, f'Page {doc.page}')` for page numbering. Body margins: top ≈130pt, bottom ≈100pt, L/R ≈60pt.
- **DOCX letterhead:** behind-text anchored picture in section header. Add inline `run.add_picture(LETTERHEAD_FULL, width=Inches(PAGE_W_IN), height=Inches(PAGE_H_IN))`, then convert the resulting `<wp:inline>` to `<wp:anchor behindDoc="1" layoutInCell="1" allowOverlap="1">` with `<wp:positionH relativeFrom="page">` + `<wp:positionV relativeFrom="page">` both at offset 0, `<wp:wrapNone/>`. Set `section.header_distance = Inches(0); section.footer_distance = Inches(0)`. Verify with zipfile that `behindDoc="1"` appears in `word/header1.xml`.
- **Filename:** `<Client>_<DocPurpose>_<YYYY-MM-DD>.{pdf,docx}` — same stem for both, ISO date so files sort chronologically.
- **Q&A docs:** after each question, render bold/italic "Answer:" + 3–4 underline rows (`"_" * 95`) so the client can type over them OR print + handwrite.
- **Verify before sending:** render page 1 + 2 + last to PNG and Read them — catches letterhead clipping, missing text, broken page breaks. The client should never be the proof-reader.

**Why this matters:** every "looks fine in PDF, broken in DOCX" mistake came from someone writing one and trying to mirror it manually. Single content model, two builders, one letterhead = no drift between formats and no drift between workspaces.

## ECOSIRE service-line catalog (cross-workspace awareness)

When writing a proposal, every agent — in any workspace — must consider ECOSIRE's full offering across both delivery vehicles:

**1. Odoo modules + implementation (D:/Development workspace)**
- 215+ Odoo modules across v17/v18/v19 (App Store catalog)
- App Store module pricing: $249 / $349 / $499 / $599 (vinculum)
- Custom Odoo implementation services (configuration + custom development)
- See `D:/Development/CLAUDE.md` for the current module list and pricing tiers.

**2. ECOSIRE.COM platform (D:/ECOSIRE.COM workspace)**
- Digital storefront at ecosire.com — sells digital products (Odoo modules + SaaS subscriptions)
- Full Odoo 19 Enterprise SaaS hosting (multi-tenant Turborepo at `apps/web` / `apps/api`)
- Sub-brands: Odovation (Odoo-focused brand at port 3010), MuhammadAmir (personal brand at port 3020), Docusaurus docs at 3002
- Tech stack: NestJS 11 + Next.js 16 + Drizzle + PostgreSQL 17 + native auth-core
- See `D:/ECOSIRE.COM/CLAUDE.md` for the platform module list (74+ NestJS modules) and service offerings.

**3. ECOSIRE.AI platform (D:/ECOSIRE.AI workspace)**
- Autonomous SEO/AEO/GEO content platform at ecosire.ai
- FastAPI + Celery + Claude/GPT — separate offering from Odoo modules and ECOSIRE.COM SaaS
- See `D:/ECOSIRE.AI/README.md` for service tiers.

A proposal that only mentions Odoo modules misses upsell into ECOSIRE.COM SaaS hosting and ECOSIRE.AI content services. Always evaluate whether prospect's needs touch all three.

## D:/Company — Legal entities + brand assets (canonical source)

`D:/Company/` is the corporate vault — NOT code, NOT a client folder, but the source of truth for:

**Legal entities:**
- `D:/Company/ECOSIRE (PRIVATE) LIMITED/` — Pakistan entity (SECP-registered, NTN-tax-registered)
  - `01 - Incorporation & Identity/` — director ID, NTN registration, original incorporation papers
  - `02 - SECP Annual Filings/` — FY2022/2023 returns, Form A, Form 29, older acknowledgements
  - `03 - Banking/` — HBL (dormant), UBL (in progress 2026)
  - `04 - Rental Agreement/` — office lease docs
  - `05 - Brand & Identity/` — letterheads, brand assets
- `D:/Company/ECOSIRE LLC/` — Wyoming entity (separate jurisdiction)

**Brand assets (canonical letterhead source):**
- `D:/Company/ECOSIRE (PRIVATE) LIMITED/05 - Brand & Identity/Letterhead/letterhead_full.png` — full A4 (1191×1685, native aspect 0.707), with side decorations and right-side dark wedge. **Use this exclusively** for client docs.
- Also available (legacy / DO NOT use unless specifically requested): `letterhead_header.png`, `letterhead_footer.png` (split crops — they LOSE side decorations), `LetterHead Ecosire Private Limited.pdf` (reference PDF), `Letterhead (Plan Empire legacy).{docx,pdf}` (defunct prior brand).

**Rules:**
- Agents MUST reference `letterhead_full.png` from `D:/Company/...` directly — never copy it into a workspace `assets/` folder (creates drift).
- For proposals citing the company entity (Pakistan vs Wyoming), the agent must specify which entity. Default: ECOSIRE (PRIVATE) LIMITED for Asia/EU clients; ECOSIRE LLC for US clients (verify with user).
- Agents needing tax / banking / corporate-status info read from `D:/Company/ECOSIRE (PRIVATE) LIMITED/0X - <subject>/` directly — never re-state these from memory (they change).
- Confidential. Treat any value as sensitive — do not output incorporation numbers, bank details, or director-personal info inline.

## Server + credentials reference layer (canonical pointers)

ECOSIRE operates production + demo + client servers across Hetzner / DigitalOcean / AWS. Per the rule "credentials live in Unity, references live in client folders" — every server has a single canonical Unity entry, and every client folder references that entry by file path, NEVER by value.

### Demo server (one shared instance for all module demos)

- **Server:** Hetzner CPX42 — 8 vCPU AMD / 16 GB RAM / 320 GB NVMe / Ubuntu 24.04 LTS, $22.59/mo, Helsinki
- **IP:** 37.27.2.10
- **Public:** `demo.ecosire.com` + wildcard `*.demo.ecosire.com` (Cloudflare DNS-only, Let's Encrypt wildcard cert)
- **Architecture:** subdomain-per-platform (`<module>.demo.ecosire.com` → `<module>_demo` Odoo DB via `dbfilter=^%d_demo$`)
- **SSH key:** `hetzner_demo` (ed25519). **Triple-synced** to survive single-file loss:
  - `~/.ssh/hetzner_demo` (primary, 600 perms)
  - `C:/Users/Amir Nazir/Dropbox/Keys/SSH Keys/hetzner_demo` (Dropbox-synced across devices)
  - 1Password vault `IXX3GXRTJ5BIFB66JCVPGF2BLE` item `cbsjap2gfvfx4m54vzya56nulq` (master copy)
  - Plus legacy `D:/tmp/hetzner_demo` (older scripts reference this path)
- **Canonical references:**
  - Unity: `C:/Users/Amir Nazir/Dropbox/Unity/03 - Areas/Credentials & Access/Demo Server SSH Key.md`
  - Memory: `~/.claude/projects/D--Development/memory/demo_server.md` (live operational state — paths, DBs, upgrade flow)
- **Status:** Live — 213 addons, 203 demo databases, lead capture wired into CRM at `api.ecosire.com/api/crm/capture`

### Client servers

Every active client has either a self-managed server OR a server we provision. Each MUST appear in:
1. **Unity** `03 - Areas/Credentials & Access/Server & Key Inventory.md` — single canonical row per server with: client, hostname, IP, provider, OS, SSH key file, primary user, login URL, role (prod / staging / dev / demo), status (live / suspended / decommissioned)
2. **Unity** `03 - Areas/Credentials & Access/SSH Keys/<client>.pem` — the actual key file (gitignored, Dropbox-only)
3. **Unity** `03 - Areas/Credentials & Access/Client Credentials.md` and/or `Server Access Credentials.md` — admin password references
4. **Project memory** `~/.claude/projects/D--Development/memory/<client>_server.md` — live operational context (deploy flow, DBs, modules installed, recent migrations, common gotchas)
5. **Client folder** `D:/EcosireClients/ActiveClients/<Client-Slug>/credentials.md` — pointer-only file: `See Unity: 03 - Areas/Credentials & Access/Server & Key Inventory.md row "<Client-Slug>"` — never the value

### Inline-credential cleanup needed (legacy, 2026-05-04)

Some active clients still have actual credential files saved inside their D:/EcosireClients/ folder rather than pointer files:
- `D:/EcosireClients/ActiveClients/Diamond-Investment-Group/04-Credentials/ODOO_LOGIN_CREDENTIALS.md` + `OLD_SHARED_HOSTING_CREDENTIALS.md`
- `D:/EcosireClients/ActiveClients/Sahara-Properties/docs/Sahara_User_Credentials.pdf`
- `D:/EcosireClients/ActiveClients/Suleman-Remittance/Remittance_System_Access_Credentials.{docx,pdf}`

**Migration plan (not yet done — flagged for next session):** for each, copy values into Unity `Server Access Credentials.md` / `Client Credentials.md` / `SSH Keys/`, then replace the in-folder file with a pointer-only `credentials.md`. Until migrated, these files are at risk if D: dies and client folder is not backed up off-machine.

## Durability & Redundancy — single-failure-survival inventory

The user's hard rule (2026-05-04): **"Don't lose any keys, information, or anything. A single accident must not bring us down."** This means every load-bearing artifact must survive any single device / drive / account / network failure.

| Asset | Primary location | Backup 1 | Backup 2 | Catastrophic-loss recovery time |
|-------|------------------|----------|----------|---------------------------------|
| **Unity vault** (creds inventory, MOC, session log) | Dropbox `C:/Users/Amir Nazir/Dropbox/Unity/` | GitHub `engrmuhammadamirnazir/Unity` (daily 23:50 push) | Multiple devices via Dropbox sync | Minutes (clone Unity GitHub repo to any device) |
| **SSH keys** (hetzner_demo + per-client) | `~/.ssh/` | Dropbox `Keys/SSH Keys/` | 1Password vault | Minutes (Dropbox sync or 1Password export) |
| **Workspace `.claude/`** (agents, skills, hooks, rules, memories) | Each `D:/<workspace>/.claude/` | `~/.claude/workspaces/<workspace>/.claude/` (daily mirror) | GitHub `engrmuhammadamirnazir/claude` (daily push at 23:50) | Hours (re-clone or copy from mirror) — **proven 2026-05-04 recovery via USB after 2-day workspace loss** |
| **Memory files** (`~/.claude/projects/.../memory/`) | Local `~/.claude/projects/` | Daily backup_all_workspaces.py copies into `~/.claude/workspaces/` mirror | GitHub `engrmuhammadamirnazir/claude` push | Hours |
| **D:/Development module source** (live editable copy) | `D:/Development/odoo{17,18,19}/server/addons/` | `D:/Development/EcosireSolutions/<Module>/<module>/` (mirror staging) | GitHub `github.com/ecosire/<module>` (one repo per module) | Minutes per module — `git clone` |
| **Client folders** (`D:/EcosireClients/`) | D: drive | **AT RISK** — no daily backup if D: dies | None today | Days — would need rebuild from Unity + memories + GitHub remotes |
| **D:/Company legal vault** | D: drive | **AT RISK** — no automated off-machine backup | None today | Days — depending on what's lost (incorporation papers etc. are in physical form too) |
| **Letterhead `letterhead_full.png`** | `D:/Company/.../Letterhead/` | No off-machine copy | — | Re-recreate from PDF (`LetterHead Ecosire Private Limited.pdf` is also there, but if D: dies both are gone) |
| **D:/EcosireClients pre-launch business plans** | D: drive | **AT RISK** — no off-machine backup | — | Days — would need to re-author |

### Action items for full redundancy (deferred — flag for user)

These gaps came up 2026-05-04 and should be addressed before another drive failure or accidental wipe:

1. **`D:/EcosireClients/` daily backup** — push to a private GitHub repo (engrmuhammadamirnazir/clients-archive or similar). Many client folders contain non-source artifacts (PDFs, DOCXs, Excel models) that aren't in any other repo. If D: dies, these are lost.
2. **`D:/Company/` daily backup** — same. Legal vault is high-value-low-volume; should be in encrypted GitHub or B2/S3.
3. **Letterhead source** — copy `letterhead_full.png` to Unity `04 - Resources/Brand Assets/` so it rides Unity's daily push.
4. **Inline-credential migration** — see "Inline-credential cleanup needed" above. Move 4 client credential files into Unity, leave pointer-only files behind.
5. **Periodic full-disk image** — quarterly external-USB image of D: covers everything in one shot. Already used 2026-05-04 to recover the .claude fleet.
6. **PORTFOLIO + READMEs in Unity** — copy `D:/Company/PORTFOLIO.md` and `D:/EcosireClients/README.md` into Unity weekly so the canonical references exist outside D:.

End-session skill (Step 9 verification) should glance at the daily-backup log and flag any push failures so silent drift is detected within 24h.

## Cross-references

- **Agents that produce client deliverables (any workspace):** `sales-proposal` (D:/Development — writes to `01-Contract/` for prospects, `quotations/` for active renewals), `documentation-writer`, `client-ops`, `functional-consultant`, `project-manager`, `opportunity-scout`. ECOSIRE.COM has analogous `documentation-writer`, `project-manager`, `business-strategist`. ALL of these read this file before producing client output.
- **Feedback memory (full pattern):** `~/.claude/projects/D--Development/memory/feedback_client_docs_in_client_folder_with_editable_version.md` — exact XML for `behindDoc="1"` anchored picture in DOCX, full PDF letterhead spec.
- **Reference generator script:** `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/_generate_clarification_questions_2026_05_04.py` — proven 2026-05-03, copy and adapt for new Q&A docs.
- **Holding-company portfolio (CANONICAL for cross-business awareness):** `D:/Company/PORTFOLIO.md` — full list of 7 ECOSIRE business lines (Odoo Modules + Implementation, ECOSIRE.COM SaaS, ECOSIRE.AI content platform, ECOSIRE.IO managed hosting, ADNET ad network, Dairy Business pre-launch, OpenClaw AI agents), legal-entity selection rules, cross-business dependencies, pending-launch attention items. Read this when writing any proposal so cross-sell and entity-selection decisions are correct.

---

## What NOT to do

- ❌ Save client deliverables to `D:/Development/tmp/` — they get lost, not part of the audit trail.
- ❌ Save proposals to `D:/Development/Quotations/` — that path is **deprecated**; use `EcosireClients/<Active|Prospective>/<Client>/{quotations,01-Contract}/`.
- ❌ Mix multiple clients into one folder.
- ❌ Save without a PDF+DOCX pair when the deliverable is client-facing.
- ❌ Use spaces, ALL-CAPS, or lowercase in client folder names. PascalCase-Hyphen always.
- ❌ Output credential values inline. Always reference Unity vault by path.
- ❌ Delete superseded versions of contracts/proposals — move them to `_superseded/`.
