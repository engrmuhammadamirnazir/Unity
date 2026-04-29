---
type: project
tags: [project, ecosire, odoo, manufacturing, client, dubai]
status: active
created: 2026-03-09
start-date: 2026-03-09
updated: 2026-04-29
---

# PRJ — Obliq Furniture Manufacturing LLC

> Odoo 19 ERP cleanup and optimization for Studio Obliq, a Dubai-based furniture manufacturer.
> Source: `D:\EcosireClients\ActiveClients\Obliq\`

> **2026-04-29 update (P0 fixed + new module shipped):**
> - "Invalid Operation: must have exactly one journal item involving the bank/cash account" error on partner-set + Feb statement upload **FIXED** — root cause was `account.payment.method.line.payment_account_id` on J24 + J29 pointing back at the bank/CC account itself. Created 2 new transit accounts on company 2 (`1422 Outstanding Receipts CA.111`, `1423 Outstanding Payments CL.111`) and repointed 4 payment_method_line rows. Self-test confirmed.
> - **New module `obliq_reconciliation_lock` v19.0.1.0.0** deployed per Kamal's other ask. Per-journal lock window for ADCB Bank / ADCB CC / Paymob / Petty Cash. Once a period is locked, entries can't be edited unless explicitly unlocked. Kamal + Ziad granted Admin group. UI at Accounting → Configuration → Reconciliation Locks.
> - Audit memo for BC.006 wipe + 4 supplier issues + Aged Payables "unknown account" delivered (`Reply_to_Kamal_2026-04-29_Issues_Diagnosis.md`). Awaiting Kamal's A/B/C on BC.006, canonical Al Quoz pick, and green-light for Landmark `supplier_rank=0` reset.
> - Cross-project pattern captured: [[feedback_odoo19_payment_method_line_outstanding_account]] applies to ANY Odoo 19 client with hand-built CoA — fleet sweep recommended.

---

## Overview

| Field | Value |
|-------|-------|
| **Client** | Obliq Furniture Manufacturing LLC / Studio Obliq |
| **Sister Brand** | Home Figures (homefigures.com) |
| **Location** | Al Quoz Industrial Area 1, Dubai, UAE |
| **Industry** | Contemporary furniture design & manufacturing |
| **Currency** | AED |
| **Fee** | USD 2,500 (confirmed, $1K advance received) |
| **Sprint** | 20 days, 4 phases |
| **Status** | CONFIRMED |

---

## Key People

| Name | Role | Email |
|------|------|-------|
| Ziad Al-Haffar | Owner/Admin | ziad@homefigures.com |
| Venus De Leon | Primary Sales (80%) | venus@studioobliq.co |
| Kamalnath | Sales + Ops (20%) | kamal@studioobliq.co |
| Karim Al Haffar | Management | karim@studioobliq.co |

---

## Database Analysis (2026-03-09)

| Metric | Value |
|--------|-------|
| Products | 585 (all consumable with tracking) |
| BOMs | 1,042 (4,280 lines, 3-level hierarchy) |
| Sales Orders | 619 (AED 1,428,190 confirmed) |
| Purchase Orders | 299 (AED 780,334) |
| Manufacturing Orders | 1,263 (610 done, 602 stuck) |
| Stock Quants | 525 (248 negative = 47%!) |
| AR Outstanding | AED 2,444,889 (91.8% unpaid) |
| AP Outstanding | AED 866,157 |
| Revenue (5 months) | AED 1.43M |

---

## 12 Critical Issues

1. **Product costs = 0** in Co2 (JSONB key) [CRITICAL]
2. **AR AED 2.44M** outstanding (91.8% unpaid) [CRITICAL]
3. **SVL stuck** AED 583K [CRITICAL]
4. **602 MOs stuck** confirmed [HIGH]
5. **248 negative quants** (47%) [HIGH]
6. **Paymob AED 265K** unreconciled [HIGH]
7. **Zero bank reconciliation** [HIGH]
8. **Company transition** incomplete [HIGH]
9. **Customized Product** 22% of revenue [MEDIUM]
10. **No Shopify integration** (studioobliq.co) [MEDIUM]
11. **No VinSupplier** integration (zero data) [MEDIUM]
12. **Customer concentration**: Landmark/Home Centre = 86.2% of revenue [RISK]

---

## Environment

| Item | Value |
|------|-------|
| Staging DB | obliq_staging (localhost:5432, user openpg) |
| Odoo | v19 on port 8072 |
| Dump | dump_extracted/dump.sql (75MB, 431K lines) |

---

## Deliverables

| Document | Status |
|----------|--------|
| Kickoff Report | v2 delivered (Obliq_Kickoff_Report_2026-03-09.pdf) |
| CLAUDE.md | 10KB comprehensive reference |

---

## Related

- [[Ecosire Private Limited]] -- Service provider
- [[Ecosire - Client Portfolio]] -- Client list
- [[MOC - Projects]] -- Project index
