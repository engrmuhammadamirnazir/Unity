---
type: index
tags: [modules, modules-clients, short-engagement, index]
aliases: [One-Off Modules, Module Customers]
created: 2026-04-22
updated: 2026-05-19
---

# Modules Clients — Off-the-shelf Module Customers

> Renamed from "One-Off Modules" on 2026-05-09 to align with the file-system taxonomy at `D:/EcosireClients/ModulesClients/`. Scope unchanged: short-cycle paid engagements where the customer buys an off-the-shelf Odoo module (or small bundle), self-hosts, and gets 60-day install support — no implementation engagement, no server access on our side.

## Cross-reference to the file system

The artifact tree lives at `D:/EcosireClients/ModulesClients/<Client-Slug>/`. This Unity folder holds the cross-project narrative — what's recent, who paid recently, which platforms / modules are selling, support window status, recurring patterns.

When a Modules Client commissions custom work, graduate them to **Project Clients** (move the file-system folder + add a per-client Unity note in `02 - Projects/Project Clients/`).

## Current / recent customers

- **Edwin Ardila — 12-module Odoo SH v19 bundle** (2026-05-18, paid USD $2,000 via Wise; invoice ECO-INV-2026-0050; folder promoted ProspectiveClients → ModulesClients on 2026-05-19). Spanish-speaking, Bogota Colombia, ardilae@gmail.com. **12 prod license keys** issued via `internal/_grant_licenses.cjs`, each tied to `ardilae@gmail.com` with `activations_limit=3` (staging + prod + spare on Odoo SH): zalando ECO-88KM-98NS-W92A · miravia ECO-UFTT-573H-QMPY · amazon ECO-M3JX-NCKP-NJUJ · aliexpress ECO-6L4G-PTZF-XJNY · temu ECO-MSFN-887N-Q5FY · shopify ECO-85NV-JQFD-JDCY · tiktok ECO-QM38-EXU5-VYZ3 · walmart ECO-NJLJ-CBSM-NRS9 · ebay ECO-MQPL-2FRN-JFH4 · targetplus ECO-V9VX-D4MS-GJDE · googleshopping ECO-UVQM-CW8F-4XQM · facebook ECO-E7J6-B6GN-KA8U. Delivery pack at `D:/EcosireClients/ModulesClients/Edwin-Ardila/delivery/Edwin-Ardila-12-modules-v19-r1.zip` (15.1 MB, 1,164 entries; bilingual ES+EN emails). All 12 docs.ecosire.com pages verified HTTP 200; 4 new pages written (miravia/target-plus/google-shopping/facebook) + 3 source-vs-doc drifts fixed (Facebook v22 not v23, Google Shopping has Reports not Promotions, AliExpress $249 not $499). Free upgrade to Odoo v20 + preferential v21+ rates included. 60-day support window starts on Edwin's install-success reply. Folder: `D:/EcosireClients/ModulesClients/Edwin-Ardila/`.
- **Bimal Tamrakar — Daraz module v17 + v18** (2026-05-08, paid). Tested on `demo.ecosire.com` (v19) before purchase. License keys: v17 `ECO-WA4K-PZ3X-NURT`, v18 `ECO-AXUN-UMJ5-D4DC`. Delivery iteration log: r1 → activation hit version-mismatch (License Client v2.1.1 normalize shipped 2026-05-08); r2 → r3 view_mode='list' on v17 → r3 → r4 //list xpath on v17 → r4 → **r5 (2026-05-11 00:30 sent)**: wizard view validator (`Field 'status' used in modifier`) + v17-only JS rpc backport (`@web/core/network/rpc` → `rpc_service`) fixed in `daraz_store_management 17.0.5.0.3`. End-to-end Playwright verified locally before delivery (all 4 wizards + Dashboard render, 0 console errors). Image-sync feature deferred to v17.0.6.0.0 within few days as free follow-up. 60-day support timer pending Bimal's install-success confirmation. Folder: `D:/EcosireClients/ModulesClients/Bimal-Tamrakar/`.
- **Quicken Accounting — Wayfair module** (`v3.14.0`, 2026-05-08): retainer-style relationship, technically a Project Client (we host on Hetzner Docker). Listed here historically because Wayfair started as a single-module sale; lives at `D:/EcosireClients/ProjectClients/Quicken-Accounting/`.
- **SAP B1 ↔ Odoo integration (Quicken)**: $1,250, 2-week delivery. Proposal sent 2026-03-31. Memory: `quicken_sap_b1_project.md`.

## Convention

- Off-the-shelf App Store sales ($249/$349/$499 tiers): file-system folder under `D:/EcosireClients/ModulesClients/`. Each gets its own folder with `README.md` + `delivery/` + `invoice/` + (later) `support/`. Track 60-day support window in README.
- Short diagnosis-and-fix tickets without an existing customer relationship: a single line in this README is usually enough — folder only when recurring touchpoints develop.
- Bug-fix delivery rounds: `<Client>-<modules>-<versions>-r2.zip`, `-r3.zip` etc.; pair with `EMAIL_TO_<CLIENT>_r2.md`. Keep prior rounds in folder for audit.

## Archive candidates

Move to `08 - Archive/` (Unity) and `D:/EcosireClients/Archive/` (file-system) once an engagement is delivered + invoiced + no further support expected for 30+ days past the 60-day window.

## Cross-links

- File-system convention: `D:/EcosireClients/README.md` (3-tier taxonomy section)
- Pattern reference: `04 - Resources/Patterns/EcosireClients_README.md`
- Lead pipeline: `02 - Projects/Prospective Clients/`
- Long engagements: `02 - Projects/Project Clients/`
