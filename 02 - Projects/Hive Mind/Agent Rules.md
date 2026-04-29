---
type: reference
category: hive-mind, agent-rules
tags: [agent-rules, cross-project, hive-mind, client-work]
created: 2026-04-29
updated: 2026-04-29
---

# Hive Mind — Cross-Project Agent Rules

> Policies that apply to **every** Claude Code session in any workspace, regardless of which project is active. Distinct from project-local rules in each `CLAUDE.md`. When project-local rules and these conflict, project-local wins.

> Append rules here when a pattern emerges that's clearly cross-project. Rule entries should be short, prescriptive, and reference an originating session.

---

## Rule R-001 — No Claude / AI attribution in client-owned repos

**Origin:** 2026-04-29 — Future Vision Waredat ZATCA engagement. User intercepted before second commit. See `D:/Development/.claude/projects/D--Development/memory/feedback_no_claude_attribution_in_client_repos.md`.

**Rule:** When committing to a CLIENT-OWNED GitHub repository (e.g. Hussam's `waredat-erp`, Soovah/Quicken's wayfair fork, Diamond/STIG repos, Obliq deployment repos, or any other client-controlled repo), commits MUST:

1. Use Amir's personal git identity:
   - `user.name = Muhammad Amir Nazir` (or with `(Ecosire)` suffix)
   - `user.email = engr.mohammadamirnazir@gmail.com`
2. NOT include `Co-Authored-By: Claude <noreply@anthropic.com>` lines
3. NOT include `🤖 Generated with [Claude Code]` or any AI/LLM attribution
4. Be purely technical in tone — what changed, why, test evidence

**Why:** Client-paid deliverables. Visible AI attribution undermines the professional value of the engagement and introduces unnecessary IP/auditability questions. This is reputation protection.

**Counter-rule (where AI attribution IS fine):**
- Ecosire's own org repos (`github.com/ecosire/*`)
- Personal experimental repos
- Demo projects
- Internal `D:/Development` workspace

**How to apply:**
- Before any `git commit` against a client repo, check `git config user.name` and `git config user.email`
- Never pass `--trailer` flags adding Claude attribution
- For PRs via `gh pr create`, the description must be purely technical

---

## Rule R-002 — Never share secrets via WhatsApp / chat / email

**Origin:** 2026-04-29 — Future Vision Waredat deployment prep. User asked "should I give you Replit logins?" Answer: no.

**Rule:**
- Never paste credentials, OTPs, or secret keys into a chat transcript
- Generate secrets DIRECTLY ON the production environment they'll be used in (e.g. `python -c 'from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())'` run on the target Replit Shell, not locally)
- Backup to 1Password / Bitwarden after generation
- For shared access, prefer collaborator/IAM grants over shared credentials

**Why:** Chat transcripts are logged. Future Claude sessions reading the transcript would see the secret. Lost-key audit trails matter for forensics.

**Counter-pattern:** Reference credentials by file path (`Unity/03 - Areas/Credentials & Access/Server & Key Inventory.md`), never by value.

---

## Rule R-003 — Convert relative dates to absolute when persisting to memory

**Origin:** Implicit in `~/.claude/CLAUDE.md` auto-memory protocol — restated here for cross-workspace agents.

**Rule:** When saving any memory file (project-local or Unity), relative dates in user input ("Thursday", "next week", "in 2 days") MUST be converted to absolute ISO dates (`2026-05-01`) before write.

**Why:** Memory persists across sessions. "Thursday" in a memory written 6 months later is meaningless.

---

## Rule R-004 — Verify before recommending from memory

**Origin:** `~/.claude/CLAUDE.md` auto-memory protocol — restated here.

**Rule:** A memory entry that names a specific function, file, flag, or path is a claim that it existed when the memory was written. Before recommending it to the user (especially for an action they'll act on), verify:

- File paths still exist (use `Glob` or `Read`)
- Functions/flags still in code (use `Grep`)
- Module versions match what memory says

If memory says X exists and current state shows X is renamed/removed/never-merged, trust current state and update or remove the stale memory entry.

---

## Format for new rules

```
## Rule R-NNN — Short imperative title

**Origin:** YYYY-MM-DD — workspace/session reference and what triggered the rule.

**Rule:** The prescriptive statement. Numbered list if multi-part.

**Why:** One-line motivation.

**How to apply:** Concrete step(s) the agent takes to enforce the rule.
```

Keep each rule under ~25 lines. If a rule needs more, link to a topic note instead.
