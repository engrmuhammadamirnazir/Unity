---
name: User speaks English; client language only in client-facing artifacts
description: Talk to Amir in English even when the topic is a non-English client. Use the client's language only inside the actual client-facing artifacts (WhatsApp drafts, email replies, proposal/invoice copy). Generalizes to Spanish (Edwin, Carlos), Arabic (Hussam/Future Vision), and any future non-English client.
type: feedback
tags: [communication, client-language, english-with-amir, fleet-wide-rule]
created: 2026-05-06
updated: 2026-05-06
source-workspace: D:/Development
source-memory: ~/.claude/projects/D--Development/memory/feedback_user_english_only_spanish_for_clients.md
---

**Rule:** Talk to Amir in English. The client language is for the client only.

**Why:** Amir told me directly during the Edwin Ardila invoice flow on 2026-05-06 ("talk to me in english... i dont understand spanish spanis is for client only"). I had been mixing Spanish and English in my own status updates / explanations to him because the client is Spanish-speaking, which made the conversation harder for him to read. He doesn't speak Spanish; the Spanish is purely a translation layer for the client. Same applies to any non-English client.

**How to apply:**

- All assistant-to-Amir text (status updates, recommendations, explanations of what I'm about to do, summaries, error reports, brief one-line acknowledgements) → **English**.
- Client-facing artifacts (`*_WhatsApp_Reply_*.txt`, proposal PDFs, invoice PDFs, email drafts intended to be sent to the client, on-letterhead documents) → **target language of the client** (Spanish for Edwin / Carlos / future LATAM-Spain leads; Arabic for Hussam / Future Vision; potentially others).
- When pasting a draft for Amir to copy-paste, do not mix the client's language into MY framing/preamble around it. The block intended for the client is in the client's language; everything else (intro, context, follow-up notes, file paths) stays English.
- The internal team-language between Amir and me is English; localized output lives only in artifacts.

**Why this is fleet-wide and not workspace-local:**

- Applies to any agent in any workspace touching a client whose primary language differs from English.
- Active examples: Edwin Ardila (Colombia/Spanish), Carlos / Distribuidora Andina (Spanish), Hussam Almohaimeed / Future Vision Waredat (Arabic possible), any future LATAM/Spain/Saudi/UAE leads.
- The sales-proposal agent already follows this convention (drafts client reply in client language, internal summaries in English) — agents working without that agent should match the pattern.

**Related:**

- [[feedback_whatsapp_drafts_ascii_only]] — that's about character set in the artifact; this rule is about which language goes to whom.
- [[feedback_client_docs_in_client_folder_with_editable_version]] — file-location rule for client deliverables.
