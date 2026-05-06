---
name: Wise pay links are per-invoice, not a reusable rail
description: Wise wise.com/pay/r/<id> URLs are tied to a specific amount + recipient request; never copy a previous client's link onto a new invoice
type: feedback
originSessionId: c88020f1-0418-44b6-a8a5-a2de0122cb87
---
Wise "Pay link" URLs of the form `https://wise.com/pay/r/<id>` are **per-payment-request artifacts**, not a reusable account identifier. Each link is generated for a specific amount, recipient, and currency and shown on the Wise dashboard as a one-shot request. Reusing one across clients would either route the wrong amount or confuse the payer.

**Why:** Caught 2026-05-06 when generating Bimal Tamrakar's $250 invoice — I copied Edwin Ardila's $2,000 link `https://wise.com/pay/r/07D4DQeMK9HWtA4` from the canonical generator template. Amir corrected immediately: "wise link was just for that specific client dont use that everywhere its specific 2k link". The Edwin Ardila generator's `WISE_LINK = "https://wise.com/pay/r/07D4DQeMK9HWtA4"` is correct *for that file* but is NOT a fleet constant.

**How to apply:**
1. Wise pay links go in invoices ONLY when Amir generates a fresh request for that exact invoice amount. Default behavior: **DO NOT include a Wise pay-link URL** unless explicitly given for this invoice.
2. The Wise option can still be offered, but as either:
   - "Wise → recipient: Muhammad Amir Nazir (info@ecosire.com)" (no URL — payer creates their own send) — this is the safe default; OR
   - The actual per-invoice link Amir generates and pastes in.
3. **Bank rail (Meezan IBAN `PK73MEZN0022040105909195`, Muhammad Amir Nazir, Current Account) IS a stable fleet constant** — that one stays the same across all USD-via-SWIFT clients.
4. When updating canonical `scripts/client_docs/ecosire_invoice_template.py` (rebuild after the 2026-05-02 wipe), make `WISE_LINK` a per-invoice parameter with default `None`, NOT a hard-coded constant.
5. When asking the user for invoice inputs upfront, include "Wise pay link for THIS invoice (or skip Wise option)" as a question — don't assume reuse from a prior client.

**Edwin Ardila's link `07D4DQeMK9HWtA4` is locked to his $2,000 invoice. Treat it as confidential to that engagement and do not propagate.**
