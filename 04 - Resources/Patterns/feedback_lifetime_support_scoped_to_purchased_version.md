---
name: Lifetime support is scoped to the Odoo version purchased
description: When selling a module for a specific Odoo version, lifetime updates and support apply ONLY to that version — never write "lifetime updates across Odoo 17/18/19" on a single-version invoice
type: feedback
originSessionId: c88020f1-0418-44b6-a8a5-a2de0122cb87
---
When ECOSIRE sells a module to a client for a specific Odoo version (the version their instance is running), the lifetime update and support entitlement is scoped to **that version only**, not all three versions (17 / 18 / 19) we maintain in the codebase.

The fact that the same module exists in all 3 odoo{17,18,19}/server/addons/ trees is for ECOSIRE's own backporting workflow — it does NOT imply the customer gets all 3 binaries with one purchase.

**Why:** Caught 2026-05-06 when generating Bimal Tamrakar's $250 Daraz invoice — line description had "lifetime updates across Odoo 17 / 18 / 19" by default (carried over from the Edwin Ardila template). Amir corrected fleet-wide: "when we sell module for specific version lifetime support is only included for that version". Cross-version coverage would mean an unbounded migration path liability — every time the client moves to a new major version, we'd be on the hook to migrate their data + provide the new-version build at no cost.

**How to apply:**
1. **Always ask the client's Odoo version** before drafting an invoice or proposal line for a single-module sale.
2. **Line wording** — instead of "lifetime updates across Odoo 17 / 18 / 19", write "lifetime updates for Odoo {VERSION}" where VERSION is the one purchased (e.g., "lifetime updates for Odoo 18").
3. **Cross-version upgrade is a separate paid line** — if the client later migrates to a newer Odoo major (e.g., v18 → v19), that's a new purchase or a preferential-rate upgrade, not auto-included.
4. **Edwin Ardila is a deliberate exception** — his $2,000 / 12-module bundle includes "free upgrade to Odoo v20" (releases late 2026) + "v21+ at preferential rate" as a closing concession. That language is invoice-specific and was a same-day-cash-close concession, NOT a fleet template.
5. **App Store listings** are also single-version — buyer gets the binary for the Odoo version they purchased on Odoo's App Store; if they're on a different version they buy that version's listing.
6. **In WhatsApp / email replies** to multi-version questions: explain that the module exists in all 3 versions but the purchase is per-version; we can quote a bundle if they need multiple versions.
7. **When asking the user for invoice inputs upfront**, include "Client's Odoo version (17 / 18 / 19 / SH-which?)" as a required question — same priority as amount and email.
