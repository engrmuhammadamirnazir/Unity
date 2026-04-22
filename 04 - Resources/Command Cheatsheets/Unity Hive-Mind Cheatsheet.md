---
type: cheatsheet
tags: [cheatsheet, unity, hive-mind, claude-code, graphify, mcp, reference]
aliases: [Hive-Mind Cheatsheet, Unity Cheatsheet, AI Workflow Cheatsheet]
created: 2026-04-22
updated: 2026-04-22
---

# Unity Hive-Mind Cheatsheet

> Single-page reference for the entire Unity + Claude Code + Graphify + Claude-Mem infrastructure. If you forget what's where or how to invoke something, start here.
>
> Companion notes: [[MOC - Hive Mind]] (project map) · [[Server & Key Inventory]] (credentials index) · [[Session Log]] (append-only journal) · `~/.claude/CLAUDE.md` (agent orientation)

---

## 1. Daily / per-session workflow

| Moment | What happens | How |
|--------|--------------|-----|
| **Session start** (any workspace) | Agent loads Unity context | `hive-mind-load` skill invoked automatically or say `load hive mind` |
| **During session — 3+ vault reads needed** | Query graph instead | `hive-mind-query` skill OR directly `graphify query "<question>" --graph "<path>" --budget 1500` |
| **Session end** — canonical changes | Agent summarises to Unity | `hive-mind-sync` skill invoked OR say `end session` / `wrap up` |
| **Session end** — automatic safety net | Silent Stop hook | `backup_unity.sh` auto-commits + pushes Unity (60s async, no user action) |
| **Daily 23:50** — scheduled safety net | Windows Task Scheduler runs backup | `Ecosire-Unity-Daily-Backup` scheduled task |

---

## 2. User-level skills (work in every project)

| Skill | Use for | Triggers |
|-------|---------|----------|
| `/graphify` | Build a knowledge graph of any folder | `/graphify .` or `/graphify <path>` |
| `hive-mind-load` | Load Unity cross-project context at session start | "start session" / "load hive mind" / "catch me up" |
| `hive-mind-query` | Query Unity graph instead of reading 3+ notes | "look up in unity" / "what does unity know about X" |
| `hive-mind-sync` | Write session summary + push Unity on session end | "end session" / "wrap up" / "save progress" |
| `unity-audit` | Monthly 14-point infrastructure health check | "audit unity" / "unity status" |
| `defuddle-ingest` | URL → clean markdown into target folder | "ingest <url>" / "pull docs from <url>" |

Skill files live at `~/.claude/skills/<name>/SKILL.md`.

---

## 3. Graphify commands

```bash
# First build (needs Claude Code session + /graphify skill)
/graphify .                                # inside the workspace, fresh session
/graphify <path>                           # build for a specific path

# Incremental refresh (no LLM, runs via post-commit hook automatically)
graphify update "<path>"                   # re-extract code files + update graph
graphify watch "<path>"                    # live refresh on file changes

# Querying an existing graph
graphify query "<question>" --graph "<path/graph.json>" --budget 1500
graphify query "<question>" --dfs          # depth-first for path-tracing
graphify explain "NodeLabel" --graph "<path/graph.json>"
graphify path "NodeA" "NodeB" --graph "<path/graph.json>"

# Ingest content into a folder then rebuild
graphify add <url> --author "Name" --dir "<folder>"

# Maintenance
graphify hook install                       # post-commit + post-checkout hooks
graphify hook status                        # check if hooks installed
graphify --mcp                              # start MCP stdio server for this graph
graphify benchmark graph.json               # measure token reduction vs raw reads
```

**Current graphs on this machine:**

| Workspace | Graph file | Status |
|-----------|-----------|--------|
| Unity | `C:/Users/Amir Nazir/Dropbox/Unity/graphify-out/graph.json` | ✓ Built 2026-04-22 (283 nodes, 513 edges, 14 communities) |
| D:/Development | `D:/Development/graphify-out/graph.json` | ⏳ Pending first build |
| D:/ECOSIRE.COM | `D:/ECOSIRE.COM/graphify-out/graph.json` | ⏳ Pending first build |
| D:/ECOSIRE.AI | `D:/ECOSIRE.AI/graphify-out/graph.json` | ⏳ Pending first build |
| D:/ECOSIRE.IO | `D:/ECOSIRE.IO/graphify-out/graph.json` | ⏳ Pending first build |

---

## 4. MCP server (graph queries as native tools)

Registered in `~/.claude/settings.json` with `"enableAllProjectMcpServers": true`.

`.mcp.json` at Unity root AND D:/Development root registers the `unity-hive-mind` stdio server. In a fresh Claude Code session inside those workspaces, the tools available are:

- `query_graph(question)` — BFS traversal with token budget
- `get_node(label)` — fetch node + 1-hop neighbours
- `get_neighbors(id)` — list adjacent nodes
- `shortest_path(nodeA, nodeB)` — path between concepts

To add MCP for another workspace (after building its graph):

```bash
cp "C:/Users/Amir Nazir/Dropbox/Unity/.mcp.json" "<target-workspace>/.mcp.json"
# then edit the hard-coded graph path inside the new .mcp.json
```

---

## 5. Daily backup (Unity → GitHub)

| What | Path |
|------|------|
| Bash script | `C:/Users/Amir Nazir/scripts/hive-mind/backup_unity.sh` |
| Batch wrapper | `C:/Users/Amir Nazir/scripts/hive-mind/backup_unity.bat` |
| Log directory | `C:/Users/Amir Nazir/scripts/hive-mind/logs/` (one file per day) |
| Scheduled task | `Ecosire-Unity-Daily-Backup` (daily 23:50 local) |
| GitHub destination | `github.com/engrmuhammadamirnazir/Unity` |

Run manually:

```bash
"C:/Users/Amir Nazir/scripts/hive-mind/backup_unity.sh"
```

Inspect scheduled task:

```powershell
Get-ScheduledTask -TaskName 'Ecosire-Unity-Daily-Backup' |
  Format-List State, @{N='NextRun';E={(Get-ScheduledTaskInfo $_).NextRunTime}}
```

---

## 6. Staleness check (all 5 graphs)

```bash
"C:/Users/Amir Nazir/scripts/hive-mind/check_graph_freshness.sh"        # text
"C:/Users/Amir Nazir/scripts/hive-mind/check_graph_freshness.sh" --json # machine-readable
"C:/Users/Amir Nazir/scripts/hive-mind/check_graph_freshness.sh" --quiet # only stale/missing
```

Exit 0 = all fresh · Exit 1 = one or more stale.
Integrated into `unity-audit` section 3.

---

## 7. Credentials & security

**Location:** `Unity/03 - Areas/Credentials & Access/` — **gitignored, never pushed to GitHub**. Values live only in Dropbox.

**Folders / files:**
- `Server & Key Inventory.md` — master index (server IPs, SSH users, key file paths)
- `SSH Keys/` — 11 keys backed up (ecosire.pem, sahara.pem, remittance.pem, hetzner_demo, diamond_group, alvi_aws.pem, ustelekomecosire.pem, ecosire_main + .pub, id_ed25519 + .pub)
- `Server Access Credentials.md` — server passwords (plain-text, sensitive)
- `Client Credentials.md` · `API Keys & Tokens.md` · `Financial & Banking.md` · `Personal Account Credentials.md` · `Recovery Codes.md`

**Retrieval pattern (never output values):**

```bash
# SSH via a Unity-backed key
ssh -i "C:/Users/Amir Nazir/Dropbox/Unity/03 - Areas/Credentials & Access/SSH Keys/<KEY>" <USER>@<IP>
```

**Credentials guard hook** (auto): `~/.claude/hooks/guard_credentials.sh` — blocks `Read` on the credentials folder and injects a warning, preventing prompt-injection-driven exfiltration. Wired in `~/.claude/settings.json` under `hooks.PreToolUse`.

---

## 8. Claude-mem (per-session automatic observation capture)

Background daemon that captures every tool call and compresses into semantic summaries.

```bash
# Start worker (Windows-specific path setup needed)
export PATH="/c/Users/Amir Nazir/.bun/bin:/c/Users/Amir Nazir/.local/bin:$PATH"
npx claude-mem start
npx claude-mem status                    # should say "Worker is running"

# Search past observations (MCP-enabled inside Claude Code)
/mem-search <query>                       # inside a Claude Code session
npx claude-mem search "<query>"           # from terminal

# Dashboard
# → http://localhost:37777
```

**If `npx claude-mem start` returns "Failed to start worker"**:

The plugin has a Windows cooldown state that sticks after a failed first attempt. Bypass:

```bash
rm -f "C:/Users/Amir Nazir/.claude-mem/worker.pid" "C:/Users/Amir Nazir/.claude-mem/supervisor.json"
export PATH="/c/Users/Amir Nazir/.bun/bin:/c/Users/Amir Nazir/.local/bin:$PATH"
cd "C:/Users/Amir Nazir/.claude/plugins/marketplaces/thedotmack/plugin/scripts"
bun worker-service.cjs &                  # detach
```

Log file: `C:/Users/Amir Nazir/.claude-mem/logs/claude-mem-YYYY-MM-DD.log`

---

## 9. Git hooks (graphify auto-rebuild)

Installed in every ECOSIRE git repo on 2026-04-22. On every `git commit` and `git checkout`, graphify does a deterministic AST-only refresh (no LLM, sub-second).

```bash
# Verify hooks present
for p in \
  "/c/Users/Amir Nazir/Dropbox/Unity" \
  "/d/Development" "/d/ECOSIRE.COM" "/d/ECOSIRE.AI" "/d/ECOSIRE.IO" \
  "/d/OpenClaw/openclaw"; do
  test -f "$p/.git/hooks/post-commit" && echo "HOOK $p" || echo "MISS $p"
done

# Install / reinstall in a new repo
( cd <repo-path> && graphify hook install )

# Remove (if ever noisy)
( cd <repo-path> && graphify hook uninstall )
```

---

## 10. Claude Code hooks (in `~/.claude/settings.json`)

| Event | Purpose | Script |
|-------|---------|--------|
| `PreToolUse` on `Read` | Credentials guard — blocks reads of Unity credentials folder unless explicitly legitimate | `~/.claude/hooks/guard_credentials.sh` |
| `Stop` (async, 60s) | Auto-commit + push Unity at session end | `C:/Users/Amir Nazir/scripts/hive-mind/backup_unity.sh` |

View them:

```bash
cat "C:/Users/Amir Nazir/.claude/settings.json"
```

---

## 11. Where everything lives

```
C:\Users\Amir Nazir\
├── .claude\                                   Claude Code user config
│   ├── CLAUDE.md                              User-level agent orientation
│   ├── settings.json                          Hooks + MCP gate + plugins
│   ├── hooks\                                 guard_credentials.sh
│   ├── skills\                                graphify, hive-mind-*, unity-audit, defuddle-ingest
│   └── plugins\marketplaces\thedotmack\       claude-mem plugin
├── .claude-mem\                               claude-mem runtime data
│   ├── claude-mem.db                          FTS5 + vector search
│   ├── logs\                                  Daily log files
│   └── worker.pid                             Current worker process
├── .bun\bin\                                  Bun runtime (needed by claude-mem)
├── .local\bin\                                uv runtime (needed by claude-mem)
├── Dropbox\Unity\                             THIS VAULT (git-tracked)
│   ├── CLAUDE.md                              Vault agent orientation
│   ├── AGENTS.md                              Cross-agent shim
│   ├── .mcp.json                              Unity-graph MCP server
│   ├── .graphifyignore                        Graph exclusions
│   ├── 00-09 folders                          PARA structure
│   ├── graphify-out\                          graph.json + graph.html + report (gitignored)
│   └── 02 - Projects\Hive Mind\               Session Log + Audit Reports
└── scripts\hive-mind\                         Backup + staleness scripts
    ├── backup_unity.sh + .bat
    ├── check_graph_freshness.sh
    └── logs\                                  Daily backup logs

D:\
├── Development\       CLAUDE.md, AGENTS.md, .mcp.json, .graphifyignore, .git/hooks/post-commit (graphify)
├── ECOSIRE.COM\       same four + .graphifyignore
├── ECOSIRE.AI\        same four
├── ECOSIRE.IO\        same four
├── Company\           CLAUDE.md + AGENTS.md (not a git repo)
├── Office\            CLAUDE.md + AGENTS.md
└── OpenClaw\openclaw\ CLAUDE.md + AGENTS.md + hooks
```

---

## 12. Common tasks (copy-paste)

**Audit Unity infrastructure (monthly):**
```
Invoke the `unity-audit` skill or say "audit unity" to run the 14-point check.
```

**Force a full Unity backup right now:**
```bash
"C:/Users/Amir Nazir/scripts/hive-mind/backup_unity.sh"
```

**Check which graphs are stale:**
```bash
"C:/Users/Amir Nazir/scripts/hive-mind/check_graph_freshness.sh"
```

**Build a missing graph (fresh Claude Code session in target workspace):**
```
/graphify .
```

**Query Unity graph from terminal (no Claude Code session needed):**
```bash
graphify query "Which servers use ecosire.pem?" --graph "C:/Users/Amir Nazir/Dropbox/Unity/graphify-out/graph.json" --budget 1500
```

**Explain a single concept:**
```bash
graphify explain "Sahara Properties" --graph "C:/Users/Amir Nazir/Dropbox/Unity/graphify-out/graph.json"
```

**Open the interactive graph viewer:**
```
Open C:/Users/Amir Nazir/Dropbox/Unity/graphify-out/graph.html in any browser.
```

**Restart claude-mem worker after a crash:**
```bash
rm -f "C:/Users/Amir Nazir/.claude-mem/worker.pid" "C:/Users/Amir Nazir/.claude-mem/supervisor.json"
export PATH="/c/Users/Amir Nazir/.bun/bin:/c/Users/Amir Nazir/.local/bin:$PATH"
cd "C:/Users/Amir Nazir/.claude/plugins/marketplaces/thedotmack/plugin/scripts"
bun worker-service.cjs &
```

**Ingest web docs into platform-api-intelligence:**
```
Ingest https://api.example.com/docs into platforms/example
```
(invokes `defuddle-ingest` skill)

---

## 13. Troubleshooting quick-reference

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| Agent in another workspace "doesn't remember" something | Session Log gap / `hive-mind-sync` not invoked at last session end | Stop hook should have at least pushed Unity; check `git log` on Unity. If entry truly missing, reconstruct from your memory or the transcript |
| `graphify query` returns "graph not found" | First build never ran for this workspace | Open a fresh Claude Code session in the workspace, run `/graphify .` |
| Stale / outdated graph results | Post-commit hook not firing or no commits recently | Run `graphify update "<path>"` manually; verify `.git/hooks/post-commit` exists |
| `hive-mind-audit` skill not found | Renamed to `unity-audit` 2026-04-22 | Use `unity-audit` instead (it was a rename) |
| MCP `query_graph` tool not visible in session | `.mcp.json` missing from workspace root or `enableAllProjectMcpServers: false` | Copy `.mcp.json` template from Unity root; confirm settings |
| Daily backup task shows `Disabled` | Manually disabled or Windows updated Task Scheduler | Re-register via Task Scheduler GUI or the PowerShell `Register-ScheduledTask` block in `scripts/hive-mind/` |
| Push failed with "rejected" | Remote changed (multi-device edit) | `git pull --rebase origin main` inside Unity, resolve, push again |
| Credentials guard hook blocked a legit read | Correct — flag, user confirms, proceed | Read the warning, confirm the task genuinely needs the credential, continue |
| claude-mem worker says "Failed to start" | Windows cooldown state after first failed attempt | See Section 8 "If ... returns Failed to start worker" |

---

## 14. One-line reminders (pin to brain)

- **Unity = single source of truth** for cross-project facts. Project-local memory = only codebase-internal patterns.
- **Never output credential values.** The guard hook helps but agents should still refuse.
- **Hive-mind-sync at end, hive-mind-load at start.** Not optional — it's the whole point.
- **Queries > reads.** Before reading 3+ Unity notes, try `graphify query` first (71.5× cheaper).
- **Post-commit hooks keep graphs fresh for free.** Just `git commit` normally.
- **23:50 nightly + Stop hook + manual `backup_unity.sh` = three layers of durability.** Unity can't vanish silently.

---

> Audit this document monthly via `unity-audit`. If any path / script / skill changes, update this file AND add a one-liner to the Session Log.
