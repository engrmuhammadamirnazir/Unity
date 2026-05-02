---
type: client
tags: [client, active, remittance, suleman, arif, odoo-19-enterprise]
client: Salueman Arif — Remittance
status: live
location: TBD (Pakistani bridge-remittance operator)
created: 2026-04-22
updated: 2026-05-02
---

# Remittance — Salueman Arif

**Bridge-remittance operator.** Custom `remittance_management` module on Bitnami Odoo 19 Enterprise. Biggest private-module build of Q2 2026 — v19.0.9.0.0 lifecycle-first refactor (2026-04-22) followed by **v19.0.10.0.0 (2026-04-26)** which restored the multi-currency Balances dashboard, swept smart defaults across forms, gave the Compliance Matrix actual `ValidationError` teeth (v9 was orphan), and shipped 17 PDF/XLSX/CSV reports under a new Reports menu.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Bitnami Odoo 19 Enterprise — `52.28.45.137` |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/remittance.pem` · user `bitnami` |
| Domains | `remittanceaccounting.ecosire.com` (prod) + `remtest.ecosire.com` (staging) |
| Custom module | `remittance_management` @ **`v19.0.10.0.24`** (LIVE on both DBs 2026-05-02 — Treasury Dashboard + Cash Management Suite + real GL balance compute) |
| Last pre-deploy backup | `pre_v10_0_23_remtest_20260501_212923.dump` + `pre_v10_0_23_remittanceaccounting_20260501_212923.dump` at `/opt/bitnami/odoo/backups/` (stamps for v10.0.4 through v10.0.22 retained) |
| GitHub | `engrmuhammadamirnazir/remittance_management` (PRIVATE) — main `0ef5a90` + new tags `v19.0.10.0.23` + `v19.0.10.0.24` (NO AI attribution per client-repo rule) |
| User Manual PDF (latest) | `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/Remittance_User_Manual_v19.0.10.0.0_Addendum.pdf` (in-app User Guide is now the canonical source — refreshed via lxml migration in v10.0.7) |

## Current state (2026-05-02 evening — UPDATED)

**v19.0.10.0.24 LIVE both DBs.** Treasury Dashboard + Cash Management Suite (v10.0.23) + real GL balance compute (v10.0.24). 14 versions in one day (v10.0.10 → v10.0.24): currency-lock hardening, dual-leg cash-in JE rewrite, sign-flip dashboards, settlement model + wizard, Stripe-grade enterprise theme, MTCN crypto-random codes, kind-adaptive lifecycle, Treasury suite, real balances. Six fleet-wide Odoo 19 patterns captured.

### What v10.0.23 added (Treasury Dashboard + Cash Management Suite — Suleman's biggest UX win since v10.0.4)
- **`Accounting → Treasury`** new client action (sequence 0). Single-page company-side view of all cash + bank balances at a glance:
  - 3 KPI cards per currency (EUR / USD / AED) summed across all accounts
  - Currency × location heatmap card
  - Per-account table with reconciliation pill (green ≤7d / amber ≤30d / red >30d), count variance dot, 30-day sparkline from snapshots, 5 row-actions (Reconcile / Cash Count / Transfer From / Open Ledger / Edit)
  - Filter chips + free-text search
  - Single round-trip via `remittance.account.treasury_dashboard_payload()`
- **3 new wizards** accessible from the Treasury topbar AND per-row kebab:
  - `remittance.cash.count` — EOD physical-vs-ledger variance, books difference to suspense as `variance_adjust`, stamps `last_count_date` + `last_count_amount`. Validate-before-mutate, `markupsafe.escape()` on operator notes.
  - `remittance.account.transfer` — same-currency or cross-currency, posts a balanced JE in **company currency** via new `_to_company_currency` helper (caught a CRITICAL Odoo 19 invariant: debit/credit must be company-currency), with FX clearing pair on cross-currency.
  - `remittance.statement.import` — CSV upload + column mapping + 5-row preview (`preview_html` computed Html), redirects to Odoo's native bank reconciliation widget via `account.bank.statement.line._action_open_bank_reconciliation_widget(default_context=...)` (the canonical v19 launcher — `tag: 'bank_statements_review'` does NOT exist).
- **Daily snapshot cron** at 23:55 server time: `remittance.account.snapshot._cron_daily_snapshot()` captures every visible + active treasury account into `remittance.account.snapshot` for the dashboard sparklines. Reuses existing snapshot model (deviation: snapshot is keyed on `account.account` GL not `remittance.account`, so payload resolves through `journal_id.default_account_id`).
- **10 new fields on `remittance.account`**: `bank_name` · `account_holder_name` · `iban` · `account_owner_id` · `treasury_role` · `is_treasury_visible` · `last_count_date` · `last_count_amount` · `current_count_variance` (computed) · `last_reconciled_date` (computed from `account.bank.statement.line.is_reconciled`).
- **Migration** `19.0.10.0.23/post-treasury-init.py` backfills `is_treasury_visible=True` + `treasury_role` from `journal_id.type` on existing accounts; warns on companies missing `remittance_suspense_account_id`.
- **Treasury tag tests pass 6/6** on remtest (`--test-tags=treasury`).

### What v10.0.24 fixed (real GL balance — replaces v9 stub)
- `remittance.account._compute_balance` had been a stub since v19.0.9.0.0 returning only `opening_balance`, ignoring all posted journal entries. Treasury Dashboard built on top therefore showed stale figures.
- Replaced with `read_group` over `account.move.line` filtered `parent_state='posted'` on `journal.default_account_id`, multi-company-guarded. `current_balance = opening_balance + total_debit − total_credit` (cash + bank are asset accounts).
- Verified post-deploy: AED Cash Position correctly shows `5,000.00 AED` from posted moves; totals roll up across the 10 treasury accounts; heatmap places the balance in the correct location bucket.

### Implementation pattern — 6-group Subagent-Driven Development
Spec → Plan → Subagent-Driven Development with the same per-task quality bar (module-developer implementer + module-validator spec compliance + odoo-engineer code quality) was used for the entire Treasury Dashboard build. ~10–14 hours total across 18 commits. Three IMPORTANT issues caught at the code-quality stage (race condition, HTML injection, FX company-currency math, broken bank-rec action tag) — none of them would have been caught by spec review alone. Pattern reusable for any future client-module rollout.

### Cross-project Odoo 19 patterns captured this session (fleet-wide reuse)
1. **`fields.Monetary(related='x.float_field')` rejects with TypeError** in Odoo 19 — registry validation enforces type-compatibility on related fields. Use `compute` field instead.
2. **Canonical bank-rec widget launcher** is `account.bank.statement.line._action_open_bank_reconciliation_widget(default_context={...})` returning `ir.actions.act_window` — NOT `tag: 'bank_statements_review'` (does NOT exist in v19).
3. **`account.account.company_id` is now `company_ids` M2M** in Odoo 19 (was singular in v17/v18).
4. **AI-footer-strip on client-owned repos**: `git filter-branch --msg-filter` works around dirty working trees by combining with `git update-index --assume-unchanged` on the unrelated dirt.
5. **Stripe-grade design tokens** shared via `static/src/theme/enterprise.scss` are now reusable across all OWL dashboards in this module — pattern transferable.
6. **Hybrid treasury architecture**: thin wrapper over `account.journal` + leverage Odoo Enterprise's bank reconciliation widget rather than rebuild. Reusable for any Odoo client needing cash-management visibility.

---

## Prior state (earlier 2026-05-02) — v19.0.10.0.10

**v19.0.10.0.10 was LIVE both DBs.** UX flagship release (v10.0.4) + 3 back-to-back report-system P0 hotfix waves (v10.0.5/6/7) + knowledge articles refreshed + NEW Partner Balance Detail drill-down (v10.0.8) + 2 follow-on P0 hotfixes (v10.0.9 XLSX 502, v10.0.10 Dashboard alert xmlids).

### What v10.0.8 added (NEW FEATURE — Partner Balance Detail drill-down)
- Click any partner row's "View Details" button in the Balances list → opens single-page OWL drill-down at `remittance_partner_balance_detail` client action.
- **Top bar:** back-link to Balances grid, partner avatar (navy gradient), name + type pill, KYC + Sanctions pills, smart-action toolbar (Print Statement / Export XLSX / Export CSV / Open Partner Form / View Open Flows).
- **3 hero balance cards** (EUR / USD / AED) with positive/negative/zero color coding + In/Out totals per currency.
- **Stats strip (5):** Transactions count, First Tx, Last Tx, Biggest Line (clickable → opens that tx), Open Flows.
- **SVG Running Balance line chart** (3-currency layered, last 6 months, no external dep).
- **Transaction Ledger table** with running balance per currency, period chips (7d/30d/90d/YTD/All), currency tabs (All/EUR/USD/AED), each row clickable → opens source `remittance.transaction`.
- Backed by single round-trip `remittance.partner.balance.get_breakdown(partner_id, period)` server method, multi-company aware via `env.companies`. Read-only.
- Files added: `models/remittance_partner_balance.py` +220 lines (`action_open_breakdown`, `get_breakdown`); `models/res_partner.py` +8 lines (`action_print_remittance_statement`); `static/src/balance_detail/{js,xml,scss}` 1100+ lines combined; `views/remittance_partner_balance_views.xml` (View Details button column + client-action registration).

### What v10.0.9 fixed (P0 — XLSX/CSV download 502 Proxy Error)
- Root cause: WSGI/werkzeug encodes HTTP headers as **latin-1**; em-dash (`—`, U+2014) in 3 XLSX builder titles crashed `Content-Disposition: filename="..."` mid-response with `UnicodeEncodeError: 'latin-1' codec can't encode character '—'`.
- Fix: em-dashes → hyphens in `partner_ledger_detailed.py` + `partner_statement.py` + `flow_statement.py`. Plus defense-in-depth: new `_safe_ascii_filename()` and `_rfc5987_filename()` helpers in `controllers/exports.py` emit dual `filename` + `filename*=UTF-8''…` directives per RFC 5987.
- Validated: `curl /remittance/export?...&format=xlsx → HTTP 200, 6251 bytes, file type "Microsoft Excel 2007+"`.

### What v10.0.10 fixed (P0 — Dashboard alerts "Missing Action")
- Root cause: 3 alert rows in Dashboard's Compliance & Operations panel referenced `ir.actions` xmlids that don't exist in the module (informed-guess strings, never verified).
- Fix: corrected 3 references in `models/remittance_dashboard.py`:
  - `action_partner_remittance` → `action_partner_remittance_all`
  - `action_remittance_suspense_item` → `action_suspense`
  - `action_remittance_shipment` → `remittance_shipment_action`

## Prior state (2026-05-02 morning) — v19.0.10.0.7

### What v10.0.4 added (UX flagship)
- **`Remittance → Dashboard`** top-menu (sequence 5, default landing) — Business KPI command centre. Hero KPI cards (Volume Processed / Outstanding Balances / Open Transactions with embedded stage funnel / Open Invoices), mini-strip (Active Shipments / KYC Pending / Suspense Items / Top Partner), 4 SVG charts (Monthly Volume area · Kind Mix pie · Currency Mix pie · Stage Funnel), Top-10 Partners table, Compliance & Operations alerts panel, Recent Activity feed, time-range chips (Today / Last 7d / This Month / YTD / All), quick-action toolbar. Hand-rolled SVG charts (no Chart.js dep), Ecosire navy + amber palette, dark-mode support. Backed by single `remittance.dashboard.get_kpis(period)` AbstractModel server method.
- **`Remittance → Quick Create`** menu — 4-field one-screen wizard for cash kinds. Replaces 14-click form-and-advance with single commit. Auto-locks currency from kind, smart payee visibility, Advance-to-Closed toggle (default ON). ~50 sec saved per cash transaction.
- **Form ergonomics bundle on tx form:** Compliance Matrix preview banner (green/yellow with KYC + Sanctions check badges) + Advance-to-Closed button (single-click full-lifecycle walk for direct-GL kinds) + inline KYC + Sanctions verification buttons (admin only, full chatter audit) + KYC/Sanctions snapshot toggles + currency picker hidden when locked + polished button labels (Advance to Next Stage / Cancel Transaction / Print Voucher / Put On Hold).
- **Bulk Advance Stage + Bulk Advance to Closed** server actions on the tx list view Actions menu.
- **User-pref default-fill** via 4 new `res.users` fields (`remittance_last_kind / payer_id / payee_id / location_id`). New tx forms pre-populate from last create.

### What v10.0.5/6/7 fixed (report system)
- **v10.0.5** — letterhead `KeyError: 'company'` (every PDF crashed; latent since v10.0.0 since Odoo 19's `web.report_layout` doesn't auto-populate `company` in custom letterhead scope). Fix: defensive `<t t-set>` chain (o.company_id → docs[0].company_id → res_company → user.company_id → env.company) + `t-if="company"` guards.
- **v10.0.6** — 13 PDF templates raised `KeyError: 'now'` (`context_timestamp(now())` not in qweb safe-eval scope; `now` is not a name there, only `datetime` and `time`). Fix: `context_timestamp(datetime.datetime.utcnow())` across all 13. Plus suspense_aging XLSX `age_days desc` (non-stored compute) → `opened_date asc` (stored).
- **v10.0.7** — Partner Statement / Ledger / Compliance reports rendered blank PDFs from menu (`<t t-foreach="docs">` empty when no res_ids selected). Two-part fix: (1) NEW `remittance.partner.report.wizard` TransientModel asks for partner + date range first (3 partner-report menu items repointed to wizard act-window actions; smart-button invocations skip wizard). (2) 9 list-style report templates got `<t t-if="not docs" t-set="docs" t-value="env['MODEL'].search([])"/>` fallback so menu invocation renders all records (limit=500 for high-volume models).
- **Knowledge articles refreshed:** root TOC mentions Dashboard + Quick Create + working reports; new "What's New in v10.0.4 – v10.0.7" article (sequence 5, 350+ line HTML body) covers Dashboard + Quick Create + form ergonomics + bulk ops + default-fill + 3 report hotfix waves + demo stats + known issues. Migration `migrations/19.0.10.0.7/post-refresh-knowledge.py` re-syncs articles via lxml (data file is `noupdate=1`).

### Validation
- **All 17 PDF reports + 14 XLSX/CSV exports downloadable.** Smoke #3 confirms 11 list-style reports render real bytes from menu invocation: Balances Snapshot 131KB · Outstanding Balances 141KB · Transactions List 693KB · Shipments List 128KB · Suspense Aging 174KB · Sonstig Breakdown 93KB · Account Snapshot 74KB · Logistics Cost Summary 88KB · Bank Reconciliation 1.3KB · Compliance Audit 283KB · Activity Summary 84KB. Plus 3 partner-report wizards trigger correct ir.actions.report.
- **Earlier validation walkthrough COMPLETE** (closed via v10.0.3 hotfixes that fixed 2 latent v9→v10 migration P0s in `remittance_invoice.py`: `kyc_status` Selection→Boolean and `client_id.partner_id` indirection on `res.partner`).

### Demo stats (for the Suleman meeting)
~70 sec saved per cash transaction (Quick Create + Advance-to-Closed + default-fill stack) · 30 cash tx/day average → ~140 hrs/year for a 3-person ops team · ~95 server-side validation checks pass · zero compliance regression · KYC teeth + audit trail + GL posting integrity all preserved.

## Prior state (2026-04-26) — v19.0.10.0.0 base release (SUPERSEDED)

**v19.0.10.0.0 LIVE** — Balances dashboard restored, smart-default sweep, Compliance Matrix wired with real teeth, 17 reports shipped, PKR removed.

### What v10 added
- **`Remittance → Balances`** top-menu (sequence 15) backed by new `remittance.partner.balance` SQL view — one row per partner with EUR/USD/AED columns, 8 filter chips (Clients/Bridges/Both/Has EUR/Has USD/Has AED/Outstanding only/Active 30d). Restores the multi-currency client view that was removed in v9.
- **Smart-default sweep** — `_onchange_kind` locks `currency_id` from kind on `remittance.transaction` (8 of 9 kinds; `variance_adjust` keeps user-pick), `default_remittance_location_id` on `res.partner` auto-fills `location_id` (no overwrite). Module-wide auto-fills also wired on bank.cost / cash.cost / suspense.item / invoice / outbound_allocation. Currency renders as `widget="badge"` when locked.
- **KYC + sanctions toggles** on `res.partner` with audit trail (`*_date`, `*_by_id`, action methods stamping who/when + chatter). Compliance Matrix `_check_required` now actually fires (raises `ValidationError`) on `draft → kyc_cleared` advance — v9 had it defined but NEVER CALLED from `action_advance_stage` (orphan).
- **17 reports** under new `Remittance → Reports` menu with 5 sub-categories on Ecosire letterhead. PDF (QWeb) + XLSX (raw `xlsxwriter`, no OCA dep) + CSV via unified `/remittance/export` controller. `Statement` smart button on partner form for one-click PDF.
- **Audit fixes:** A2 — `_compute_remittance_balances` now reads from `account.move.line` joined via `remittance_tx_id` (was reading `tx.amount` which missed USD leg of `bank_eur_out_usd_in`). A3 — `remittance.balance.line.role` from `is_remittance_intermediary` flag (was guessing from debit/credit sign).

### What v10 removed
- **PKR currency** (3 rows from `res_company_activated_currency_rel`); pre-migration aborts (UserError) if any `remittance_tx_id`-tagged GL line uses PKR.
- Orphan `views/remittance_balance_overview_views.xml`.
- Redundant `My Queue` menu.

### Deploy hotfixes folded in (4 cascading)
1. Self-contained Ecosire letterhead — Odoo 19 `web.external_layout_standard` uses `t-attf-class="header ..."` not literal `class="header"`, so `hasclass('header')` xpath fails. Fixed via primary self-contained template + `t-out 0` slot.
2. `data/knowledge_data.xml` wrapped in `<data noupdate="1">` — Odoo 19 RelaxNG rejects `<odoo noupdate="1">` with direct records.
3. Balance breakdown article body switched from `<![CDATA[]]>` to inline HTML — schema rejects CDATA inside `<field type="html">`.
4. **🔥 P0 PROD BREAK** — `<t t-inherit="web.ListView" t-inherit-mode="primary">` xpath couldn't locate parent element in v19's compiled ListView, broke every list view fleet-wide. Hotfix removed inheriting template + registry entry. **KPI hero banner deferred to v10.0.x patch.**

These four are now fleet-wide feedback files in D:/Development project-local memory: `feedback_odoo19_relaxng_strict.md`, `feedback_odoo19_external_layout_pattern.md`, `feedback_owl_listview_inherit_p0.md`, plus the Bitnami CLI env note added to `remittance_server.md`.

---

## Prior state (2026-04-22) — v19.0.9.0.0 lifecycle-first refactor (PRIOR — superseded by v10)

**v19.0.9.0.0 LIVE** — lifecycle-first refactor responding to Suleman's meeting feedback: "still feels like Excel, need end-to-end tracking of client money until it reaches payer-designated final destination, clear all current data, forward only".

**Architecture:**
- 6 shadow models dropped: `remittance.client / bridge / case / ledger.entry / reconciliation / balance.engine`. `account.move.line` is the single source of truth via new `remittance_tx_id` tag column.
- 4 new models: `remittance.stage.config` (10 seeded gate rows), `remittance.suspense.item` (variance discipline with maker-checker), `remittance.flow` (SQL-view lens over chains), `account.move.line._inherit`.
- `remittance.transaction` refactored: 9 kinds (cash_eur_in/out, cash_aed_in/out/expense, bank_eur_in, bank_eur_out_usd_in — one-tx dual-currency — bank_usd_out, variance_adjust) × 10-stage Kanban pipeline (draft → kyc_cleared → funds_received → eur_locked → usd_booked → usd_out → aed_payout → closed + on_hold + cancelled) × dual-partner legs × `flow_root_id` (stored recursive compute).
- GL dispatcher posts real `account.move` per kind at `funds_received` stage entry. Every line tagged with `remittance_tx_id` so Balance Breakdown reads directly from the GL.
- 2 OWL widgets: Flow Timeline (vertical chain walker on tx form) + Partner Ledger (xlsx-exact grid on res.partner form with yellow/green/blue cell semantics matching NEW BUSINESS 2024.xlsx `accounts` sheet).
- 4-role security: Accountant < Operations < Finance/Compliance < Admin. Maker-checker ir.rule on suspense resolution.
- 12 control accounts seeded per company (1010/1020/1110/1120/1210/1310/1410/1420/2910/4910/4920/6910) + cash+bank journals per activated currency (EUR/USD/AED/PKR).

**Deployment trail:**
- Spec: `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/specs/2026-04-22-remittance-v19.0.9-lifecycle-first-design.md` (883 lines, commit `5239ce7`)
- Plan: `D:/EcosireClients/ActiveClients/Suleman-Remittance/docs/plans/2026-04-22-remittance-v19.0.9-implementation-plan.md` (4085 lines, commit `8110f38`)
- Pre-deploy backups: `/home/bitnami/backups/remittanceaccounting_pre_v19_0_9_final_20260422_161808.dump` + `remtest_pre_v19_0_9_20260422_145324.dump`
- Rollback script: `/home/bitnami/rollback_v19_0_9.sh` (prompts YES for safety)
- Post-seed shell script (Enterprise `create_asset` workaround): `D:/Development/odoo19/server/addons/remittance_management/scripts/seed_control_accounts.py`

## Design lineage

- **v19.0.7.0.0** (2026-04-14) — spec v3.2 decisions: DROP bridge, cash skips invoice, dual-leg, auto variance, multi-client outbound split.
- **v19.0.8.0.0** (2026-04-17) — 15-sheet-feature buildout from NEW BUSINESS 2024.xlsx: split-lines, categories, location auto-infer, cost_tier, 7-bucket P&L, matchup dashboard, Sonstig drill.
- **v19.0.8.1.x** (2026-04-17) — balance.line role-split, OWL role badges, partner_client/partner_bridge drill-down.
- **v19.0.8.2.x** (2026-04-17 to 04-21) — ledger compact, legacy menu cleanup, dashboard partner-role, knowledge-article rewrite.
- **v19.0.9.0.0** (2026-04-22) — lifecycle-first rebuild: shadow ledger dropped, real GL posting, Flow Timeline + Partner Ledger widgets, 10-stage Kanban, forward-only clean slate.

## Where detail lives

- **D:/Development project memory** — `remittance_client.md`, `remittance_v19_0_9_lifecycle_first.md`, `session_2026_04_22_remittance_v09_lifecycle.md`, `feedback_enterprise_account_create_asset.md`, `feedback_bitnami_odoo19_migration_signature.md`, `remittance_server.md`, and all prior session/v8.x memory files.
- **Code** — `D:\Development\odoo19\server\addons\remittance_management\` (dev) + `D:\EcosireClients\ActiveClients\Suleman-Remittance\` (client repo + docs + backups).

## Open threads (post-v10.0.24)

- **Suleman client demo video** — should now feature the full Treasury Dashboard + Cash Count + Transfer + Statement Import flow, replacing the v10.0.10-era 10-min flow. Record after Suleman has tested.
- **`_je_bank_eur_out_usd_in` JE math** — still needs Suleman accountant call to confirm intended FX gain/loss + company-currency conversion pattern. Now relevant alongside the new Treasury Transfer wizard which already does cross-currency JE math correctly via `_to_company_currency`. Use the transfer wizard's pattern as a reference when fixing the bank wizard.
- **App Store HTML refresh** — `static/description/index.html` still references PKR/Pakistan copy. Should also feature the Treasury Dashboard screenshot now that it's the marquee feature.
- **v10.0.25 backlog candidates** — surfaced during v10.0.23/24 build:
  - Reports inline date filter chips (carried from v10.0.9 backlog)
  - Send-by-email on every report
  - Statement smart-button audit signature on partner record
  - Save-and-continue keyboard shortcut on tx form
  - Treasury Dashboard pagination + `read_group` batching when account count exceeds ~50 (current per-account `read_group` is fine for Suleman's 10–15 accounts but won't scale)
- **Residual payment** collectable upon v10 sign-off.

## Next actions

- [ ] Record client demo video that includes Treasury Dashboard + Cash Count + Transfer + Statement Import (supersedes the v10.0.10 demo doc).
- [ ] Schedule 30-min call with Suleman + accountant to confirm `bank_eur_out_usd_in` GL pattern (v10.0.25 prerequisite).
- [ ] Refresh `static/description/index.html` — drop PKR copy, add Treasury Dashboard screenshot.
- [ ] Test the Treasury features end-to-end on real Suleman accounts (he has 5–15 EUR/USD/AED accounts across Estonia/France/UAE).
- [ ] Drop the three local stashes on `feat/v19.0.10-balances-and-ux` once verified nothing useful remains (`stash@{0}/{1}/{2}` summaries: see session notes).
- [ ] Collect residual upon v10 sign-off.
