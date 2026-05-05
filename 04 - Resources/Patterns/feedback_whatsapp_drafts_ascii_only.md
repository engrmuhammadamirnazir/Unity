---
name: WhatsApp / forward-to-client drafts must be plain ASCII
description: Never use Unicode arrows, em/en dashes, smart quotes, ellipsis, or other non-ASCII typography in any block intended for the user to copy-paste into WhatsApp or another forwarding channel - they break copy/paste flow and render unprofessionally on the client side
type: feedback
originSessionId: d00b0c80-6f86-4c09-95cb-7a05a1fef95a
---
When drafting a reply for the user (Amir) to copy-paste and forward to a client (Niccolo, Suleman, Hussam, Kamal, etc.), the draft MUST be 7-bit ASCII only.

**Why:** Niccolo flagged 2026-05-04 on the BWG 588462 / INV 00555 thread: a draft I wrote contained the right-arrow character and Niccolo replied "message does not copy cleanly then. never use that again". Either the arrow doesn't survive Claude Code terminal -> clipboard -> WhatsApp, or the recipient's render breaks. Same risk applies to every non-ASCII typography glyph - em dash, en dash, curly quotes, ellipsis, bullets, multiplication sign, etc. The result is unprofessional and the user has to manually clean the draft before sending.

**How to apply:**
- For any block I label "Draft reply to <client>", "WhatsApp reply", "Email draft", or similar copy-paste-to-forward output: use only 7-bit ASCII characters in the draft body
- Substitution table:
  - right-arrow -> ", so" / ", then" / period and new sentence
  - em dash -> " - " (space-hyphen-space) or split into two sentences
  - en dash -> "-" or "to" (e.g., "Mon-Fri" or "10 to 12")
  - curly quotes (left/right double, left/right single) -> straight ASCII double / single quote
  - ellipsis glyph -> three ASCII dots "..."
  - bullet glyph -> "- " or "1. " numbered list
  - multiplication sign -> "x" or "*"
  - non-ASCII whitespace (nbsp, em space) -> regular space
- Currency and arithmetic: $, /, +, -, %, =, (), ASCII only
- Tables in Markdown (`|---|---|`) are fine when rendered in MY response to the user, but DO NOT put a markdown table inside a copy-paste-to-WhatsApp draft - prose or simple "Label: value" lines copy cleaner
- This rule does NOT apply to internal artifacts: Markdown reports, accountant-agent reports, session memory files, scripts, GitHub commit messages, anything that doesn't pass through a copy/paste/WhatsApp/SMS pipeline. Those keep full Unicode for readability.

**Trigger pattern in my responses:** any heading like "## Draft reply to..." / "## WhatsApp reply" / "**Forward this to <client>:**" / a blockquote that the user will copy verbatim. If the block is for the user to read but NOT forward, no constraint.
