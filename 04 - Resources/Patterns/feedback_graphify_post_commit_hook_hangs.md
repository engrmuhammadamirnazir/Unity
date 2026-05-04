---
name: graphify post-commit hook hangs automated commits on large repos
description: When a git repo has the graphify post-commit hook installed AND the repo has many tracked files, automated commit scripts appear to hang silently after git commit. Bypass hooks during automation via `git -c core.hooksPath=/dev/null commit`.
type: feedback
tags: [pattern, fleet-wide, git, automation, deploy]
source-workspace: D:/Development
captured: 2026-05-04
---

In any automated commit script running against a git repo with many tracked files (60k+) AND a graphify-installed post-commit hook (`./.git/hooks/post-commit` from `graphify hook install`), `git commit` will trigger a knowledge-graph rebuild via `_rebuild_code(Path('.'))` on every commit. On D:/Development with 60k tracked files this rebuild takes minutes and effectively makes the script appear to hang — `git commit` returns a fast SHA, but the script's stdout/log output is consumed by the hook and never resumes until the hook finishes.

Symptom diagnostic via `bash -x`: the trace ends on the `git commit -m '...'` line and never produces lines for the next statement. Yet `git log` shows the commit landed.

**Why:** The hook inherits the script's stdout/stderr file descriptors. While the hook runs, those FDs are kept open, so subsequent script writes appear after a long delay (or get lost if the FD is otherwise consumed). For an unattended cron-like script, this looks like a silent failure — the commit happens but the push step never runs.

**How to apply:** any automated commit on a repo with this hook MUST bypass git hooks via the `-c core.hooksPath=/dev/null` flag for that one invocation:

```bash
git -c core.hooksPath=/dev/null commit -m "chore: ..."
```

This skips pre-commit, commit-msg, AND post-commit hooks for that single git invocation, without disabling the hook globally. Interactive commits (where you want graphify to rebuild) keep using regular `git commit`.

The flag is preferred over `git commit --no-verify` because `--no-verify` only skips pre-commit + commit-msg hooks, NOT post-commit (which is the offender for graphify).

**Already applied in:** `D:/Development/scripts/backup/daily_dev_commit.sh` (the daily auto-commit + push that runs in the composite scheduled task). Without this flag, the script would commit but never push, leaving the master Development repo silently behind on every daily run.

**Sister gotchas:**
- [[feedback_backup_pipeline_must_verify_push]] — silent push failure is the other failure mode that makes a backup script worse than no backup
- [[feedback_daily_backup_robustness_2026_05_04]] — earlier same-family fix on the user-level backup script (PowerShell Tee-Object + Python log() racing on the same file)

Together these mean: any automated commit + push script must (a) bypass hooks AND (b) verify push exit code with explicit alert log on failure AND (c) avoid pipe-style logging that interacts badly with git's redirected output.
