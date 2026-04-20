---
type: cheatsheet
tags: [commands, claude, ai, cli, cheatsheet]
created: 2026-03-02
updated: 2026-04-19
---

# Claude Code Commands

> Comprehensive reference for Claude Code CLI, current as of April 2026.
> Covers CLI flags, slash commands, keyboard shortcuts, skills, subagents, hooks, plugins, and MCP.

---

## Quick Start

```bash
npm install -g @anthropic-ai/claude-code   # Install globally
claude                                      # Start interactive session
claude --version                            # Check version
claude update                               # Update to latest
claude doctor                               # Diagnose installation
claude migrate-installer                    # Move from global npm to local
```

One-shot / piped usage:

```bash
claude "explain this code"                  # One-shot prompt
claude -p "what does this do"               # Print mode (non-interactive)
cat error.log | claude -p "explain errors"  # Pipe input
git diff | claude -p "write commit msg"     # Chain with other tools
claude -p "generate README" > README.md     # Output to file
```

Session control:

```bash
claude -c                                   # Continue last conversation
claude --resume                             # Pick session to resume
claude -r <session-id>                      # Resume specific session
claude --fork-session                       # Branch off current session
claude --from-pr <num>                      # Resume from GitHub PR context
```

---

## Authentication

```bash
claude auth login             # Sign in (interactive)
claude auth login --sso       # SSO login
claude auth login --console   # Console.anthropic.com API billing
claude auth logout            # Sign out
claude auth status            # Check status (JSON default, --text for human)
claude setup-token            # Generate long-lived token for CI/scripts
```

---

## CLI Flags (Reference)

### Session & Resume

| Flag | Purpose |
|------|---------|
| `-c`, `--continue` | Continue the most recent session |
| `-r`, `--resume [id]` | Resume specific session by ID |
| `--fork-session` | Branch off current session |
| `--from-pr <num>` | Start with context from GitHub PR |
| `--session-id <uuid>` | Attach custom session ID |
| `-n`, `--name <label>` | Label the session |

### Model & Effort

| Flag | Purpose |
|------|---------|
| `--model <name>` | `opus`, `sonnet`, `haiku`, or full ID |
| `--fallback-model <name>` | Fallback if primary unavailable |
| `--effort low\|medium\|high\|xhigh\|max` | Thinking budget |

### Permissions

| Flag | Purpose |
|------|---------|
| `--permission-mode <mode>` | `default`, `acceptEdits`, `plan`, `auto`, `dontAsk`, `bypassPermissions` |
| `--dangerously-skip-permissions` | Skip all permission prompts |
| `--allow-dangerously-skip-permissions` | Permit the above flag in config |

### Tools

| Flag | Purpose |
|------|---------|
| `--allowedTools "pattern"` | Pre-approve tools |
| `--disallowedTools "pattern"` | Block specific tools |
| `--tools "Bash,Edit,Read"` | Restrict to listed tools only |

### System Prompts

| Flag | Purpose |
|------|---------|
| `--system-prompt "..."` | Replace system prompt |
| `--system-prompt-file <path>` | Load system prompt from file |
| `--append-system-prompt "..."` | Add to default system prompt |
| `--append-system-prompt-file <path>` | Append from file |

### Input / Output

| Flag | Purpose |
|------|---------|
| `-p`, `--print` | Print mode (non-interactive) |
| `--output-format text\|json\|stream-json` | Output format |
| `--input-format text\|stream-json` | Input format |
| `--json-schema <file>` | Validate final output against schema |
| `--max-turns N` | Cap agentic turns |
| `--max-budget-usd <n>` | Cap spend per session |
| `--verbose` | Verbose logs |
| `--debug [category]` | Debug logs (e.g. `--debug hooks`) |

### Files, Plugins, MCP

| Flag | Purpose |
|------|---------|
| `--add-dir <path>` | Grant file access + auto-load `.claude/skills` |
| `--plugin-dir <path>` | Load a local plugin directory |
| `--mcp-config <file>` | Extra MCP config |
| `--strict-mcp-config` | Only use specified MCP config |
| `--settings <file>` | Use alternate settings file |
| `--exclude-dynamic-system-prompt-sections` | Improve cache reuse for multi-user |

### Agents & Remote

| Flag | Purpose |
|------|---------|
| `--agent <name>` | Main thread agent |
| `--agents '{"name":...}'` | Inline agent definitions |
| `--teammate-mode auto\|in-process\|tmux` | How teammates run |
| `--remote "task"` | Run remotely |
| `-rc`, `--remote-control "name"` | Remote control mode |
| `--teleport` | Resume remote session locally |
| `--chrome` / `--no-chrome` | Force browser tools on/off |

---

## Subcommands

| Command | Purpose |
|---------|---------|
| `claude config` | Open tabbed settings UI |
| `claude agents` | List configured subagents |
| `claude mcp add <server>` | Add MCP server (stdio, sse, http) |
| `claude mcp list` | List configured MCP servers |
| `claude mcp remove <server>` | Remove MCP server |
| `claude plugin install <name>` | Install plugin from marketplace |
| `claude plugin list` | List installed plugins |
| `claude plugin enable/disable <name>` | Toggle plugin |
| `claude plugin uninstall <name>` | Remove plugin |
| `claude auto-mode defaults` | Print auto-mode classifier rules |
| `claude auto-mode config` | Show effective auto-mode config |
| `claude remote-control` | Start server for web control |
| `claude setup-token` | Long-lived OAuth token for CI |
| `claude doctor` | Diagnose install problems |
| `claude update` | Update to latest version |

---

## Slash Commands (In-Session)

### Session & Context
| Command | Action |
|---------|--------|
| `/help` | Show help |
| `/status` | Session info (model, cost, perms) |
| `/resume` | Resume a previous session |
| `/rename` | Rename current session |
| `/clear` | Clear conversation |
| `/compact` | Summarize and compact history |
| `/context` | Show token context breakdown |
| `/cost` | Token usage + dollar cost |
| `/recap` | Summary of the session so far |
| `/export` | Save transcript |

### Model & Behavior
| Command | Action |
|---------|--------|
| `/model` | Switch model mid-session |
| `/effort` | Change thinking effort |
| `/output-style` | Theme, syntax, rendering |
| `/btw` | Side question — no history impact |

### Configuration & Health
| Command | Action |
|---------|--------|
| `/config` | Open settings UI |
| `/doctor` | Check installation health |
| `/init` | Initialize `CLAUDE.md` for project |
| `/login`, `/logout` | Account management |
| `/upgrade` | Upgrade Pro/Max plan |
| `/terminal-setup` | Configure terminal features |
| `/ide` | Connect to IDE |
| `/vim` | Toggle vim keybindings |
| `/bug` | File a bug report |

### Permissions & Memory
| Command | Action |
|---------|--------|
| `/permissions` | View/edit permission rules |
| `/memory` | Manage `CLAUDE.md` memory |
| `/add-dir <path>` | Add directory to allowed paths |

### Agents, Hooks, Plugins, MCP
| Command | Action |
|---------|--------|
| `/agents` | List / manage subagents |
| `/teams` | Agent team status |
| `/hooks` | View configured hooks (read-only) |
| `/plugin` | Plugin manager UI |
| `/reload-plugins` | Reload plugins without restart |
| `/mcp` | List MCP servers + test tools |
| `/todos` | Active task list |

### Git & Review
| Command | Action |
|---------|--------|
| `/review` | Code review on current changes |
| `/security-review` | Security audit of pending changes |
| `/pr-comments` | Fetch GitHub PR comments |
| `/release-notes` | Generate release notes |

> **Note:** Skills in `.claude/skills/` auto-register as slash commands — `/my-skill` runs `.claude/skills/my-skill/SKILL.md`.

---

## Keyboard Shortcuts

### Permission & Flow
| Shortcut | Action |
|----------|--------|
| `Shift+Tab` | Cycle permission modes (default → acceptEdits → plan → auto → bypassPermissions) |
| `Esc` `Esc` | Rewind to previous turn |
| `Ctrl+C` | Cancel current input / generation |
| `Ctrl+D` | Exit session |
| `Ctrl+O` | Toggle transcript viewer (tool-use detail) |
| `Ctrl+T` | Toggle task list |

### Input & Editing
| Shortcut | Action |
|----------|--------|
| `!` (start) | Bash mode — run command and include output |
| `@` | File path autocomplete |
| `/` (start) | Command / skill picker |
| `#` (start) | Save line to `CLAUDE.md` memory |
| `Shift+Enter` | Multiline (iTerm2, WezTerm, Ghostty, Kitty) |
| `\` + `Enter` | Multiline (all terminals) |
| `Ctrl+L` | Clear prompt (keep history) |
| `Ctrl+R` | Reverse-search history |
| `Ctrl+G` / `Ctrl+X Ctrl+E` | Open prompt in `$EDITOR` |
| `Ctrl+V` | Paste image from clipboard |
| `Ctrl+K` / `Ctrl+U` / `Ctrl+W` | Delete to end / to start / previous word |
| `Ctrl+Y` / `Alt+Y` | Paste deleted / cycle paste history |
| `Alt+B` / `Alt+F` | Cursor back / forward one word |

### Model & Mode Toggles
| Shortcut | Action |
|----------|--------|
| `Option+P` / `Alt+P` | Switch model (keep prompt) |
| `Option+T` / `Alt+T` | Toggle extended thinking |
| `Option+O` / `Alt+O` | Toggle fast mode |
| `Ctrl+B` | Background bash command (press twice for tmux) |
| `Ctrl+X Ctrl+K` | Kill all background agents (twice to confirm) |

---

## Permission Modes

| Mode | Behavior |
|------|---------|
| `default` | Ask per action |
| `acceptEdits` | Auto-accept file edits, ask for others |
| `plan` | Plan mode — propose only, no edits |
| `auto` | Learned classifier decides |
| `dontAsk` | Never prompt (uses allow/deny rules) |
| `bypassPermissions` | No gating (dangerous) |

**Plan mode** (`Shift+Tab` until "plan"): Claude writes a plan as code blocks; you review and approve before any edits happen. Ideal for complex refactors or unfamiliar codebases.

---

## Models

| Model | Use Case | Speed | Cost |
|-------|----------|-------|------|
| `opus` (Opus 4.7) | Complex reasoning, architecture | Slowest | Highest |
| `sonnet` (Sonnet 4.6) | Standard dev work | Medium | Medium |
| `haiku` (Haiku 4.5) | Fast/simple tasks, validators | Fastest | Lowest |
| `inherit` | Match caller's model | — | — |

**Fast mode:** toggle with `/fast` or `Alt+O` — uses Opus 4.6 at faster output speed.

---

## `CLAUDE.md` (Project Memory)

Project root file auto-loaded at session start. Nested `CLAUDE.md` in subfolders are loaded when working in that scope.

```markdown
# Project Instructions

## Build & Test
- Run tests: `npm test`
- Build: `npm run build`

## Code Style
- TypeScript strict mode
- Functional components only

## Security
- Never commit .env files
- Never modify database migrations directly
```

Use `#` prefix in chat to append a line: `# Always run lint before commit`.

---

## Skills

Reusable prompt workflows in `.claude/skills/<name>/SKILL.md`.

### Directory Layout
```
.claude/skills/
  my-skill/
    SKILL.md           # Skill definition (required)
    references/        # Optional supporting docs
```

### Frontmatter
```yaml
---
name: my-skill
description: "What this skill does and when to use it"
argument-hint: "[target] [options]"
disable-model-invocation: true    # User must invoke explicitly
user-invocable: false             # Hidden from menu; Claude only
allowed-tools: [Read, Grep, Edit]
context: fork                     # Run in background subagent
agent: Explore                    # Route to specific agent
model: sonnet
effort: medium
paths: ["src/**/*.ts"]            # Glob filter — only activate in these paths
---

Instructions for the skill. Use $ARGUMENTS, $0, $1 for arguments.

!`echo "shell runs before content is sent"`
```

### Invoking
```
/my-skill                    # Run the skill
/my-skill arg1 arg2          # $ARGUMENTS = "arg1 arg2"
```

### Common Patterns
| Pattern | Use Case |
|---------|----------|
| `context: fork` | Heavy task → isolated subagent |
| `agent: Explore` | Read-only codebase exploration |
| `disable-model-invocation: true` | User-only actions |
| `paths: [...]` | Scope skill to specific files |

---

## Subagents

Defined in `.claude/agents/<name>.md`. Appear in `/agents` list.

### Frontmatter
```yaml
---
name: my-agent
description: "Use when... Proactively use after..."
tools: Read, Write, Edit, Glob, Grep, Bash
disallowedTools: WebSearch
model: opus                # opus | sonnet | haiku | inherit
permissionMode: default    # default | acceptEdits | plan | bypassPermissions
maxTurns: 50
memory: project            # user | project | local
background: false
isolation: none            # none | worktree (for parallel edits)
color: blue
skills: [skill1, skill2]
mcpServers: [server1]
hooks:
  SubagentStop:
    - type: command
      command: "echo done"
---

System prompt / instructions (markdown).
```

### Memory Scopes
| Scope | Location | Persistence |
|-------|----------|-------------|
| `user` | `~/.claude/agent-memory/<name>/` | Across all projects |
| `project` | `.claude/agent-memory/<name>/` | This project only |
| `local` | `.claude/local/agent-memory/<name>/` | Local, gitignored |

Agent reads first 200 lines of its `MEMORY.md` at startup.

### Built-in Agent Types
- `Explore` — read-only codebase research
- `Plan` — architectural planning
- `general-purpose` — multi-step tasks

---

## Hooks

Event handlers at lifecycle points. Defined in `settings.json`, plugin `hooks/hooks.json`, or agent/skill frontmatter.

### Events
| Event | Fires | Use Case |
|-------|-------|---------|
| `SessionStart` | Session begins | Load env, init state |
| `SessionEnd` | Session exits | Cleanup |
| `UserPromptSubmit` | Before prompt processed | Validate input |
| `PreToolUse` | Before tool runs | Block, validate |
| `PostToolUse` | After tool succeeds | Format, lint |
| `Stop` | Claude finishes response | Checkpoint |
| `StopFailure` | Response errored | Fallback |
| `PermissionRequest` | Permission prompt shown | Auto-decide |
| `PermissionDenied` | User denied perm | Log |
| `PreCompact` / `PostCompact` | Around summarization | Archive state |
| `FileChanged` | File edited | Watch, sync |
| `CwdChanged` | Working dir changed | Update context |
| `ConfigChange` | Settings changed | Reload |
| `Notification` | MCP/plugin notification | Act on it |
| `SubagentStart` / `SubagentStop` | Subagent lifecycle | Track, integrate |

### Handler Types
```yaml
hooks:
  PreToolUse:
    - type: command                    # Bash (JSON stdin → exit code/stdout)
      command: "python check.py"
      matcher: "Write"                 # Only fires for Write tool
  PostToolUse:
    - type: prompt                     # Single-turn LLM eval (yes/no)
      prompt: "Did this edit introduce bugs?"
  Stop:
    - type: agent                      # Spawn subagent
      agent: validator
  Notification:
    - type: http                       # HTTP POST
      url: "https://example.com/webhook"
```

### Exit Codes (command hooks)
| Code | Effect |
|------|--------|
| 0 | Continue |
| 2 | Block action (PreToolUse only) |
| Other | Warn, but continue |

---

## Plugins

Distributed packages of skills + agents + hooks + MCP servers.

### Directory Layout
```
my-plugin/
├── .claude-plugin/plugin.json    # Manifest
├── skills/                       # SKILL.md folders
├── agents/                       # Agent definitions
├── hooks/hooks.json              # Hook configs
├── .mcp.json                     # MCP servers
├── .lsp.json                     # LSP servers
├── monitors/monitors.json        # Background watchers
└── settings.json                 # Default config
```

### Manifest Fields
- `name` — unique ID + skill namespace
- `description`, `version`, `author`

### Workflow
```bash
claude plugin install namespace@marketplace   # From marketplace
claude --plugin-dir ./my-plugin               # Local testing
# In session:
/plugin                                        # Plugin manager UI
/reload-plugins                                # Reload without restart
```

Installed skills become `/namespace:skillname`.

---

## MCP (Model Context Protocol)

### Add Servers
```bash
claude mcp add npx some-package
claude mcp add sse https://example.com/sse
claude mcp add stdio /path/to/binary arg1 arg2
claude mcp list
claude mcp remove <server>
```

### Config File (`.mcp.json`)
```json
{
  "mcpServers": {
    "example": {
      "command": "npx",
      "args": ["example-mcp"]
    }
  }
}
```

### In Session
- Tools appear as `mcp__server__tool`
- `/mcp` lists servers and tests tools
- Hooks can match by pattern: `mcp__memory__.*`

---

## `settings.json` Schema

**Scope order** (highest wins): Managed → User (`~/.claude/settings.json`) → Project (`.claude/settings.json`) → Local (`.claude/settings.local.json`).

### Top-Level Keys
| Key | Purpose |
|-----|---------|
| `model` | Default model (`sonnet`, `opus`, `haiku`, full ID) |
| `effort` | Default thinking effort |
| `defaultMode` | Default permission mode |
| `agent` | Default agent on main thread |
| `permissions` | `allow` / `deny` rule arrays |
| `hooks` | Hook handler map |
| `env` | Environment variables |
| `enabledPlugins` | Plugin enable/disable map |
| `extraKnownMarketplaces` | Custom plugin marketplaces |
| `disableAllHooks` | Kill-switch for hooks |
| `disableSkillShellExecution` | Block `` !`cmd` `` in skills |
| `theme` | `dark` / `light` / `auto` |
| `statusLine` | Custom status line (npm pkg or command) |
| `voiceEnabled` | Voice input on/off |
| `skipDangerousModePermissionPrompt` | Skip bypass warning |

### Example
```json
{
  "model": "sonnet",
  "defaultMode": "acceptEdits",
  "permissions": {
    "allow": ["Bash(npm *)", "Write(src/**)", "Edit(*)"],
    "deny": ["Bash(rm -rf *)"]
  },
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "hooks": {
    "PostToolUse": [
      { "matcher": "Edit|Write", "type": "command", "command": "npm run lint" }
    ]
  }
}
```

---

## Agent Teams (Experimental)

Enable in `.claude/settings.local.json`:
```json
{ "env": { "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1" } }
```

Multiple Claude Code instances work in parallel with a shared task list. Ask inside a session: *"Create a team of 3 agents: one to build the module, one to write tests, one to validate."*

| Feature | How |
|---------|-----|
| Task creation | Main agent creates and assigns tasks |
| Parallel execution | Teammates work independently in background |
| Communication | Shared task list + inter-agent messages |
| Completion | `TeammateIdle` hook fires on teammate finish |

---

## Common Workflows

| Need | Command |
|------|---------|
| Continue last session | `claude -c` |
| Plan before executing | `claude --permission-mode plan` |
| Skip permissions (testing) | `claude --dangerously-skip-permissions -p "..."` |
| Custom system rules | `claude --append-system-prompt "Always use TypeScript"` |
| Parallel feature branches | Use git worktrees + `--plugin-dir` per worktree |
| Run non-interactive | `claude -p "..." --max-turns 3` |
| Switch model mid-session | `Option+P` or `/model` |
| Debug hooks | `--debug hooks` or `/hooks` |
| Fork a session | `claude --fork-session` |
| Resume from PR | `claude --from-pr 123` |

---

## Ecosire Agent Teams (Project-Specific)

### Using Odoo Agents on Customer Projects

Customer Odoo projects can reuse the Odoo agent team by launching `claude` from `D:/Development`:

```bash
cd D:/Development
claude
# Then: "Work on Sahara at D:/Ecosire Customer Service/Active Clients/Sahara"
```

Alternative options:
- **Copy agents:** `cp -r "D:/Development/.claude/agents" "<customer>/.claude/agents"`
- **Symlink (Windows):** `mklink /D "<customer>\.claude\agents" "D:\Development\.claude\agents"`
- **Generate custom team:** `/setup-agent-teams <path>`

### Team 1: Odoo Modules (`D:/Development`)

**Agents (6):**
| Agent | Model | Purpose |
|-------|-------|---------|
| `module-developer` | Opus | Creates new Odoo modules from scratch |
| `backporter` | Sonnet | Converts v19 modules to v18/v17 |
| `module-validator` | Sonnet | Validates Python, XML, CSV, manifests |
| `test-runner` | Haiku | Runs install + test suites across versions |
| `publisher` | Sonnet | Pushes to GitHub, syncs branches |
| `opportunity-scout` | Sonnet | Market research, competitor analysis |

**Skills (17):**
| Command | Purpose |
|---------|---------|
| `/new-module [platform]` | Create new module from template |
| `/backport-module [name]` | Backport to v18 + v17 |
| `/validate-module [name]` | Validate across all versions |
| `/test-module [name] [ver]` | Install + unit tests |
| `/publish-module [name]` | Push to GitHub (all branches) |
| `/full-pipeline [platform]` | Create → publish lifecycle |
| `/find-opportunities` | Research market gaps |
| `/update-module [name]` | Update existing module |
| `/upgrade-dashboard [name]` | Upgrade OWL dashboard |
| `/generate-html [name]` | Generate App Store HTML |
| `/forwardport-module [name]` | Port to newer Odoo version |
| `/setup-agent-teams [path]` | Generate agents for any project |
| `/submit-appstore [name]` | App Store submission checklist |
| `/regression-test [name]` | Cross-version regression testing |
| `/dependency-graph [name]` | Analyze module dependencies |
| `/generate-changelog [name]` | Generate changelog from git |
| `/module-health [name\|"all"]` | Validation status report |

### Team 2: ECOSIRE.COM Website (`D:/ECOSIRE.COM`)

**Agents (5):**
| Agent | Model | Purpose |
|-------|-------|---------|
| `fullstack-dev` | Opus | NestJS API + Next.js frontend |
| `code-reviewer` | Sonnet | Security/architecture audits (read-only) |
| `devops` | Sonnet | AWS EC2, PM2, Nginx, Docker |
| `i18n-specialist` | Sonnet | 11-locale translation management |
| `test-engineer` | Sonnet | Playwright E2E testing |

**Skills (7):**
| Command | Purpose |
|---------|---------|
| `/deploy [app]` | Deploy to production with pre-flight checks |
| `/test-e2e [path]` | Playwright E2E tests |
| `/new-feature [desc]` | Full dev lifecycle |
| `/fix-bug [desc]` | Diagnose + fix with RCA |
| `/translate [page]` | Update translations across 11 locales |
| `/seo-audit [page]` | SEO/AEO/GEO compliance audit |
| `/db-migrate [desc]` | Drizzle database migrations |

### Team 3: AI Content Engine (`D:/Ecosire Customer Service/.../Ai Content Automation`)

**Agents (4):**
| Agent | Model | Purpose |
|-------|-------|---------|
| `backend-dev` | Opus | FastAPI, Celery, SQLAlchemy, agent runtime |
| `frontend-dev` | Sonnet | Next.js dashboard, WordPress plugin |
| `qa-engineer` | Haiku | 1,519 pytest tests |
| `devops-ace` | Sonnet | Docker (11 containers), AWS |

**Skills (4):**
| Command | Purpose |
|---------|---------|
| `/deploy-ace [service]` | Deploy with Docker + migrations |
| `/run-tests [target]` | Run pytest suite (1,519 tests) |
| `/new-agent [name]` | Create new AMGI agent |
| `/scan-site [url]` | Run site scanner for SEO analysis |

### Shared Protocols
- **PMP-grade workflow:** Planning (scope, impact, risk) → Execution (read-first, incremental) → Verification (build, test)
- **Self-evolving memory:** Read before work, write after work
- **Daily GitHub backup:** `github.com/engrmuhammadamirnazir/claude.git`

---

## What's New in 2025/2026

| Feature | What It Does |
|---------|-------------|
| **Skills** | Markdown prompt extensions, replace ad-hoc commands |
| **Subagents** | Delegate to specialized agents via `/agents` |
| **Plugins** | Versioned packages for skills + agents + hooks + MCP |
| **Plan Mode** | `Shift+Tab` — review before execute |
| **Auto Mode** | `--permission-mode auto` with learned classifier |
| **Extended Thinking** | `Alt+T` toggle or "ultrathink" keyword |
| **Structured Outputs** | `--json-schema` validates final output |
| **Output Styles** | `/output-style` for theme/syntax/rendering |
| **Transcript Viewer** | `Ctrl+O` detailed tool-use view |
| **Task Lists** | `Ctrl+T` for tracked progress |
| **MCP Channels** | `--channels` for MCP notifications |
| **Session Recap** | Auto-summary when returning from away |
| **Vim Mode** | Full vim keybindings via `/vim` |

---

*See also: [[Git Commands]] | [[GitHub CLI Commands]] | [[Claude Code Unified Memory]]*
