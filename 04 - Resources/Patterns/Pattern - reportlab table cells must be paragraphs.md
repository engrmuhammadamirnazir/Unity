---
type: pattern
category: document-generation
tags: [reportlab, python-docx, letterhead, proposal, pdf, anti-pattern]
created: 2026-05-12
updated: 2026-05-12
applies-to: [D:/Development, D:/ECOSIRE.COM, D:/ECOSIRE.AI, D:/ECOSIRE.IO]
---

# Pattern — reportlab Table cells must be `Paragraph` objects

> When building Ecosire letterhead PDFs (proposals, invoices, analysis reports) with reportlab, every non-trivial `Table` cell **must** be wrapped in a `Paragraph(text, style)`. Raw Python strings as cell content silently fail in two ways that show up only at rendering time.

## The two failure modes

1. **No word wrap.** Reportlab treats a raw string as a single horizontal line. If the string is wider than the column, it gets clipped or overflows into adjacent columns. Visually: text from one cell collides with text from the next column on the same row.
2. **No HTML entity decoding.** `&amp;`, `&bull;`, `<b>...</b>`, `<i>...</i>` render LITERALLY as those characters. Only `Paragraph` parses reportlab's mini-HTML subset.

## When it bites

Caught on Brad & Brad ERP Analysis Report (2026-05-12). First draft of the 11-page proposal had the Community-vs-Enterprise comparison table showing `&amp;` in cells like "Customer (CRM &amp; loyalty)" and the entire description column overflowed into the next column. User screenshot showed text overlapping across 3 columns on page 3.

Same risk exists on every proposal/invoice generator in every workspace:
- `D:/Development/scripts/client_docs/`
- `D:/EcosireClients/ProspectiveClients/*/01-Contract/_generate_proposal_*.py`
- `D:/EcosireClients/ProjectClients/*/quotations/*.py`
- Any future ECOSIRE.COM admin-side document generator

## The canonical fix

```python
from reportlab.platypus import Paragraph, Table

def _cell(text, style):
    return Paragraph(text, style)

cell_style = ParagraphStyle(
    "cell", parent=styles["BodyText"],
    fontName="Helvetica", fontSize=8.5,
    textColor=INK, alignment=TA_LEFT,
    leading=11.5, spaceAfter=0,
)

rows = [
    [_cell("<b>Functional area</b>", cell_header_style),
     _cell("<b>Odoo Community</b>",   cell_header_style),
     _cell("<b>Odoo Enterprise</b>",  cell_header_style)],
    [_cell("<b>Accounting</b>", cell_style),
     _cell("Basic <b>account</b> module …", cell_style),
     _cell("<b>account_accountant</b>: …", cell_style)],
    # …
]

t = Table(rows, colWidths=[110, 185, 180], repeatRows=1)
t.setStyle(TableStyle([
    ("VALIGN",      (0, 0), (-1, -1), "TOP"),
    ("LEFTPADDING", (0, 0), (-1, -1), 5),
    # … other style rules
]))
```

Also:
- `repeatRows=1` so the header re-prints on each page if the table spans pages.
- For tier cards / multi-element blocks that must not split: wrap the whole block in `KeepTogether([para1, para2, ...])`.
- Use Unicode `•` (U+2022) directly for bullets — `&bull;` encodes to `\x7f` in Helvetica.
- Set `colWidths` summing exactly to the frame content width (`PAGE_W - MARGIN_L - MARGIN_R` = 475 pt for A4 with 60 pt L+R margins).
- Use ASCII apostrophes and consistent dashes: en-dash `–` for ranges (`5–7 days`), em-dash `—` for clauses.

## Reference implementation

`D:/EcosireClients/ProspectiveClients/Brad-and-Brad-Restaurant/01-Contract/_generate_proposal_2026_05_12.py` — full ~850-line proposal generator with the helper, 5 typed tables, KeepTogether tier blocks, repeating headers and consistent typography. Use as the canonical template for any new Ecosire letterhead document.

> Older generators (e.g. `D:/EcosireClients/ProspectiveClients/Edwin-Ardila/01-Contract/_generate_proposal_2026_05_05.py`) pass raw strings to `Table` and happen to work because their cells are short. They are **not** the pattern to copy for any new document.

## Writing-tone rules captured in the same iteration

User feedback on the first draft was "improve documentation writing skill". The cleanup pass produced these reusable rules:

- No marketing claims ("genuinely cheap", "best balance").
- No over-specific unsupported figures ("saves 5 hours / week").
- Neutral specificity — say what the tool does, not how good it is.
- Recommendation reasons given as 5 short labelled bullets, not a paragraph wall.
- En-dash for numeric ranges (`5–7 days`); em-dash for clauses.
- ASCII apostrophes (`don't`, not `don’t` in code; `don't` in prose is fine if the font has the proper curly quote — but consistent).
- Section numbers with two spaces (`1.  What is included…`) so they don't collide with content on narrow widths.

## Related

- [[Ecosire - Document Generation]] (when created — currently this Patterns note is the canonical reference)
- [[Reference - Odoo Pakistan Subscription Pricing]]
- Workspace memory: `~/.claude/projects/D--Development/memory/feedback_pdf_letterhead_table_cells_must_be_paragraphs.md`
