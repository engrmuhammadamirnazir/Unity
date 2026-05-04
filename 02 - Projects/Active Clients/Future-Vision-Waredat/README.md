---
type: client
status: active-live-production
client: Future Vision Est. For Custom Clearance
country: Saudi Arabia
service: ZATCA Phase 2 + Phase 3 e-invoicing on custom Waredat ERP (Replit-hosted Flask SaaS)
fee: USD 750
created: 2026-04-28
went-live: 2026-05-05
updated: 2026-05-05
tags: [client, active, zatca, ksa, replit, flask, e-invoicing]
---

# Future Vision Waredat (KSA — ZATCA Phase 2/3)

## Status: LIVE on production (2026-05-05)

ZATCA Phase 2 + Phase 3 fully delivered for tenant **ABIR ALMANAFETH Establishment Ltd** (`aber-almanafez`, VAT `300832422400003`) on Waredat ERP. Production CSID issued via `/core/production/csids` and live invoice reported via `/core/reporting/single`. First production ZATCA tax record created (`SIN-SMOKE-001` smoke test, 1.00 SAR).

## Client

- **Legal name:** Future Vision Est. For Custom Clearance
- **CEO:** Mr. Hussam Saleh Almohaimeed
- **Location:** Jeddah, Al Balad District, Bayadha, Saudi Arabia
- **Phone / WhatsApp:** +966 547 792 429
- **Email:** alsaleh.fcco@gmail.com (general) / hussam.almohaimeed@cports.sa (FATOORA admin)
- **Industry:** Customs clearance + freight forwarding (parent) + multi-tenant ERP SaaS (Waredat ERP)

## Product

- **Brand:** Waredat ERP — multi-tenant SaaS (DB per company), bilingual AR/EN
- **Production URL:** https://waredaterp.com/
- **Replit workspace:** https://replit.com/@hussamalmohaime/Customs-Flow
- **GitHub repo:** https://github.com/hussamalmohaimeed-a11y/waredat-erp (`main` at commit `439fd408` post-2026-05-05)
- **Stack:** Flask 3.1.3 + SQLAlchemy 2.0.41 + PostgreSQL (Replit-managed) + lxml 6.0.2 + cryptography 46.0.7. ZATCA SDK 238-R4.0.0 bundled.
- **Access (Amir):** SSH key authorized on Replit; details in [[../../03 - Areas/Credentials & Access/Per-Client/Future-Vision-Waredat/replit-ssh-and-fatoora]] (gitignored)

## Engagement

- **Phase 2** (technical e-invoicing — UBL 2.1, XAdES-BES, TLV QR, CSID onboarding, sandbox→prod promotion): ✅ LIVE (PR #2 commit `439fd408` merged 2026-05-05)
- **Phase 3** (operational hardening — retry queue + audit log + Postgres immutability triggers + integrity job + EGS lifecycle): ✅ LIVE since 2026-05-04 (PR #1 `d85cea3`)
- **Phase 4** (UX — settings move + onboarding wizard + RBAC + bilingual screens): ⏸ Separate proposal pending — Lean ($500/1.5d), Standard ($850/3d), Comprehensive ($1500/5d)
- **Total fee delivered:** USD 750 (single line item, transfer slip received 2026-04-29 — reconfirm Meezan PK credit)

## Tenant onboarded for ZATCA (production)

| Field | Value |
|---|---|
| Tenant ID in DB | 2 |
| Tenant code | `aber-almanafez` |
| Legal name | ABIR ALMANAFETH Establishment Ltd / مؤسسة عبير المنافذ |
| VAT | `300832422400003` |
| Commercial Registration | `7006925437` |
| EGS Serial | `1-Waredat\|2-ERP-1.0\|3-EGS-AAM-001` |
| Production compliance CSID requestID | `1777931841180` |
| Production CSID requestID | `147515858278` |

Both CSIDs encrypted via `EncryptedText` TypeDecorator using Replit Secret `FIELD_ENCRYPT_KEY` (Bitwarden-backed by Hussam).

## Deployment artifacts

- Local archive: `D:/EcosireClients/ActiveClients/Future-Vision-Waredat/reports/production_csid_2026_05_05/`
- On Replit: `/tmp/cert_out_prod/` (production CSID JSON, compliance CSID JSON, 6/6 cert proof, smoke-test result, DB pre-update backup)
- Smoke test: `SIN-SMOKE-001` 1.00 SAR via `/core/reporting/single` → HTTP 202 REPORTED
- Patched code: `repo/zatca_phase2.py` commit `439fd408` (3 fixes in `build_xmldsig_signature` +81/−34)

## Key learnings (cross-project)

- [[zatca_phase2_new_tenant_deployment_runbook]] — full E2E procedure for any future ZATCA tenant. ~15-20 min budget once OTPs ready.
- [[feedback_zatca_phase2_canonicalization]] — the 3 XAdES recipe fixes (cert digest = base64-text bytes, SP digest = Alhaditech-format raw, issuer = reverse-RDN+", ").
- [[feedback_replit_ssh_push_falls_back_to_local_clone]] — Replit askpass needs TTY; commit+push from local D: clone instead.
- **Production-track shortcut:** skip simulation production CSID layer (broken on `/simulation/production/csids` with Invalid-CSR). Go straight `/core/compliance` + `/core/production/csids`. Production CSID promotion needs NO OTP per Alhaditech reference (verified empirically).

## Pending follow-ups

- [ ] **Hussam:** send WhatsApp confirmation message at `D:/EcosireClients/ActiveClients/Future-Vision-Waredat/docs/04_whatsapp_to_hussam_zatca_live.md`
- [ ] **Hussam:** decide smoke-test invoice housekeeping (keep `SIN-SMOKE-001` as deployment marker, or post a 1.00 SAR credit note)
- [ ] **Hussam:** tighten tenant address fields (street/city length) before high invoice volume — 3 BR-KSA non-blocking warnings on first reported invoice
- [ ] **Replit:** working tree on `fix/xades-simplified-hashing` post-merge — recommend `git checkout main && git pull origin main`
- [ ] **Phase 4 UX proposal** when Hussam wants it
- [ ] **Engagement payment** — reconfirm USD $750 Meezan PK credit
- [ ] **Sim production CSID** (non-blocking, future): `/simulation/production/csids` Invalid-CSR root cause investigation if sim-staging layer needed

## Cross-references

- [[../../05 - Maps of Content/MOC - Hive Mind|MOC - Hive Mind]] — workspace map
- [[../Hive Mind/Session Log#2026-05-05 evening — D:/Development + D:/EcosireClients — Future Vision ZATCA Phase 2 + Phase 3 LIVE on production for ABIR ALMANAFETH|2026-05-05c session log entry]]
- [[../../03 - Areas/Credentials & Access/Per-Client/Future-Vision-Waredat/replit-ssh-and-fatoora|Replit SSH + ZATCA portals (gitignored)]]
- Project memory at `~/.claude/projects/D--Development/memory/`:
  - `future_vision_zatca_client.md` (canonical project state)
  - `feedback_zatca_phase2_canonicalization.md` (technical recipe)
  - `zatca_phase2_new_tenant_deployment_runbook.md` (reusable runbook)
  - `session_2026_05_05c_zatca_phase2_live_production.md` (this session's deep log)
