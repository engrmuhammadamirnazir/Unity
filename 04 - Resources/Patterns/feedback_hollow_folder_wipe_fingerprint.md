---
name: Hollow folders are the diagnostic fingerprint of a working-tree wipe
description: A folder with subcomponent shells (models/ controllers/ views/) but no __manifest__.py is the canonical signature of a partial filesystem wipe — the wipe killed source files but left directory structure. Detect on cron, alarm on non-zero count.
type: feedback
tags: [pattern, fleet-wide, disaster-recovery, monitoring]
source-workspace: D:/Development
captured: 2026-05-04
---

The May 2 2026 wipe of D:/Development hollowed out 143 odoo19 + 122 odoo17 module folders. The fingerprint:
- Folder timestamps recently changed (May 2 08:42)
- `__pycache__/` survives (older Apr 16/17 timestamps, untouched)
- Subfolder shells `models/ controllers/ views/ tests/ wizard/` exist (all recent timestamp)
- ALL `__init__.py`, `__manifest__.py`, `*.py`, `*.xml`, plus `data/ security/ report/ static/ i18n/` directories DELETED

This pattern is identical to the `.claude/` wipe of the same day. It's not a random delete — it's a working-tree wipe that targeted source files but spared the pycache (which has different inode patterns).

**Why this is diagnostic:** A normal "user deleted by mistake" event removes whole folders or removes files in a discoverable pattern (one feature, one module). A working-tree wipe shows up as MANY hollow folders simultaneously — the structure is preserved (so directory listing looks normal at first glance) but the actual code is gone. Without an automated detector, this passes silently.

**How to apply:** Run `python <repo>/scripts/audit/hollow_folder_check.py` daily AND before any deploy. Adapt the detector for any repo that follows the Odoo module convention (or analogous: each subfolder has a manifest at root).

**Detection rule** (encoded in `D:/Development/scripts/audit/hollow_folder_check.py`):
```python
SUBCOMPONENT_HINTS = {"models", "controllers", "views", "data", "security", "report",
                      "wizard", "tests", "static", "i18n", "demo", "wizards"}

# A folder is hollow iff:
# (a) it does NOT contain __manifest__.py
# (b) it DOES contain at least one folder name in SUBCOMPONENT_HINTS
```

Generalize for non-Odoo repos: replace `__manifest__.py` with the project's required-at-root manifest filename (`package.json`, `Cargo.toml`, `pyproject.toml`, etc.) and `SUBCOMPONENT_HINTS` with that ecosystem's typical subfolders.

**Already applied:** `D:/Development/scripts/audit/hollow_folder_check.py` runs every night before the daily auto-commit; refuses to snapshot a damaged tree (alerts via `tmp/backup_alerts.log` instead).

**Sister gotchas:**
- [[feedback_backup_pipeline_must_verify_push]] — silent push failures and silent wipes are both invisible without explicit detection layers
- [[Patterns - Disaster Recovery from GitHub]] — the procedure to follow once a wipe is detected
