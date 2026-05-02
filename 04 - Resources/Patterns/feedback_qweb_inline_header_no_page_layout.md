---
name: QWeb reports — inline `<div class="header">` does NOT produce page-level headers; must use `web.external_layout`
description: A QWeb report template with `<div class="header">` and `<div class="footer">` placed INSIDE the body content renders the header once at the top of the rendered HTML and the footer once at the bottom — wkhtmltopdf does NOT promote those to page-level header/footer that runs on every page. To get proper running headers/footers/page numbers on every page, the template must `t-call="web.external_layout"` (or one of its variants) which routes the header/footer divs through Odoo's report-rendering machinery to wkhtmltopdf's `--header-html` / `--footer-html` flags.
type: feedback
updated: 2026-05-03
originSessionId: 2026_05_03b_remittance_v10_0_39_demo_prep
---

## The rule

**To get a running header/footer on every page of a QWeb PDF report, t-call `web.external_layout`.** Don't hand-roll a `<div class="header">` inside the report body — wkhtmltopdf doesn't see it as a page-level element.

## The pattern that fails

```xml
<!-- WRONG — header/footer appear ONCE in the rendered HTML, not on each page -->
<template id="my_layout">
    <div class="header" style="...">
        <strong>COMPANY NAME</strong>
        <div>address...</div>
    </div>
    <div class="article">
        <t t-out="0"/>  <!-- body content goes here -->
    </div>
    <div class="footer" style="...">
        <span>page <span class="page"/> of <span class="topage"/></span>
    </div>
</template>

<template id="my_report">
    <t t-call="web.html_container">
        <t t-foreach="docs" t-as="o">
            <t t-call="my_layout">
                <div class="page">
                    ...content...
                </div>
            </t>
        </t>
    </t>
</template>
```

Symptoms:
- Header appears ONLY on page 1 (top of the rendered HTML)
- Footer appears ONLY on the last page (bottom of the rendered HTML)
- No page numbers
- Multi-page reports look unprofessional next to native Odoo invoices

Why: `wkhtmltopdf` constructs page-level headers/footers by running the report engine in three passes — it asks Odoo to generate a separate HTML fragment for header (`--header-html`) and footer (`--footer-html`). Odoo's standard `web.external_layout` (and its variants `web.external_layout_standard`, `_boxed`, `_clean`, `_striped`) define a contract where the engine extracts the `<div class="header">` and `<div class="footer">` and passes them to wkhtmltopdf as the page-level fragments. A primary template that just inlines those divs in the body sidesteps this contract — they end up as plain body content.

## The fix

Either:

**Option 1 — t-call `web.external_layout` directly in your report:**
```xml
<template id="my_report">
    <t t-call="web.html_container">
        <t t-foreach="docs" t-as="o">
            <t t-call="web.external_layout">
                <div class="page">
                    ...content...
                </div>
            </t>
        </t>
    </t>
</template>
```

`web.external_layout` dispatches to whichever document layout the company has selected (Settings → Companies → Document Layout: Standard / Boxed / Clean / Striped). Branding (logo, accent colour, header text, footer text, bank account info) is configured there, no template edits needed.

**Option 2 — Make your custom layout DELEGATE to `web.external_layout`:**

```xml
<template id="my_layout">
    <!-- Resolve `o` and `company` defensively for legacy callers -->
    <t t-if="not o and docs" t-set="o" t-value="docs[:1] and docs[0]"/>
    <t t-if="not company and o and 'company_id' in o"
       t-set="company" t-value="o.company_id.sudo()"/>
    <t t-if="not company" t-set="company" t-value="env.company.sudo()"/>

    <!-- Delegate -->
    <t t-call="web.external_layout">
        <t t-out="0"/>
    </t>
</template>
```

This way every existing report that already calls `t-call="my_layout"` keeps working but gets proper page-level headers/footers automatically. **This was the chosen fix on Suleman Remittance v10.0.39** — 17 reports were calling `external_layout_remittance`, the layout was rewritten to delegate, all 17 reports got fixed in one edit.

## Real incident — 2026-05-03 Suleman Remittance demo prep

User reported: "Transaction voucher does not have any header and footer like other Odoo external reports do, also do check reports."

Diagnosis: `report/external_layout.xml` (`external_layout_remittance`) hard-coded `<div class="header">` with "ECOSIRE PRIVATE LIMITED" branding and `<div class="footer">` with page-counter spans inline. wkhtmltopdf saw these as body content. 17 reports across the module shared this layout (transaction voucher, partner statement, balances snapshot, flow statement, partner ledger detailed, compliance audit, account snapshot, suspense aging, bank reconciliation statement, sonstig breakdown, activity summary, outstanding balances, transactions list, shipments list, shipment doc pack, partner compliance profile, logistics cost summary).

Fix: rewrote `external_layout_remittance` to delegate to `web.external_layout`. All 17 reports now use Daleel's configured Document Layout (primary_color=#5e4766 purple, layout id=206). Hard-coded "ECOSIRE PRIVATE LIMITED" branding gone — proper company branding inherited from Settings → Companies → Document Layout.

Bonus benefit: when Daleel's accountant changes the document layout in Settings, every report updates instantly with no developer involvement.

## Detection signals

- "The PDF has the header on page 1 only / footer on the last page only" — primary signal
- "No page numbers"
- "Reports don't match invoices' look-and-feel" — invoices use `web.external_layout`, custom reports usually don't
- Reading the layout template and seeing `<div class="header">` outside any `t-call="web.external_layout"` — code smell

## How to apply

1. **In any new report**: always `t-call="web.external_layout"` for the body wrapper. Don't roll your own.
2. **For existing modules with custom layouts**: rewrite the custom layout to delegate (Option 2 above) — single edit, all reports fixed.
3. **For company-specific branding**: configure via Settings → Companies → Document Layout, NOT by editing the report template.
4. **Test**: print a multi-page report (e.g. partner statement with many lines). Verify header on page 2+, footer + page numbers on every page.

## Reusable across

Every Odoo workspace with custom QWeb PDF reports:
- Client-specific custom reports (Sahara, Obliq, Oenoteca, etc.)
- Module App Store releases that include reports
- Internal reporting modules (analytics, KPI exports, audit trails)
- Anywhere a developer wrote `t-call="<custom_module>.<custom_layout>"` instead of `t-call="web.external_layout"`

The Suleman remittance fix demonstrated that the delegate-to-web.external_layout pattern can retrofit 17 reports with a single template edit — useful for retro-fitting existing modules without touching every report file.

## Related memories

- None directly. New family.

## Reusable detection grep

```
grep -rn 'class="header"' <module>/report/ | grep -v "external_layout"
```

If any hit appears outside an `external_layout_*` template, audit it.
