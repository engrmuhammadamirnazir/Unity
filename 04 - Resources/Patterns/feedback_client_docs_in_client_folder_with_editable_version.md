---
type: pattern
tags: [pattern, client-docs, letterhead, branding, deliverables, hive-mind]
aliases: [Client Docs Pattern, Letterhead Pattern, Client Doc Format Rule]
created: 2026-05-04
updated: 2026-05-04
source: D--Development/memory/feedback_client_docs_in_client_folder_with_editable_version.md
---

# Pattern — Client documents go in client folder; always ship PDF + editable DOCX

## Two-part rule

**Part 1 — Output location.** Every client-facing document MUST be written to the client's organised folder, not to scratch paths like `D:/<workspace>/tmp/`. Canonical client root: `D:\EcosireClients\ActiveClients\<Client-Slug>\`. Documents go under `docs/`. The generator script lives in the same `docs/` folder so the document is reproducible from one place.

**Part 2 — Always two formats.** Every client-facing document ships as a pair:
1. **PDF** — locked layout, signature-ready, print-clean.
2. **DOCX** — editable; client can type answers, mark up, sign in directly. For Q/A docs the DOCX must include explicit "Answer:" labels + underline-row blanks (3–4 rows of `"_" * 95`) so the client can type or print-and-handwrite.

Both files share an identical filename stem (`<Client>_<DocPurpose>_<YYYY-MM-DD>`) and the canonical ECOSIRE letterhead at native aspect ratio.

## Why

- Earlier attempt put the deliverable in `D:/Development/tmp/` — easy to lose, not associated with the client, no audit trail. Client-folder organisation gives you a chronological record per client.
- PDF-only forces the client to annotate by hand and send a photo, or copy questions into a separate doc. Both add friction.
- An aspect-squashed letterhead OR a header/footer crop pair (instead of the full-page design) makes the client perceive the brand borders as "cut off" — the side decorations on the ECOSIRE letterhead don't appear in the header/footer crop pair, only in `letterhead_full.png`.

## How to apply

### 1. Find or create the client folder

```bash
# Look for existing folder
ls "D:/EcosireClients/ActiveClients/" | grep -i <client>
# Create if missing
mkdir -p "D:\EcosireClients\ActiveClients\<Client-Slug>\docs"
```

### 2. Generator script in the client folder

Convention: filename starts with underscore (`_generate_<doc-purpose>_<YYYY_MM_DD>.py`) so it sorts above deliverables. Single Python data structure feeds two builders (`build_pdf()` + `build_docx()`).

### 3. Use the FULL letterhead PNG

Use `D:/Company/ECOSIRE (PRIVATE) LIMITED/05 - Brand & Identity/Letterhead/letterhead_full.png` (1191×1685 px, A4 aspect 0.707). NOT the split `letterhead_header.png` + `letterhead_footer.png` — these crops omit the mid-left triangle, bottom-left triangle, and right-side continuation that make the brand feel complete.

### 4. Page size = A4

ReportLab `pagesize=A4` (595.27 × 841.89 pt). python-docx `section.page_width = Inches(8.27); section.page_height = Inches(11.69)`. Aspect matches the letterhead exactly — no distortion or letterboxing.

### 5. PDF — full-page background via `onPage` callback

```python
from reportlab.lib.pagesizes import A4
from reportlab.platypus import BaseDocTemplate, PageTemplate, Frame
from reportlab.lib.utils import ImageReader

def _draw_letterhead(c, doc):
    c.drawImage(ImageReader(LETTERHEAD_FULL), 0, 0,
                width=PAGE_W, height=PAGE_H, mask='auto')
    c.setFont('Helvetica', 8)
    c.drawRightString(PAGE_W - MARGIN_R, 60, f'Page {doc.page}')

doc = BaseDocTemplate(out, pagesize=A4,
                     leftMargin=60, rightMargin=60,
                     topMargin=130, bottomMargin=100)
frame = Frame(60, 100, PAGE_W - 120, PAGE_H - 230, id='body')
doc.addPageTemplates([PageTemplate(id='lh', frames=[frame],
                                   onPage=_draw_letterhead)])
```

Body margins: top ≈ 130 pt clears the top-right wedge + ECOSIRE logo block. Bottom ≈ 100 pt clears the diagonal stripes. L/R ≈ 60 pt clears the small side triangles.

### 6. DOCX — full-page background via behind-text anchored picture

python-docx has **no direct API** for behind-text anchoring. Pattern that works:

```python
from docx.shared import Inches, Pt
from docx.oxml.ns import qn
from docx.oxml import parse_xml

def _add_full_page_letterhead(section, image_path, page_w_in, page_h_in):
    header = section.header
    p = header.paragraphs[0]
    p.paragraph_format.space_before = Pt(0)
    p.paragraph_format.space_after = Pt(0)
    run = p.add_run()
    # Add inline first via python-docx helper
    run.add_picture(image_path,
                    width=Inches(page_w_in), height=Inches(page_h_in))
    drawing = run._element.find(qn('w:drawing'))
    inline = drawing.find(qn('wp:inline'))

    cx = int(round(page_w_in * 914400))
    cy = int(round(page_h_in * 914400))

    docPr = inline.find(qn('wp:docPr'))
    cNvGraphicFramePr = inline.find(qn('wp:cNvGraphicFramePr'))
    graphic = inline.find(qn('a:graphic'))

    anchor_xml = f'''<wp:anchor
        xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing"
        xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main"
        distT="0" distB="0" distL="0" distR="0"
        simplePos="0" relativeHeight="1" behindDoc="1" locked="0"
        layoutInCell="1" allowOverlap="1">
      <wp:simplePos x="0" y="0"/>
      <wp:positionH relativeFrom="page"><wp:posOffset>0</wp:posOffset></wp:positionH>
      <wp:positionV relativeFrom="page"><wp:posOffset>0</wp:posOffset></wp:positionV>
      <wp:extent cx="{cx}" cy="{cy}"/>
      <wp:effectExtent l="0" t="0" r="0" b="0"/>
      <wp:wrapNone/>
    </wp:anchor>'''
    new_anchor = parse_xml(anchor_xml)
    if docPr is not None: new_anchor.append(docPr)
    if cNvGraphicFramePr is not None: new_anchor.append(cNvGraphicFramePr)
    if graphic is not None: new_anchor.append(graphic)
    drawing.remove(inline)
    drawing.append(new_anchor)
```

Set `section.header_distance = Inches(0); section.footer_distance = Inches(0)` so the anchor positions from the page edge. Verify with `zipfile`:

```python
import zipfile
with zipfile.ZipFile(out_docx) as z:
    xml = z.read('word/header1.xml').decode('utf8')
    assert 'behindDoc="1"' in xml
```

### 7. DOCX answer space (for Q/A docs)

After each question put a bold/italic "Answer:" label and 3 underline rows of 95 underscores. This works for typing AND for printed handwriting; the client doesn't have to choose.

### 8. Verify before sending

Render PDF page 1, page 2, last page to PNG and `Read` them. Catches letterhead clipping, missing text, broken page breaks before the doc reaches the client. The client must never be the proof-reader.

## Concrete proven case (2026-05-04)

`D:\EcosireClients\ActiveClients\Suleman-Remittance\docs\`:
- `_generate_clarification_questions_2026_05_04.py` — single-source generator
- `Suleman_Clarification_Questions_2026-05-04.pdf` — 9 pages, A4, full letterhead behind text
- `Suleman_Clarification_Questions_2026-05-04.docx` — same content, editable, `<wp:anchor behindDoc="1">` verified in `word/header1.xml`

Iteration history (4 deploys to land it):
1. First draft in `D:/Development/tmp/` with header/footer at squashed 70 pt height (24% vertical squash).
2. Aspect-correct heights but still split header/footer — moved to client folder, added DOCX with answer lines.
3. Switched to `letterhead_full.png` as full-page background — A4 page, side decorations now visible.
4. Address line trimmed to "Mr. Suleman Arif" only.

## Related fleet patterns

- [[feedback_propagate_learning_not_artifacts]] — why this pattern lives in Unity (universal lesson) but the generator scripts stay in workspace `.claude/` (project-specific implementation).
