---
name: Backup pipelines must EXPLICITLY verify push success — silent commit-without-push is a 25-day failure mode
description: A backup script that runs `git add/commit/push` and exits 0 if the push fails silently is worse than no backup. The commit succeeds locally, the script logs "successful," but origin diverges from local. We discovered ~/.claude had been failing this way for 25 days (104 MB JSONL blob > GitHub 100 MB limit). Rule&#58; backup scripts must (a) capture push exit code, (b) detect specific failure classes (>100 MB blob, auth, network), (c) exit non-zero so the scheduler / monitor knows, (d) log a remediation hint not just "FAILED."
type: feedback
updated: 2026-05-03
originSessionId: 2026_05_03_hive_mind_infrastructure_overhaul
---

## The rule

**Any script that pushes to a remote MUST verify the push succeeded and exit non-zero on failure.** "It committed locally" is not a backup. A backup is what's on the remote.

The pattern:

```bash
# WRONG — silent failure
git add -A
git commit -m "..."
git push origin main 2>&1   # exit code lost
echo "Backup successful"
exit 0

# WRONG TOO — exit code captured but failure not classified
git add -A
git commit -m "..."
if git push origin main 2>&1; then
    echo "OK"
else
    echo "FAILED"   # but: is it auth? blob size? network? — operator can't fix without diagnostic
fi

# RIGHT
git add -A
git commit -m "..."
PUSH_OUT=$(git push origin main 2>&1)
PUSH_RC=$?
if [ $PUSH_RC -ne 0 ]; then
    if echo "$PUSH_OUT" | grep -q "exceeds GitHub.*100"; then
        echo "ERROR: push blocked by file > 100 MB. Run filter-repo to clean. Exit 2."
        exit 2
    elif echo "$PUSH_OUT" | grep -q "Authentication failed"; then
        echo "ERROR: auth failed. Check token. Exit 3."
        exit 3
    fi
    echo "ERROR: push failed: $PUSH_OUT"
    exit 1
fi
```

## Why

The failure mode is invisible: commits keep accumulating locally, the log says "Backup successful," everything looks fine — but origin is hours, days, or weeks behind. If the local machine dies, you lose everything since the last successful push.

In our case: `~/.claude` accumulated 18 unpushed commits over 25 days because a single 104 MB `.jsonl` blob from a long Suleman Remittance session exceeded GitHub's 100 MB hard limit. The old `backup-daily.sh` did:

```bash
if git push origin main --quiet 2>&1; then
    echo "Backup successful" >> "$LOG_FILE"
else
    echo "Backup push FAILED" >> "$LOG_FILE"
    exit 1
fi
```

The exit 1 was correct, BUT:
1. Windows Scheduled Task didn't surface "Last run failed" prominently
2. No alarm / desktop notification on failure
3. No remediation hint ("HINT: blob > 100 MB; run filter-repo")
4. Scheduler kept running daily, accumulating more failed runs

## How to apply

For backup scripts:
1. **Capture push exit code AND output.** `rc=$?` and `out=$(...)` separately so you can grep the message.
2. **Classify common failure modes** — output an actionable hint, not just "FAILED":
   - `>100 MB blob` → "run filter-repo"
   - `Authentication failed` → "check $GITHUB_TOKEN / SSH key"
   - `Could not resolve host` → "network down, will retry tomorrow"
3. **Exit code per class** so the scheduler can branch:
   - 0 = success
   - 1 = transient (network, retry)
   - 2 = needs human intervention (size, auth, history rewrite)
4. **Log the actual error message**, not just "FAILED." Future-you reading the log a week later needs the diagnostic.
5. **For Windows Scheduled Tasks specifically:** the action's exit code is recorded in Task Scheduler history. Set the task to email or notify on failure if backup is critical.
6. **Verify after a successful push** — `git rev-parse origin/main` should equal `git rev-parse main`. If not, something raced.

## Real incident — 2026-04-08 → 2026-05-03

`~/.claude` daily backup task. From 2026-04-08 onward, every day's log read "Backup push FAILED." Exit code 1 each time. Task continued daily. **25 days passed before discovery.** Discovered only when investigating a separate "skill might have been lost" concern.

Fix in this session: rewrote backup script (now `backup_all_workspaces.py`):
- Captures `proc.returncode` and `proc.stdout + proc.stderr`
- Greps stderr for `"exceeds GitHub"` / `"100.00 MB"` / `"100 MB"` to detect blob-size failures specifically; emits hint and exits 2
- Logs first 500 chars of error verbatim, not just "FAILED"
- Returns separate phase-3 (Unity) push only if phase-2 (claude repo) push succeeded — no cascade of false reports

## Detection signals

- "Successful" backup logs but `git status` shows N commits ahead of origin
- GitHub repo's "last pushed" timestamp drifts behind expectation
- Local repo grows steadily but remote size stays flat
- `git fetch && git status` shows divergence

## Reusable across

- Any cron-driven git backup
- CI/CD release-tag pushes (where the push step's failure must halt the pipeline)
- Multi-tenant backup scripts pushing N repos sequentially (failure of one shouldn't silently skip the rest, but each should report individually)
- pg_dump | s3 cp pipelines (verify the upload, not just the dump)
- rsync to remote (use `--checksum --verify` and check exit code)

## Related memories

- [feedback_no_op_placeholder_in_signoff_method.md](feedback_no_op_placeholder_in_signoff_method.md) — same family: success-logged-but-actually-noop
- [feedback_module_pump_static_validation_insufficient.md](feedback_module_pump_static_validation_insufficient.md) — static checks lie about runtime correctness
- [feedback_propagate_learning_not_artifacts.md](feedback_propagate_learning_not_artifacts.md) — same session: another structural rule
