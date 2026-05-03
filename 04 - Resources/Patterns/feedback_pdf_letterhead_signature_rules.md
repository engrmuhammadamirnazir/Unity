---
type: pattern
tags: [pattern, client-deliverables, branding, pdf, letterhead, reportlab, signature]
aliases: [PDF Letterhead Rules, ECOSIRE Letterhead PDF Rules, ReportLab Letterhead Pattern]
created: 2026-05-04
updated: 2026-05-04
source-memory: ~/.claude/projects/D--ECOSIRE-COM/memory/feedback_pdf_letterhead_signature_rules.md
---

# Pattern — ECOSIRE Letterhead PDF Rendering Rules

> Sister pattern to [[feedback_client_docs_in_client_folder_with_editable_version]]. Eight hard rules for generating any client-facing PDF on ECOSIRE letterhead with the digital signature tool. Caught five recurring layout bugs across two consecutive sessions (2026-05-03 Mineralissima + 2026-05-04 Suleman clarification doc).

## The rules (apply in EVERY workspace producing client deliverables)

### 1. Use `letterhead_full.png`, NEVER just header_strip + footer_strip

`D:\Company\ECOSIRE (PRIVATE) LIMITED\05 - Brand & Identity\Letterhead\` contains:

| Asset | Size | What it is |
|-------|------|------------|
| `letterhead_header.png` | 1191 × 180 | Top strip ONLY |
| `letterhead_footer.png` | 1191 × 122 | Bottom strip ONLY |
| `letterhead_full.png` | 1191 × 1685 | **Complete A4-aspect page** with corner wedges, side accents, contact bar, triangle band |

Always draw `letterhead_full.png` as a full-page background on every page. Drawing only the header + footer strips makes the side decorative wedges and corner triangles disappear — the page looks "cut off."

In ReportLab/`onPage`:
```python
canvas.drawImage(str(FULL_PNG), 0, 0, width=PAGE_W, height=PAGE_H, mask="auto")
```

In python-docx (`<wp:anchor behindDoc="1">` in `word/header1.xml`) — see [[feedback_client_docs_in_client_folder_with_editable_version]] for the exact XML.

### 2. Safe content margins for `letterhead_full.png`

Measured against the 1191×1685 PNG at A4 scale (~0.5 px/pt):

| Edge | Required margin | Clears |
|------|-----------------|--------|
| Top    | **55 mm** | ECOSIRE logo band + top-right wedge (~150 px from top) |
| Bottom | **30 mm** | Contact bar + triangle band (~155 px from bottom) |
| Left   | **20 mm** | Bottom-left wedge sits at x < 22 pt in lower portion |
| Right  | **20 mm** | Top-right wedge sits at x > 569 pt in top portion |

Smaller margins cause text to collide with the corner wedges.

### 3. Escape literal HTML/XML tags in any prose passed to `Paragraph()`

ReportLab's paraparser interprets `<title>`, `<meta>`, `<link rel=...>`, `<head>` etc. as HTML tags and crashes with `paraparser: syntax error: invalid attribute name`. When writing prose about HTML/SEO tags (common in audit reports), always escape:

```
<title>     → &lt;title&gt;
<meta>      → &lt;meta&gt;
<link rel=  → &lt;link rel=&quot;canonical&quot;&gt;
<head>      → &lt;head&gt;
```

Same applies to issue table cell strings and any other Paragraph input.

### 4. `ecosire_sign_pdf.py` defaults are tuned for ONE-signer NDA layout, NOT two-column proposal blocks

The defaults `sig_xy=(135, 186)`, `date_xy=(130, 131)`, `stamp_xy=(400, 115)` in `D:\Company\ECOSIRE (PRIVATE) LIMITED\05 - Brand & Identity\Signatures\ecosire_sign_pdf.py` were calibrated for the Amalfi NDA (single signature column, signature line low on page). They WILL NOT work for a two-column "For ECOSIRE / For Client" proposal layout, where each cell is only ~241 pt wide and signature lines sit much higher on the page.

Always import `sign_pdf` and override per-document. For the standard proposal sig_table with 16-pt left padding at TOP_M=55mm:

```python
sig_xy=(75, 542),     # cursive on the ECOSIRE underline (left column)
sig_width=125,        # narrower than default 150 to fit 210pt-wide cell
date_xy=(102, 498),   # date right after "Date: " label, lowered 5pt for underscore baseline
stamp_xy=(210, 525),  # stamp to the RIGHT of cursive — DO NOT overlap
stamp_size=70,        # smaller than default 125 to fit beside cursive in 210pt cell
```

### 5. Stamp must NOT overlap the cursive signature

Default stamp_size=125 with default placement causes the stamp to sit ON TOP of the cursive name, obscuring it. In a real wet-signed document the stamp goes BESIDE the signature, not over it.

For two-column layouts: `stamp_x = sig_x + sig_width + 5 pt` (immediately right of cursive, sharing the same vertical band). Smaller stamp_size (~70 pt) fits cleanly.

### 6. Date drawn at same Y as the underscores appears ABOVE them

The Helvetica `_` glyph sits ~4-6 pt below the baseline of regular characters. So `c.drawString(x, y, "03 May 2026")` and the rendered `Date: ____` line at the same baseline Y will visually MISALIGN — the date text appears floating above the underscores.

**Solution:** lower `date_xy` y by **5 pt** below the cell text's baseline.

### 7. `s.append(PageBreak())` after overflowing content produces ORPHAN/EMPTY PAGES

If the last block on a page overflows by 1-2 pt, ReportLab moves it to the next page automatically. An explicit `PageBreak()` immediately after that block then forces a SECOND page break, leaving a near-empty page in the middle of the document.

The Mineralissima audit went from 13 pages to 11 pages (two orphan pages eliminated) just by replacing 13 of 16 hard `PageBreak()` calls with `CondPageBreak(7 * cm)`.

**Default rule:** use `CondPageBreak(7 * cm)` between sections — only breaks if less than 7 cm of vertical space remains. Reserve hard `PageBreak()` for: (a) cover-to-body transitions, (b) signature page (must start fresh).

### 8. `ecosire_doc_generator` module is NOT on this machine

Legacy proposal scripts under `D:\EcosireClients\ActiveClients\Suleman-Remittance\` and `Track-Congo\` reference `from ecosire_doc_generator import EcosireDoc` with `sys.path.insert(0, "D:/Development/scripts/client_docs")`. That folder does **NOT** exist on the current machine — the module was lost or never migrated. Build new client PDFs directly with `reportlab.platypus` (BaseDocTemplate + Frame + PageTemplate) using `letterhead_full.png` as the full-page background per Rule 1.

## Canonical reusable template

Clone these for every new client deliverable; change content only, the layout machinery is correct:

- `D:\EcosireClients\ProspectiveClients\Mineralissima\build_deliverables.py` — multi-section audit + proposal builder
- `D:\EcosireClients\ProspectiveClients\Mineralissima\sign_proposal.py` — two-column signature block coordinates

## Why these rules exist

All eight bugs surfaced in a single Mineralissima session (2026-05-03 → 2026-05-04 early hours) and burned ~6 iterations:

1. First version showed only header strip — Amir flagged "letterhead is cutout and its page borders are not visible"
2. Three rebuild iterations crashed on `<title>` / `<meta>` / `<link rel=canonical>` literal text
3. First signed proposal had stamp slammed on top of the cursive name
4. Second had stamp still partially overlapping
5. First two signed versions had date "03 May 2026" floating above the underline
6. Audit was 13 pages with two near-empty orphan pages until CondPageBreak

Saving these so the next client proposal (or audit, or contract) starts at iteration 1, not iteration 6.

## Cross-references

- [[feedback_client_docs_in_client_folder_with_editable_version]] — sister pattern: where to put the file, PDF + DOCX pair, A4 page size, full-page letterhead background
- [[PATTERNS - INDEX]] — fleet-wide pattern index
- Source memory: `~/.claude/projects/D--ECOSIRE-COM/memory/feedback_pdf_letterhead_signature_rules.md`
- First applied at: `D:\EcosireClients\ProspectiveClients\Mineralissima\` (2026-05-03 → 2026-05-04)
