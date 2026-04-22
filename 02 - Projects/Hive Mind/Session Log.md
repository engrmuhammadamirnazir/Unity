---
type: log
tags: [hive-mind, session-log, append-only, cross-project]
aliases: [Hive Mind Log, Agent Session Journal]
created: 2026-04-22
updated: 2026-04-22
---

# Hive Mind — Session Log (Append-Only)

> Every Claude Code session — in any workspace — writes a 1–5 line entry here on close via the `hive-mind-sync` skill. Entries are reverse-chronological. This file is the single thread that connects all project workspaces into one continuous conversation for future agents.

## How to write an entry

Keep entries tight. Format:

```
### YYYY-MM-DD — Workspace name — one-line headline
- What was done (1–3 lines, outcomes not process)
- Cross-project impact, if any (mentions of other workspaces)
- Canonical facts promoted to Unity: [[wiki-links to updated notes]]
```

## What belongs here (vs. project-local memory)

**Write here** when the work touches more than one workspace, produces a canonical fact (client/server/credential), or would be useful to an agent in a DIFFERENT project next week.

**Keep in project-local memory** when the work is only about that codebase's internals — a fix to a specific file, a local test failure, a refactor inside one module. Those stay in `~/.claude/projects/<slug>/memory/`.

---

## Log (newest first)

### 2026-04-22 (late night) — D:/Development (Quicken Accounting / Wayfair) — v19.0.3.11.0 native Deduction/chargeback parsing shipped + prod payout rescued
- **Root bug:** `edi_parser.parse_payment_remittance` stopped at `Sub-total:` and let `Deduction,"PO#",date,…,amount` blocks leak into the invoice-line parser as phantom `chargeback` rows. Result: strict balance check failed (`expected_net − header_net` = sum of missing deductions ≠ 0), `_post_settlement_entries` rejected, payouts stuck in `imported`.
- **Fix shipped (v19.0.3.11.0):** new `_parse_deduction_blocks` helper emits one `adjustment` txn per chargeback with CreditID → `source_order_transaction_id` + Reason/Item/Memo/Desc → `note`; new Step 5b in `_post_settlement_entries` flips `reconciled=True, posting_status=posted` on every aggregate-booked fee/adjustment/chargeback line (fixes both `reconciliation_rate > 100%` AND deductions stuck showing "Pending" after post). Backported to v18 + v17 (pure-Python parser copied wholesale; surgical Edits elsewhere to avoid the `_sql_constraints` vs `models.Constraint` divergence).
- **Prod (soovah1) live:** module deployed via paramiko SFTP + `docker restart` (no `odoo -u` needed — pure Python patch). Payout 10002004859744 rescued from `imported` limbo: wiped 196 malformed rows, re-parsed to 185 sale + 11 adjustment, `_post_settlement_entries()` produced 188 JEs (185 per-invoice clearing payments + Fees $846.23 + Adjustments $964.27 + Wire $40,489.13). Backfill script retroactively marked aggregate lines reconciled on 3 already-posted payouts. **All 7 Quicken payouts now at 100.00% reconciliation, 0 pending aggregate lines.**
- **GitHub published (all 4 branches):** `ecosire/wayfair_store_management` @ main `b4bfed0` / 19.0 `1c24be8` / 18.0 `20f2c7c` / 17.0 `6b04294`. App Store HTML refreshed.
- **Two reusable fleet gotchas captured:** (1) `odoo -u` inside docker-compose fails with missing socket — pass `--db_host=db --db_port=5432 --db_user=... --db_password=...` explicitly, or just `docker restart` when patch is pure Python. (2) Scripts piped into `odoo shell` via stdin need explicit `from odoo import fields` — interactive shell globals don't propagate into `exec()`ed stdin body.
- Cross-project impact: both gotchas apply to EVERY docker-compose-deployed Odoo 19 tenant (Quicken / Diamond-STIG) and every odoo-shell repair script across the fleet. Saved as `feedback_code_restart_vs_module_update.md` + `feedback_odoo_shell_explicit_imports.md` in D:/Development project memory.
- Canonical facts promoted to Unity: [[Server & Key Inventory]] updated — Quicken row now flags `ecosire_main` as de-authorized (use password until re-appended) and lists Wayfair module @ v19.0.3.11.0.

### 2026-04-22 (night marathon) — D:/Development (Remittance / Suleman Arif) — v19.0.9.0.0 lifecycle-first refactor LIVE on prod + remtest
- **Full 9-phase end-to-end rebuild in a single session** responding to Suleman's morning meeting feedback: "still feels like Excel, need end-to-end tracking, clear all current data, forward only". Brainstorming → spec (883 lines) → plan (4085 lines) → execution → prod deploy.
- **What shipped:** 6 shadow models dropped (`remittance.client/bridge/case/ledger.entry/reconciliation/balance.engine`); 4 new models (`stage.config` with 10-seed gates, `suspense.item` with maker-checker, `flow` SQL-view lens, `account.move.line._inherit` with `remittance_tx_id` tag column); `remittance.transaction` rewritten for 9 kinds + 10-stage Kanban pipeline + dual-currency single-tx (`bank_eur_out_usd_in` — Suleman's "EUR out + Dollar in booked as ONE transaction"); `flow_root_id` recursive compute; GL dispatcher posts real `account.move` per kind; `balance.line` rewritten to read ONLY from `account.move.line` (fixes TXN/2026/01031 invisibility — conversions now emit two rows per partner); 2 new OWL widgets (Flow Timeline vertical timeline + Partner Ledger xlsx-grid with yellow/green/blue cell semantics); 4-role security (Accountant < Operations < Finance/Compliance < Admin); 12 control accounts + per-currency journals seeded per company; menu tree rewritten (My Queue / Flows / Transactions Kanban / Partners / Accounting with Odoo-native reports / Configuration / Help).
- **Both DBs LIVE on `52.28.45.137`** — `remittanceaccounting` (prod) + `remtest` at v19.0.9.0.0. Pre-deploy backups: `remittanceaccounting_pre_v19_0_9_final_20260422_161808.dump` + `remtest_pre_v19_0_9_20260422_145324.dump`. Rollback ready: `/home/bitnami/rollback_v19_0_9.sh`. Data state: 0 transactions (wipe approved), 10 stage_config rows seeded, 134 intermediary partners preserved, HTTP 200 both subdomains. Branch `feat/v19.0.9-lifecycle-first` + tag `v19.0.9.0.0` pushed to `engrmuhammadamirnazir/remittance_management` (PRIVATE) — 10 commits.
- **Two reusable Odoo 19 Enterprise / Bitnami gotchas** — applies to EVERY Bitnami Odoo 19 client (Sahara / Obliq / Oenoteca / Amalfi / Diamond / Alvi / Remittance): (1) Bitnami v19 rejects `migrate(env, version)` — use `migrate(cr, version)` + build env via `api.Environment(cr, SUPERUSER_ID, {})` inside when ORM needed; (2) Enterprise `account.account.create_asset` is NOT NULL varchar with no default and often absent from `_fields` at migration time — always pass `'create_asset': 'no'` unconditionally + savepoint-wrap + raw-SQL fallback.
- Cross-project impact: both gotchas saved as `feedback_bitnami_odoo19_migration_signature.md` + `feedback_enterprise_account_create_asset.md` in D:/Development project memory. Applicable to every Bitnami Odoo 19 client in the fleet — any fleet-wide upgrade work needs to know these.
- Canonical facts promoted to Unity: [[PRJ - Remittance (Suleman Arif)]] README updated with v19.0.9.0.0 LIVE state + new module version + lifecycle-first architecture summary.

### 2026-04-22 (late evening) — D:/Development (Sahara Properties) — res.partner deduplication on prod + testingsahara cloned
- **saharaproperties prod: 247 → 184 partners** (63 losers unlinked — clean delete per user requirement, no archive residue). Built end-to-end reusable pipeline at `D:/EcosireClients/ActiveClients/Sahara-Properties/scripts/partner_dedup/` — normalize/tiers/blocking (30 pytest tests), detect.py (rapidfuzz token_set_ratio + inverted-index blocking), enrich.py (context loader), execute.py (per-pair savepoint + `res.partner._merge_method(destination_rs, source_rs)` + FK-count canary + `unlink()` + chain-map for transitive clusters), verify.py (0 dangling FK pointers confirmed).
- **11 `functional-consultant` subagents dispatched in parallel** to review 102 TIER-2 pairs → 84 MERGE / 9 SKIP / 9 ESCALATE. User resolved escalates: SPECIAL MOMENT EVENTS AND FLOWERS/GC FURNITURE 4-way cluster collapsed to id 86; MUHAMMED/MUHAMMAD ISMAIL + UMMER/UMMAR PUTHAH kept separate (individual names too risky without corroborating data). Final: 63 OK + 25 SKIP_SAME_AFTER_CHAIN + 0 FAILED.
- **testingsahara cloned** from post-merge saharaproperties (drop+recreate+pg_restore+filestore rsync+Odoo restart). Both DBs serve 181 active partners. Backups at `/home/bitnami/backups/saharaproperties_{pre,post}_merge_2026-04-22.pgdump.gz` + local copies at `D:/EcosireClients/ActiveClients/Sahara-Properties/backups/`.
- **Odoo 19 gotchas (reusable for every Odoo 19 client in the fleet):** `res.partner.mobile` removed in v19 (merged into `phone`); Odoo returns `False` not `None` for empty char fields; `_merge_method(destination, source)` needs recordsets not ID lists; `__file__` unavailable when piped to `odoo shell` — hardcode paths.
- Cross-project impact: pipeline is client-agnostic. Applicable to any Odoo 19 tenant with duplicate-partner cleanup needs (Obliq, Diamond/STIG, Amalfi — watch for data-quality candidates). PMS/PDC model names in `enrich.py` would need swapping per-client.
- Canonical facts promoted to Unity: none (saharaproperties DB state change is client-local; pipeline is reusable but lives in client folder, not Unity).

### 2026-04-22 (evening — addendum) — D:/Development (Oenoteca) — CSCS Inv 13972 rebuilt via proper PO→Bill→Pay→LC flow
- **Correction to previous entry below.** Nick called out that direct `in_invoice` creation broke Oenoteca's canonical PO-before-Bill rule. Unwound the direct bill + misc payment JE (sequence numbers reused), rebuilt through:
  - **PO `P00167`** USD $2,332.03 CSCS LLC with landed-cost service products (Freight 454 → 500002, Tariff 1355 → 500001), confirmed.
  - **Bill `BILL/2026/03/0013`** generated via `purchase.order.action_create_invoice`, `ref=13972`, posted.
  - **Payment `MISC/2026/03/0009`** manual misc JE (DR 211000 AP / CR 101401 Bank), reconciled → bill `paid`, residual $0.
  - **Landed Cost `LC/2026/0026`** linked to WH/IN/00048 Pierre Prieur receipt; Freight split `by_quantity` + Tariff `by_current_cost_price`; validated → `STJ/2026/03/0005` capitalized $2,332.03 into Sancerre Blanc inventory.
- **Permanent rule saved** to local memory (`feedback_oenoteca_po_before_bill.md`): every Oenoteca vendor charge MUST go PO → Bill, including freight / duty / services — never `account.move in_invoice` directly.
- Cross-project impact: Odoo 19 Enterprise payment-flow gotcha still stands (previous entry). New gotcha: when a freight-forwarder invoice covers an existing wine PO, the clean pattern is a *separate* PO from the forwarder with landed-cost service products, then a `stock.landed.cost` linking both. Applies to every Oenoteca-style inventory-capitalizing client.
- Canonical facts promoted to Unity: none (Oenoteca-local; rule lives in local project memory, not Unity).

### 2026-04-22 (evening) — D:/Development (Oenoteca production) — CSCS Inv 13972 booked + lot/serial tracking disabled fleet-wide
- **Bill booked + paid:** CSCS LLC Invoice #13972 → `BILL/2026/03/0013` dated 2026-03-18, $2,332.03 ($985 Freight 500002 + $1,347.03 Duty 500001) for PO P00114 Pierre Prieur et Fils (BL# 540600022902 Le Havre→Houston). Payment via manual JE `MISC/2026/03/0009` DR 211000 AP / CR 101401 Bank, reconciled → bill paid.
- **Lot/serial tracking disabled on 17 wine products** (all from Enohance shipment #77) after Nick confirmed he does not track lots/serials anywhere. `WH/OUT/00513` S00343 Uncorked/Altura Wine then validated (15 bottles shipped). 17 existing `stock.lot` rows + 12 lot-linked quants left in place; harmless with tracking=none.
- **Odoo 19 payment-flow gotcha (reusable for any Odoo 19 Enterprise client):** `account.payment.register` wizard can leave the payment in `state='in_process'` with `move_id=False` — the bill sticks at `payment_state='in_payment'`, residual unchanged, because the cash JE is never created. Root cause in Oenoteca's case: BNK1 outbound methods (Manual/Checks/NACHA) have `payment_account_id=False` (no Outstanding Payments account), and the client's actual workflow is direct bank-statement reconciliation without `account.payment` records (other paid bills have `matched_payment_ids=[]`). Fix that worked: delete the orphan payment + post a manual `account.move` (DR AP / CR Bank) + `account.move.line.reconcile` against the bill AP line. Worth checking for on any Odoo 19 Enterprise client using direct bank-recon.
- Cross-project impact: Odoo 19 payment pattern applies to every Odoo 19 Enterprise client in the fleet (Obliq, Sahara, Diamond/STIG, Alvi-Dental, Remittance).
- Canonical facts promoted to Unity: none (local Oenoteca inventory/AP state; no new client / server / credential).

### 2026-04-22 — D:/ECOSIRE.AI — Red Dead Redemption 2 article published to freepcgames.org
- New publish script `ai-content-engine/backend/scripts/publish_red_dead_redemption_2.py` (follows the freepcgames.org prompt template: `####` h4 sections, no bold headings, 150–200-word About block, 7 keyword placements with random bolding). Published as WP post ID 110 → https://freepcgames.org/2026/04/22/red-dead-redemption-2-full-pc-game-download/ via `ace-api` Docker container.
- **Gotcha for future ECOSIRE.AI agents:** the `ace-api` container working dir is `/code` (not `/app`). Scripts live under `/code/scripts/`. One-shot pattern: `scp` to `/tmp/` on prod → `docker cp` into `ace-api:/code/scripts/` → `docker exec -w /code ace-api python -m scripts.<name>`.
- Cross-project impact: none. Confined to ECOSIRE.AI.
- Canonical facts promoted to Unity: none (no new client / server / credential).

### 2026-04-22 (late evening + beyond) — D:/Development — Unity organization scaffold + claude-mem worker running + Cheatsheet
- **Unity scaffold reorganization (non-destructive):** added `02 - Projects/Active Clients/` with dedicated folders + READMEs for 7 top active clients (Sahara, Obliq, Oenoteca, Diamond, Amalfi, Remittance-Suleman, Alvi-Dental). Added `02 - Projects/Prospective Clients/README.md` with pipeline table (Noon, Wayfair/Quicken, Gaspar MELI, MyFirstTech, Tariq Trendyol, BreezIntl Daraz, Ximplifika, Texas Closeout, ATH, RedXpider). Added `02 - Projects/One-Off Modules/README.md`. Added `03 - Areas/Businesses/` with READMEs for ECOSIRE.COM, ECOSIRE.AI, ECOSIRE.IO, Odoo Modules, Managed Hosting. Updated [[MOC - Projects]] with links to all four new index READMEs. **Existing PRJ files and Ecosire Client Portfolio untouched — scaffolding is additive only.**
- **claude-mem worker: ROOT-CAUSED + RUNNING.** The `npx claude-mem start` failure was a Windows cooldown state (plugin caches "Worker unavailable on Windows" after first failed attempt). Fix: delete `~/.claude-mem/worker.pid` + `supervisor.json`, then run `bun worker-service.cjs` directly. Worker now running PID 18276, port 37777 healthy, dashboard at http://localhost:37777.
- **Cheatsheet** — `04 - Resources/Command Cheatsheets/Unity Hive-Mind Cheatsheet.md` = single-page master reference (14 sections: workflow, skills, graphify commands, MCP, backup, staleness, credentials, claude-mem, git hooks, settings hooks, where-everything-lives, common tasks, troubleshooting, one-line reminders). Added pointer to top of [[MOC - Command Cheatsheets]].
- **Stop hook (silent)** — `~/.claude/settings.json` now has `hooks.Stop` running `backup_unity.sh` async 60s timeout. Third durability layer (after skill-invoked hive-mind-sync and daily 23:50 Task Scheduler backup).
- **Renamed:** `hive-mind-audit` → `unity-audit` (memorable). Expanded to 14-point check covering all new infra.

### 2026-04-22 (late evening) — D:/Development — All 5 research recommendations + 3 risk mitigations implemented
- **#1 Graphify git hooks** — `graphify hook install` ran in all 6 git repos (Unity, Development, ECOSIRE.COM, ECOSIRE.AI, ECOSIRE.IO, OpenClaw). post-commit + post-checkout hooks now auto-refresh AST on every commit (no LLM, <1s).
- **#2 Graphify MCP server** — `.mcp.json` files at Unity root + D:/Development root register `unity-hive-mind` MCP server pointing at `Unity/graphify-out/graph.json`. `enableAllProjectMcpServers: true` set in `~/.claude/settings.json`. `pip install --user mcp` already done. Any agent in these sessions gets `query_graph/get_node/get_neighbors/shortest_path` as tools without custom skill code.
- **#3 AGENTS.md shims** — written at every workspace root (Development, ECOSIRE.COM, ECOSIRE.AI, ECOSIRE.IO, Company, Office, Unity). Future-proofs against Codex/Aider/Gemini-CLI sessions.
- **#4 claude-mem installed** — `npx claude-mem install` registered the plugin at user level (`~/.claude/plugins/marketplaces/thedotmack/`). Bun 1.3.13 + uv 0.11.7 also installed (prerequisites). Worker daemon failed to start on first try (Windows-specific health-check timeout); plugin hooks will activate in the next Claude Code session — worker troubleshooting deferred.
- **#5 defuddle-ingest skill** — user-level skill at `~/.claude/skills/defuddle-ingest/`. Opinionated wrapper around `defuddle parse` for platform-api-intelligence + general research note ingests. Auto-installs `npm install -g defuddle` on first use.
- **Risk #1 — Credential guard** — PreToolUse hook at `~/.claude/hooks/guard_credentials.sh`. Any `Read` on `Unity/03 - Areas/Credentials & Access/**` returns exit 2 with a warning injected into the model's context. Registered in `~/.claude/settings.json` under `hooks.PreToolUse`.
- **Risk #2 — Staleness check** — `scripts/hive-mind/check_graph_freshness.sh` scans all 5 target graphs, flags any where source files are >7d newer than `graph.json`. Tested: Unity fresh, 4 others missing (expected, needs first `/graphify .`). Integrated into `unity-audit` section 3.
- **Rename:** `hive-mind-audit` skill renamed to `unity-audit` (more memorable, anchors on the product name). Expanded from 10 checks to 14 checks covering all new infra from this session: graphify git hooks, MCP server + `.mcp.json`, AGENTS.md shims, claude-mem plugin + worker, Bun/uv runtime, credentials guard hook.
- **Stop hook (silent) added** — `~/.claude/settings.json` now has a `Stop` event hook that runs `backup_unity.sh` async (60s timeout) when any Claude Code session ends. Durability safety net for when agents forget to invoke `hive-mind-sync`: even without a session-log entry, Unity gets committed + pushed automatically.
- **Risk #3 — Dropbox+multi-agent write races** — `hive-mind-sync` already commits + pushes Unity on session end; existing daily scheduled backup is the catch-up. Updated `.gitignore` to exclude `.claude/` (upstream skills) and `graphify-out/` (regeneratable) from git tracking, preventing accidental submodule/conflict scenarios.
- Pending next session: run `/graphify .` inside D:/Development / ECOSIRE.COM / ECOSIRE.AI / ECOSIRE.IO (4 graphs still missing). Troubleshoot claude-mem Windows worker (plugin works without it; worker adds background observation ingest).

### 2026-04-22 (evening) — D:/Development — Unity graph built + query skill + audit skill + research pass
- **Unity knowledge graph built:** 283 nodes, 513 edges, 14 labeled communities (Unity Hive Brain & Corporate Vault / Client Portfolio / Ecosire Platform & Knowledge Base / Odoo Module Library / SEO-AEO-GEO / Agent Fleet / Cheatsheets & Production Infra / CLI Reference / People / + 5 templates). 10 god nodes, 5 surprising cross-community connections. Output: `graphify-out/graph.json` (332KB), `graph.html` (247KB), `GRAPH_REPORT.md` (12KB). Extracted via 5 parallel general-purpose subagents in ~3min.
- **New skills added at user level:** `hive-mind-query` (queries graphify graph before 3+ reads, 71.5× token reduction) and `hive-mind-audit` (10-point monthly health check — vault, backup, graph freshness, skill wiring, credentials hygiene, session log activity, SSH inventory, graphify features, ecosystem watch).
- **Research landscape captured:** top 5 similar projects (claude-mem, graphify, obsidian-skills, code-review-graph, cognee/supermemory); prevailing patterns (short CLAUDE.md + Skills, symbol+graph > embeddings, MCP as memory socket, hooks over prompts, AGENTS.md standard); 5 ranked improvements (graphify hook install / graphify.serve MCP / AGENTS.md shims / claude-mem compression / defuddle pipeline); 3 risks (prompt injection via vault, stale graph confidence, Dropbox+multi-agent write races).
- Canonical facts promoted: [[MOC - Hive Mind]] now references the built graph; user-level [[CLAUDE]] lists all 5 installed skills.
- Pending next session: run `/graphify .` inside each of D:/Development / D:/ECOSIRE.COM / D:/ECOSIRE.AI / D:/ECOSIRE.IO to build their first code graphs.

### 2026-04-22 — D:/Development — Unity hive-brain infrastructure complete (rename + graphify + hive-mind skills + daily GitHub backup + server/key inventory)
- Dropbox vault renamed: `Muhammad Amir Obsidian Vault` → **Unity**. Claude Code project dir + GitHub repo (`engrmuhammadamirnazir/Unity`) renamed to match. Local `origin` remote updated.
- Installed `kepano/obsidian-skills` into `<vault>/.claude/skills/` (obsidian-markdown, obsidian-bases, json-canvas, obsidian-cli, defuddle) — gitignored (upstream content).
- Installed `graphifyy` (v0.4.25) + `/graphify` skill at user level (`~/.claude/skills/graphify/`). Available in every Claude Code session.
- Wrote `.graphifyignore` for Unity, D:/Development, D:/ECOSIRE.COM, D:/ECOSIRE.AI, D:/ECOSIRE.IO (credentials + heavy binaries excluded).
- Created `hive-mind-sync` + `hive-mind-load` user-level skills at `~/.claude/skills/` (invokable from every session).
- Updated `~/.claude/CLAUDE.md` (applies to every session) + every sibling project CLAUDE.md (Development, ECOSIRE.COM, ECOSIRE.AI, ECOSIRE.IO, Company, Office, OpenClaw) with a MANDATORY Hive-Mind banner — dev agents previously forgot cross-project state.
- **Daily Unity backup:** Windows Scheduled Task `Ecosire-Unity-Daily-Backup` runs `C:\Users\Amir Nazir\scripts\hive-mind\backup_unity.bat` at 23:50 local (next run tonight). Script: `backup_unity.sh` (bash). Log dir: `C:\Users\Amir Nazir\scripts\hive-mind\logs\`.
- **Server & Key Inventory:** Created `[[Server & Key Inventory]]` under `03 - Areas/Credentials & Access/` consolidating every client server, IP, SSH user, and key file path. Copied 11 SSH keys (ecosire.pem, hetzner_demo, sahara.pem, remittance.pem, diamond_group, ecosire_main(+.pub), alvi_aws.pem, ustelekomecosire.pem, id_ed25519(+.pub)) into `03 - Areas/Credentials & Access/SSH Keys/`. Folder is gitignored — values live in Dropbox only.
- Canonical facts promoted: [[MOC - Hive Mind]], [[Server & Key Inventory]], [[CLAUDE]] (vault root).
- Pending (not blocking): run `/graphify .` in a fresh session for each of Unity, D:/Development, D:/ECOSIRE.COM, D:/ECOSIRE.AI, D:/ECOSIRE.IO to build their first knowledge graphs. After the first full build, weekly cron can run `graphify update <path>` (no LLM) for incremental refresh.

---

<!-- New entries go ABOVE this line so the newest stays at top -->
