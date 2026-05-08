---
type: client
tags: [client, active, amalfi, foods, bahrain, ksa, odoo-17, blueprint-review, zatca-phase-2]
client: Amalfi Foods / Farhoud Ventures
status: blueprint-review — Master Blueprint received 2026-04-27 from Yehia; Ecosire response (Technical Review + Revised Phased Proposal) drafted 2026-04-30, awaiting send + working session 4-7 May
location: Bahrain (BH WLL) + planned KSA entity (CR pending)
created: 2026-04-22
updated: 2026-04-30
---

# Amalfi Foods / Farhoud Ventures

**5-co multi-company Odoo 17.0 Enterprise tenant, 28 users, $15M/yr revenue.** Fiverr inbound 2026-04-18, NDA signed 2026-04-19. Highest-value new prospect of April.

## Quick facts

| Field | Value |
|-------|-------|
| Contact | Yehia (IT lead) |
| Current stack | Odoo Online, Odoo 17.0 Enterprise, 5 companies, 28 users |
| Revenue | $15M/yr |
| BOM coverage | 13.7% only → FSSC-22000 risk |
| Custom Python | **ZERO** — all Studio customizations (84 custom fields + 2 custom models) |
| Upgrade path | Safe: v17 → v19 (clean) |
| Existing partner | Mast IT (original Odoo implementer) |
| Upside | Ghitha SWF expansion = $30-60K group-ERP scope |

## Engagement value

- **Phase 1 — Hosting + v17→v19 Upgrade bundle:** $750 (discounted from $2,500 list; conditional on 12-mo Managed Hosting commitment)
- **Ecosire Managed Hosting:** USD 100/mo (CPX51-sized; up from default $50 due to scale)
- **Phase 2 up-revised:** $2,500-4,500 (Studio cleanup + BOM restructure + priority-1 Approvals app rollout)

## Recent state (2026-04-22)

- NDA signed 2026-04-19 via branded Ecosire digital signature + SHA-256 cert page.
- **4.57 GB DB dump received, restored locally PG15 + Odoo 17 filestore (49K files), analyzed end-to-end** under NDA.
- **Rev 2 PDFs emailed 2026-04-20**: Hosting Recommendation + Tenant Analysis Report (16 sections).
- Rev 1 moved to `_superseded/`.
- v19 upgrade dump received (Odoo ticket #4195441, 211.8 MB).
- 15-task implementation plan WRITTEN in `docs/superpowers/specs/` + `plans/` — execution pending user choice (subagent-driven vs inline).
- Approvals app 80% ready for Yehia's priority-1 ask.

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/amalfi_foods_client.md`, `session_2026_04_18_amalfi_foods_proposal.md`, `session_2026_04_19*.md`, `session_2026_04_20_amalfi_*.md`
- PDFs + local DB clone: `D:\EcosireClients\ProspectiveClients\Amalfi-Foods\` (or wherever the NDA-secured copy landed)

## Open threads

- **AWAITING** Amir to review + click-send Ecosire response to Yehia's Master Blueprint.
- 90-min working session proposed Sun 4 May / Mon 5 / Tue 6 / Wed 7 May, 09:00-17:00 Bahrain.
- v19 upgrade plan still parked (now folded into Phase 1 of the Revised Proposal).
- Ghitha SWF expansion realised — Yehia's blueprint = $30-60K+ scope was right; pricing band moved to **$18K-38K 5-phase implementation** (cut from initial $28-58K draft per Amir's call — sales-friendly anchor for price-sensitive Fiverr-origin client + protects Ghitha follow-on).

## Next actions

- [ ] Amir reviews regenerated PDFs (letterhead applied after fix); confirms Cc list (m.abdellatif, Manan, Zouheir Farhoud).
- [ ] Send email to Yehia with both PDFs.
- [ ] On working-session slot confirmed: send video conf invite + agenda.
- [ ] Within 3 business days post-session: issue final SoW; on countersign, mv ProspectiveClients → ActiveClients + Phase 1 invoice (5% of agreed tier).

---

## 2026-04-30 update — Master Blueprint review session

**Trigger:** Yehia Natout (Group COO) emailed 2026-04-27 18:34 BST sharing `Amalfi-Foods-Odoo-ERP-Master-Blueprint_compressed.pdf` (2.1 MB, 18 chapters covering BH WLL + KSA entities, 5 sites, 24/7 multi-shift production, ZATCA Phase 2, IoT, Dawmt HR, full CoA + 25 analytic cost centres, 14-KPI COO dashboard, 15 non-negotiable system rules, 28-week phased roadmap). Ask: "how can we collaborate to build something similar."

**Ecosire response drafted (NOT YET SENT):**
- `AmalfiFoods_2026-04-30_Blueprint_Technical_Review.{docx,pdf}` (12 pp letterhead — exec summary with top-5 strengths and top-5 risks → 8 cross-cutting findings → 16 chapter-by-chapter reviews → 15 numbered open questions → recommended next steps with 4 working-session slots).
- `AmalfiFoods_2026-04-30_Revised_Phased_Proposal.{docx,pdf}` (13 pp letterhead — 5-phase implementation aligned to blueprint roadmap, two timeline scenarios, three commercial tiers, milestone billing schedule, assumptions tied to open questions, exclusions).
- Reply email body draft at project-local `02-Discovery/08_Reply_to_Yehia_Blueprint_Review_2026-04-30.md`.
- Two reusable generators at `D:/Development/scripts/client_docs/generators/Amalfi_Blueprint_Review.py` and `Amalfi_Revised_Proposal.py`.

**Pricing band shift (CANONICAL):** $4-7K Phase 1-3 indicative (per 2026-04-20 Hosting Recommendation Rev 2) → **$18K-38K 5-phase implementation** (this session). Three tiers: Lean USD 18,000 / Standard USD 28,000 [recommended] / Comprehensive USD 38,000. Phase 1 first invoice on Standard = USD 1,400 (5%). Recurring fees separate (Managed Hosting $100/mo + Odoo Enterprise pass-through ~$697/mo + IoT hardware cost-plus). Pricing cut from initial draft ($28K/$42K/$58K) to final tiers per Amir's mid-session call — Yehia's Fiverr origin + Mast IT incumbent anchor + Ecosire's strategic interest in the Ghitha follow-on > maximizing first engagement.

**5 critical scope-locking questions for working session:**
1. KSA Commercial Registration timing — within 12 weeks (single-stage rollout) or longer (two-stage rollout: Bahrain go-live first at week 22, KSA at +6-8 weeks on CR issue).
2. Mettler Toledo scale model conflict — blueprint Chapter 14 specifies Ariva-S (retail-class), but Yehia's 2026-04-19 written update confirmed actual scales are ICS429 + ICS449 (industrial). Different IoT integration paths.
3. Hidd Bread Factory existence — new (greenfield commissioning, +2-4 weeks), existing-off-tenant (on-board, +1-2 weeks), or re-labelled (rezoning, +3-5 days).
4. 5-co tenant fate (Amalfi / Swabbs / Motivate / Event Fab / Holding) — blueprint reduces to 2 entities (Amalfi BH + Amalfi KSA); other 3 default to dormant.
5. Mast IT continuation — decommission default at Bahrain go-live, hybrid (named modules retained), or continue.

**ZATCA Phase 2 capability:** proven canonical pattern from Future Vision Waredat sandbox certification of 2026-04-29 (9 of 9 official scenarios passed, Compliance CSID issued). Reusable for Amalfi KSA without re-discovery; configuration plus targeted adapter work, not greenfield development.
