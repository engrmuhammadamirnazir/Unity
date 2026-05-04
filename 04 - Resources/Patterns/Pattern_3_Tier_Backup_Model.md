---
name: 3-Tier Backup Model (workspace → master GitHub repo → Hetzner Storage Box)
description: For any workspace where loss would be catastrophic, run three concentric backup tiers that fail independently. Tier 1 = local git committed daily to a private master GitHub repo. Tier 2 = per-artifact remote (e.g. per-module repo). Tier 3 = off-platform SFTP backup (Hetzner Storage Box). All three wired into a single composite scheduled task.
type: pattern
tags: [pattern, fleet-wide, disaster-recovery, infrastructure]
source-workspace: D:/Development
captured: 2026-05-04
---

## The principle

Three concentric defenses that fail independently. A corrupted local repo still has GitHub; a deleted GitHub repo still has Hetzner; a Hetzner outage still leaves local + GitHub. Each tier protects against a different failure mode.

## The three tiers

**Tier 1 — Master private GitHub repo for the WHOLE workspace.**
- Purpose: protect against local working-tree wipe (the May 2 2026 disaster pattern)
- Implementation: `git init` the workspace root, set remote to `engrmuhammadamirnazir/<Workspace>`, `.gitignore` excludes upstream sources / Python interpreters / logs / pycache / nested-git-repo subfolders (those have their own remotes) / large binary blobs / DB dumps
- Daily auto-commit + push via a single bash script (e.g. `scripts/backup/daily_dev_commit.sh`)
- MUST bypass post-commit hooks via `git -c core.hooksPath=/dev/null commit` — see [[feedback_graphify_post_commit_hook_hangs]]
- MUST run a damage detector first (e.g. [[feedback_hollow_folder_wipe_fingerprint]]) and refuse to commit a damaged tree

**Tier 2 — Per-artifact remotes (existing, leverage what's there).**
- Purpose: granular protection + already exists for publishing flows
- For D:/Development: 209 per-module GitHub repos at `github.com/ecosire/<name>` with `17.0`/`18.0`/`19.0`/`main` branches
- For other workspaces: each app/package's own remote, each client's own private repo, etc.

**Tier 3 — Off-platform SFTP backup (Hetzner Storage Box BX11).**
- Purpose: protect against GitHub account compromise / loss / takedown
- BX11 ~3.81 EUR/mo, 1 TB, SSH/SFTP/SMB/BorgBackup compatible
- Daily zstd-compressed tarball (workspace minus `.gitignore`'d paths) uploaded via `sftp -i <key> -P 23 -b - <user>@<host>`
- 14-day rolling retention on Storage Box, 14-day local retention in `_backups/storagebox/`
- Until Storage Box ordered + env vars set, runs in local-only mode (still produces a third local copy independent of git)

## Composite scheduled task

All three tiers wired into a SINGLE Windows scheduled task (e.g. existing `Claude Code Daily Backup` 23:50 nightly). The PowerShell entry point chains:
1. Workspace mirror + lessons aggregator (existing for `~/.claude/`)
2. Daily Dev commit + push (Tier 1)
3. Hetzner Storage Box snapshot (Tier 3)

Composite exit non-zero on ANY-step failure. The existing notification path surfaces problems. Each step's exit code captured separately so failures are diagnosable.

## Detection layer

Pre-flight detection script (e.g. hollow-folder check) runs as the first thing the daily commit script does. If detection fails, the script writes ALERT to a separate alerts log AND exits 1 — the composite task surfaces it the same night, instead of a silent multi-day drift.

## Why this works

- **Independence:** local git, per-artifact remotes, and SFTP backup are at three different failure boundaries. Compromising one doesn't compromise the others.
- **Restorable:** each tier is independently restorable. From Tier 1: `git clone`. From Tier 2: per-module clone (the publishing flow already does this). From Tier 3: SFTP-pull the latest tarball + extract.
- **Detectable:** the pre-flight check + non-zero composite exit means problems can't hide for weeks.
- **Cheap:** Tier 1 is free (private GitHub repo), Tier 2 already exists, Tier 3 is ~3.81 EUR/mo for 1 TB.

## Already applied

- D:/Development: full 3-tier deployed 2026-05-04 after the May 2 wipe. Master repo `engrmuhammadamirnazir/Development` (private), composite task in `~/.claude/scripts/backup-all-workspaces.ps1`. Tier 3 env vars pending Hetzner Storage Box order.

## Could be applied to (other workspaces)

- D:/ECOSIRE.COM, D:/ECOSIRE.AI, D:/ECOSIRE.IO — each could have its own master repo + Tier-3 entry in the composite task
- D:/Company — already has Tier-1 git init (commits `345ce0c`, `893bff8`); Tier-2 GitHub remote pending user approval; Tier-3 could pipe through the same Hetzner Storage Box

## Sister patterns

- [[feedback_backup_pipeline_must_verify_push]] — every backup must verify the push succeeded, never silently swallow failures
- [[feedback_daily_backup_robustness_2026_05_04]] — three rules every backup-script edit must respect (size cap < 50MB, no double-write log, don't descend into nested git repos)
- [[feedback_hollow_folder_wipe_fingerprint]] — the detection rule that gates the daily commit
- [[feedback_graphify_post_commit_hook_hangs]] — a hook that must be bypassed for the daily commit to actually push
