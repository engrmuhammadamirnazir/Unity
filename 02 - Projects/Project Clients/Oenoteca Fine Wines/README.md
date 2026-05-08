---
type: client
tags: [client, active, oenoteca, wines, houston, odoo-19]
client: Oenoteca Fine Wines (A&N Fine Wines)
status: live
location: Houston, TX
created: 2026-04-22
updated: 2026-04-22
---

# Oenoteca Fine Wines

**Fine-wine distributor — Houston, TX.** Odoo 19 on isolated Hetzner (migrated off shared AWS 2026-04-14). Heavy inventory + accounting workload.

## Quick facts

| Field | Value |
|-------|-------|
| Server | Hetzner isolated (old AWS `3.20.73.143`, now migrated) |
| SSH key | `Unity/03 - Areas/Credentials & Access/SSH Keys/ustelekomecosire.pem` |
| Custom module | `oenoteca_customizations` v1.2.0 (payment-form tag on SO+Invoice main forms + both PDFs) |
| Scale | 5,310 bottles on hand / $101,072 inventory post-physical-count v2 |
| Contacts | Niccolò Saltarelli, Nick |

## Recent state (2026-04-22)

- Physical-count v2 DONE: STJ/2026/04/0008 $31,021 + 0009 $936 write-down, final 5,310 btl / $101,072.
- Nick confirmed 3 deliveries done (Bandol $25,344 / Gina Lyons $5,040 / Childress $3,024 invoices standing = $33,408 AR).
- 2026-04-18 Phase 2 +$14,301.69 STJ/2026/04/0007 (pre-v2 baseline).
- YTD GM 41.7% post-adjustment.
- Q1 Financial Statements PDF delivered (Rev $407K / NI $77.9K).
- 2 Library bills booked end-to-end 2026-04-17 (GRW Mouton 2000-2003 $3,285.63 + BWG Vega Sicilia/Lopez Heredia $701).
- BWG Hundred Acre Ark 2011 PO P00162 $872 pending physical delivery.
- Enohance proforma 14/2026 booked 2026-04-21 (EUR 327.73).

## Where detail lives

- Project memory: `~/.claude/projects/D--Development/memory/oenoteca_client.md`, `session_2026_04_*_oenoteca_*.md` (multiple)
- Also: `C:\Users\Amir Nazir\Dropbox\ONETECA FINE WINES\` (client accounting data folder, separate from vault)
- Vault legacy: [[PRJ - Oenoteca Fine Wines]]

## Open threads

- Spain OOSHW taxonomy gap (flagged in GRW bill session).
- BWG freight GL anomaly flagged.
- GRW partner-duplicate in `res.partner`.

## Next actions

- [ ] Validate Bandol/Sarasota/Childress invoice payments when received.
- [ ] Resolve partner-duplicate issue.
