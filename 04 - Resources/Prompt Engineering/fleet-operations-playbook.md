# FLEET OPERATIONS PLAYBOOK
## How to Run, Verify, and Prompt Your AI Agent Fleet in Claude Code

> Your fleet is built. This playbook teaches you how to drive it.

---

## TABLE OF CONTENTS

1. Fleet Verification — Making Sure Everything Works
2. Three Ways to Invoke Your Fleet
3. Prompt Templates — Copy-Paste for Every Scenario
4. Daily Workflows — Your New Development Routine
5. Monitoring & Quality Assurance
6. Troubleshooting Common Issues
7. Cost Management

---

## 1. FLEET VERIFICATION — FIRST RUN VALIDATION

After the mega-prompt finishes building everything, run these checks **before trusting the fleet with real work.**

### 1.1 Structural Verification

Open Claude Code in your project root and run:

```
Verify the agent fleet installation:

1. List all files in .claude/agents/ — confirm all 17 agent .md files exist
2. List all directories in .claude/skills/ — confirm all skill folders have SKILL.md
3. Check .claude/agent-memory/ — confirm shared-knowledge/ and per-agent memory dirs exist
4. Read .claude/settings.json — confirm hooks and CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
5. Read the root CLAUDE.md — confirm it lists all agents and skills
6. Show me a summary table: agent name | skills count | memory scope | tools
```

### 1.2 Individual Agent Smoke Tests

Test EACH agent with a trivial task to verify it activates, loads its skills, and writes to memory:

```
# Test auto-delegation (just describe a task — Claude should pick the right agent)
"Review the security of our authentication middleware"
→ Should auto-delegate to @security-auditor

# Test explicit invocation
"Use the frontend-engineer agent to analyze our component folder structure"
→ Should spawn @frontend-engineer with nextjs-patterns skill preloaded

# Test a skill directly
/pr-review
→ Should activate the pr-review skill and start a review workflow

# Test memory
"What patterns has the code-architect agent learned so far?"
→ Should read .claude/agent-memory/code-architect/MEMORY.md
```

### 1.3 Agent Team Smoke Test

```
Create a small agent team to analyze our project README:
- Teammate 1: check if the README accurately describes our tech stack
- Teammate 2: check if setup instructions actually work
- Teammate 3: check for broken links
Keep it fast — this is just a verification test.
```

If all three teammates spawn, work in parallel, and the lead synthesizes a report — your team system works.

### 1.4 Self-Evolution Verification

```
After completing a task, run the self-evolve protocol.
Show me what would be written to:
1. Your own agent memory
2. Shared knowledge memory
3. Any skill proposals
Don't write yet — just show me what you WOULD write so I can verify the quality.
```

Once verified, tell it to proceed with the writes.

---

## 2. THREE WAYS TO INVOKE YOUR FLEET

### Method A: Natural Language (Auto-Delegation)

**Just describe what you need.** Claude reads each agent's `description` field and delegates automatically.

The KEY is to use **trigger words** that match agent descriptions:

| What You Say | Agent That Activates | Why |
|---|---|---|
| "Plan the authentication feature" | @code-architect | "plan" + "feature" in description |
| "Build the login page" | @frontend-engineer | "page" + "UI" |
| "Create the users API endpoint" | @backend-engineer | "API" + "endpoint" |
| "Write a migration for the orders table" | @database-engineer | "migration" |
| "Is this code secure?" | @security-auditor | "security" + "audit" |
| "This page is slow, help" | @performance-auditor | "slow" + "optimize" |
| "Check accessibility on the dashboard" | @design-auditor | "accessibility" |
| "Write tests for the payment service" | @test-engineer | "test" |
| "Set up the CI pipeline" | @devops-engineer | "CI" + "pipeline" |
| "Should we use Drizzle or Prisma?" | @tech-researcher | "should we use" + "compare" |
| "Document the API" | @documentation-writer | "document" + "API" |
| "Plan next sprint" | @project-manager | "sprint" + "plan" |
| "Analyze our market position" | @business-strategist | "market" + "competitive" |
| "Brainstorm feature ideas" | @brainstorm-facilitator | "brainstorm" + "ideas" |
| "Connect to the [external] API" | @api-integrator | "integration" + "API" |
| "Find all dead code" | @codebase-explorer | "dead code" + "find all" |
| "Full quality review before merge" | @quality-orchestrator | "quality review" |
| "Upgrade fleet to latest Claude Code" | @fleet-engineer | "upgrade fleet" |

### Method B: Explicit Agent Invocation

When you want to be SPECIFIC about which agent handles a task:

```
Use the security-auditor agent to review all authentication endpoints
```

```
Have the code-architect agent design the data model for a multi-tenant billing system
```

```
Use the test-engineer agent to write integration tests for the OrderService
```

### Method C: Slash Commands (Skills)

Invoke skills directly when you want a specific workflow:

```
/pr-review              → Comprehensive PR review
/sprint-planning        → Sprint planning ceremony
/threat-model           → STRIDE threat model for a feature
/retrospective          → Sprint retrospective
/tech-radar             → Technology evaluation
/decision-matrix        → Weighted decision analysis
/compound-loop          → Run the compound engineering cycle
/self-evolve            → Force a self-evolution check
/skill-factory          → Create a new skill from a pattern
/memory-curator         → Clean up and optimize memory files
/fleet-upgrade          → Audit fleet against latest Claude Code docs and upgrade
```

---

## 3. PROMPT TEMPLATES — COPY-PASTE FOR EVERY SCENARIO

### ────────────────────────────────
### 🚀 NEW FEATURE DEVELOPMENT
### ────────────────────────────────

#### Solo Agent (Small Feature)
```
Implement a [feature name] that [what it does].

Requirements:
- [Requirement 1]
- [Requirement 2]
- [Requirement 3]

Acceptance criteria:
- [Testable criterion 1]
- [Testable criterion 2]

Start with the code-architect agent to plan the approach.
Then implement with appropriate agents (frontend/backend/database).
Run self-evolve when done.
```

#### Agent Team (Large Feature)
```
Create an agent team to implement [feature name].

Overview: [2-3 sentence description of the feature]

Requirements:
- [List all requirements]

Team composition:
- Team Lead: @code-architect (plans architecture, coordinates, delegate mode only)
- Teammate 1: @frontend-engineer (owns frontend files — pages, components, styling)
- Teammate 2: @backend-engineer (owns backend files — modules, controllers, services, DTOs)
- Teammate 3: @database-engineer (owns database files — migrations, entities, queries)
- Teammate 4: @test-engineer (owns all *.test.* and *.spec.* files)

File ownership boundaries must be respected — no two teammates edit the same file.
Each teammate works in its own worktree.
After implementation, run /pr-review as quality gate.
Run /compound-loop to extract learnings.
```

#### Full-Stack Feature with Business Context
```
We need to build [feature] for [user persona] so they can [job to be done].

Business context:
- [Why this matters commercially]
- [User pain point it solves]
- [Success metrics: how we'll know it works]

First, have @business-strategist evaluate the feature against our product strategy.
Then have @code-architect design the technical approach.
Then create an agent team for implementation.
Finally, run a pre-merge quality gate with @quality-orchestrator.
```

### ────────────────────────────────
### 🔍 CODE REVIEW & AUDITS
### ────────────────────────────────

#### Quick PR Review
```
Review the changes in the current branch against main.
Use /pr-review for a comprehensive multi-dimensional review.
```

#### Deep Security Audit
```
Create an agent team for a comprehensive security audit of [scope].

Team:
- Lead: @security-auditor (coordinates, synthesizes findings)
- Teammate 1: Dependency scanner (npm audit, license check, CVE analysis)
- Teammate 2: Authentication reviewer (JWT lifecycle, session management, OAuth flows)
- Teammate 3: Input validation reviewer (injection, XSS, SSRF, path traversal)
- Teammate 4: Configuration reviewer (CORS, CSP, headers, secrets exposure)

Teammates should challenge each other's findings.
Output: Unified security report sorted by severity with specific file:line references.
```

#### Performance Audit
```
Use the performance-auditor agent to analyze [scope]:

1. Frontend: Bundle size analysis, Core Web Vitals assessment, render performance
2. Backend: API response times, N+1 queries, caching opportunities
3. Database: Slow query analysis, missing indexes, connection pool utilization

Establish baselines first, then recommend prioritized optimizations with expected impact.
Update performance-baselines.md with findings.
```

#### Design & Accessibility Audit
```
Use the design-auditor agent to review [component/page/section]:

Check against:
- WCAG 2.1 AA compliance
- Nielsen's 10 usability heuristics
- Our design token adherence (spacing, colors, typography)
- Responsive behavior across mobile/tablet/desktop
- All interactive states (hover, focus, active, disabled, loading, error, empty)

Output findings categorized as: Critical | Warning | Suggestion
```

#### Full Pre-Release Audit
```
Create an agent team for release readiness assessment of [version/branch]:

- Lead: @quality-orchestrator (coordinates all audits, produces unified report)
- Teammate 1: @security-auditor (security review)
- Teammate 2: @performance-auditor (performance review)
- Teammate 3: @design-auditor (UI/UX and accessibility review)
- Teammate 4: @test-engineer (test coverage verification, regression check)

Output: Single release readiness report with GO / NO-GO recommendation.
Each finding must include severity, file:line, description, and fix suggestion.
```

### ────────────────────────────────
### 🔧 BUG FIXING & DEBUGGING
### ────────────────────────────────

#### Single Bug
```
Bug: [describe the bug — what happens vs. what should happen]
Steps to reproduce: [numbered steps]
Expected: [expected behavior]
Actual: [actual behavior]

Debug this systematically:
1. Reproduce and confirm the bug
2. Isolate the root cause (don't guess — trace the actual code path)
3. Fix with minimal change
4. Add a regression test that would have caught this
5. Check if the same pattern exists elsewhere in the codebase
6. Update anti-patterns.md if this is a new category of bug
```

#### Complex Multi-System Bug
```
Create an agent team to debug [issue]:

Symptoms: [what's happening]
Affected area: [frontend/backend/database/all]

- Teammate 1: @frontend-engineer (investigate client-side behavior, network requests)
- Teammate 2: @backend-engineer (investigate API logs, service logic, error handling)
- Teammate 3: @database-engineer (investigate query performance, data integrity)

Teammates should share findings with each other in real-time.
When one teammate finds a clue, others should adjust their investigation.
Converge on root cause before implementing any fix.
```

### ────────────────────────────────
### 📋 PROJECT MANAGEMENT
### ────────────────────────────────

#### Sprint Planning
```
Use the project-manager agent to plan Sprint [N].

Context:
- Sprint duration: [2 weeks]
- Team capacity: [story points or days]
- Previous sprint velocity: [check your memory]
- Current backlog priorities: [list or reference to backlog]

Run /sprint-planning to execute the full ceremony.
Output a sprint plan with committed stories, risks, and capacity utilization.
```

#### Status Report
```
Use the project-manager agent to generate a status report.

Cover:
- Sprint progress (% complete, burndown trend)
- Completed items since last report
- Current blockers and risks
- Upcoming milestones
- Agent fleet health (any memory issues, skill gaps identified?)

Format: Executive summary with RAG status, then details.
```

#### Retrospective
```
Use the project-manager agent to run a sprint retrospective.

Run /retrospective with:
- What went well this sprint
- What didn't go well
- What should we change
- Agent fleet observations: which agents performed well? which need tuning?

Output: Action items with owners and deadlines.
Update shared memory with learnings.
```

### ────────────────────────────────
### 🧠 RESEARCH & STRATEGY
### ────────────────────────────────

#### Technology Evaluation
```
Use the tech-researcher agent to evaluate [technology/library/approach].

Research questions:
- How does it compare to our current solution?
- What's the migration effort?
- What are the risks?
- What do teams at our scale report about production usage?

Run /tech-radar to produce an Adopt/Trial/Assess/Hold recommendation.
Run /decision-matrix for a weighted comparison if there are multiple options.
```

#### Strategic Analysis
```
Create an agent team for strategic analysis of [opportunity/threat]:

- Lead: @business-strategist (coordinates analysis)
- Teammate 1: Market researcher (competitive landscape, market sizing)
- Teammate 2: @code-architect (technical feasibility, architecture implications)
- Teammate 3: @brainstorm-facilitator (creative alternatives, blue ocean opportunities)

Output: Strategic recommendation with evidence, risk assessment, and implementation roadmap.
```

#### Brainstorming Session
```
Use the brainstorm-facilitator agent for an ideation session on [topic].

Constraints:
- [Budget/time/technical constraints]
- [Must-have requirements]

Run a full ideation cycle:
1. Diverge: Generate 30+ ideas using SCAMPER, reverse brainstorming, and analogies
2. Cluster: Group by theme
3. Evaluate: Score on Impact × Feasibility × Novelty
4. Select: Top 3 ideas developed into mini-proposals
5. Next step: Identify the fastest way to validate the #1 idea
```

### ────────────────────────────────
### 🔄 REFACTORING
### ────────────────────────────────

```
Refactor [module/component/system] to [goal].

Before starting:
1. @code-architect: Design the target architecture
2. @test-engineer: Ensure current test coverage is sufficient (add tests if needed)
3. @codebase-explorer: Map all dependencies on the code being refactored

Then create an agent team for implementation:
- Each teammate owns a specific package/layer
- Work in worktrees to avoid conflicts
- Run tests after each major change
- @quality-orchestrator validates the result

This is a zero-regression refactor. No behavior changes. Tests must pass throughout.
```

### ────────────────────────────────
### 📚 DOCUMENTATION
### ────────────────────────────────

```
Use the documentation-writer agent to [task]:

Options:
- "Document all API endpoints" → generates OpenAPI/API docs
- "Write an ADR for our decision to use [technology]" → runs /adr-template
- "Create an onboarding guide for new developers" → runs /onboarding-guide
- "Update the CHANGELOG for this release" → runs /changelog-format
- "Write a runbook for [incident type]" → runs /runbook-template
```

### ────────────────────────────────
### 🛠️ FLEET MAINTENANCE
### ────────────────────────────────

#### Weekly Memory Cleanup
```
Run /memory-curator to clean up and optimize all memory files.
Show me a summary of:
- Which memories were removed (stale/duplicate)
- Which were promoted from agent-specific to shared
- Current line counts for all MEMORY.md files
- Any memories that are conflicting across agents
```

#### Skill Enhancement
```
Run /skill-factory to review recent work and identify:
- Any task patterns that should become new skills
- Any existing skills that need updating based on new learnings
- Any agent definitions that need updated skills: lists
Show me proposals before creating anything.
```

#### Fleet Health Check
```
Run a fleet health assessment:
1. Check all agent definitions are valid (name, description, tools, memory, skills)
2. Check all skill SKILL.md files have valid frontmatter
3. Check all MEMORY.md files are under 200 lines
4. Check shared-knowledge files for staleness (entries older than 60 days)
5. Check for skill proposals that haven't been implemented yet
6. Check for agent upgrade proposals that haven't been applied
7. Report: fleet health score, issues found, recommended actions
```

#### Monthly Fleet Upgrade (Claude Code Features)
```
Use the fleet-engineer agent to run a full fleet upgrade audit.
Read the latest Claude Code documentation and changelog.
Compare against our current fleet configuration.
Show me the full upgrade report before making any changes.
```

#### Quick Post-Update Check
```
Claude Code just updated. Use @fleet-engineer to:
1. Read the latest changelog entries
2. Check if any new features affect our fleet
3. Propose quick wins we should adopt immediately
```

#### Upgrade History
```
Show me .claude/fleet-upgrade-log.md
What features have we adopted? What's still pending?
Are there any deferred features we should reconsider?
```

---

## 4. DAILY WORKFLOWS — YOUR NEW ROUTINE

### Morning Standup
```
Good morning. Run a quick standup:
1. Check project-manager memory for current sprint status
2. Check shared-knowledge for any overnight findings
3. What's the highest priority task for today?
4. Any blockers or risks that need attention?
```

### Starting a Task
```
I'm working on [task/ticket]. Before I start:
1. Read relevant agent memories for any prior context
2. Check anti-patterns.md for known pitfalls in this area
3. Check design-patterns.md for established patterns to follow
4. Suggest which agent(s) should handle this and the approach
```

### Finishing a Task
```
I've finished [task]. Run the compound loop:
1. Review what was built
2. Run /pr-review for quality check
3. Run /self-evolve to extract learnings
4. Update relevant memory files
5. What should I work on next?
```

### End of Day
```
End of day wrap-up:
1. Summary of what was accomplished
2. Any new entries in shared-knowledge?
3. Current sprint burn-down status
4. Any risks or blockers for tomorrow?
5. Run /memory-curator if any memory files are getting large
```

---

## 5. MONITORING & QUALITY ASSURANCE

### How to Know Agents Are Working Well

**Check 1 — Memory Growth (Weekly)**
```
Show me the git diff for .claude/agent-memory/ since last week.
Are agents actively learning? Is the knowledge useful or just noise?
```

**Check 2 — Skill Evolution (Bi-weekly)**
```
Show me all entries in shared-knowledge/skill-proposals.md.
Which proposals should we implement? Which should we reject?
```

**Check 3 — Anti-Pattern Tracking**
```
Show me shared-knowledge/anti-patterns.md.
Are the same mistakes still happening, or has the fleet actually stopped repeating them?
```

**Check 4 — Estimation Accuracy (Per Sprint)**
```
Use @project-manager to compare estimated vs. actual for last sprint's stories.
What's our estimation accuracy trend? Is it improving?
```

**Check 5 — Agent Delegation Accuracy**
```
Review the last 10 tasks. For each one:
- Which agent handled it?
- Was that the RIGHT agent?
- Did it need to be redirected to a different agent?
If delegation accuracy is below 80%, agent descriptions need tuning.
```

### Red Flags to Watch For

| Red Flag | What It Means | Fix |
|---|---|---|
| Agent memory files are empty after weeks | Self-evolve isn't running | Check hooks, remind agents to update memory |
| Same bug pattern appears 3+ times | Anti-patterns.md not being read | Add to CLAUDE.md as a hard rule |
| Agent team takes 10+ minutes for simple tasks | Too many teammates spawned | Reduce team size, use subagents instead |
| Agent ignores its preloaded skills | Skill descriptions don't match triggers | Rewrite skill descriptions with better keywords |
| Context compaction happens mid-task | Agent is overloaded | Split task, use subagents for research |
| Memory files exceed 200 lines | Memory-curator hasn't run | Run /memory-curator immediately |
| Agents contradict each other | Shared knowledge has conflicts | Review and resolve in shared-knowledge/ |

---

## 6. TROUBLESHOOTING

### "Claude isn't using my agents"
- Check that agent `.md` files are in `.claude/agents/` (not just `.claude/`)
- Verify the `description` field contains trigger words matching your prompt
- Try explicit: `"Use the security-auditor agent to..."`
- Run `/agents` in Claude Code to see loaded agents
- Restart session if you just created the agent files

### "Agent Teams won't spawn"
- Verify `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in settings.json OR environment
- Agent Teams requires Opus 4.6 — check your model with `/model`
- Try a simpler team first (2 teammates, not 5)
- Check that you've described file ownership clearly

### "Skills aren't auto-invoking"
- Check that `description` in SKILL.md frontmatter contains relevant keywords
- Make sure `disable-model-invocation` is NOT true (unless you want manual only)
- Try `/skill-name` to test it works manually first
- Add skill names to CLAUDE.md as a reminder to Claude

### "Agent memory is always empty"
- Check `memory: project` is in the agent's frontmatter
- Verify `.claude/agent-memory/<agent-name>/` directory exists
- Check that the agent's system prompt includes memory update instructions
- Manually seed MEMORY.md with one entry to prime the pattern

### "Agent team is too slow/expensive"
- Each teammate is a full Claude instance (3-4x single session token cost)
- Use subagents (not teams) when workers don't need to communicate
- Limit to 3-5 teammates max for most tasks
- Use `model: sonnet` on teammates for execution, keep lead on opus
- Note: the `skills:` preload bug for team teammates exists (Issue #29441) —
  skills may not load in team-spawned agents. Workaround: embed critical
  knowledge directly in the agent's system prompt body until fixed.

---

## 7. COST MANAGEMENT

### Token Budget Rules of Thumb

| Operation | Approximate Cost |
|---|---|
| Single agent task (Sonnet) | ~20K-50K tokens |
| Single agent task (Opus) | ~20K-50K tokens (5x cost per token) |
| Subagent spawn | ~10K-30K tokens each |
| Agent Team (3 teammates) | ~3-4x single session |
| Agent Team (5 teammates) | ~5-6x single session |
| Full pre-release audit team | ~150K-300K tokens |

### Cost Optimization Strategies

1. **Use subagents for independent tasks** — they're cheaper than Agent Teams
2. **Use Agent Teams only when communication is needed** — workers sharing findings
3. **Set `model: sonnet` on execution agents** — keep Opus for planning/coordination
4. **Keep CLAUDE.md lean** — every unnecessary instruction costs tokens every turn
5. **Use skills with progressive disclosure** — don't embed everything in agent prompts
6. **Run /memory-curator weekly** — bloated memory files waste context on every startup
7. **Use /compact when context fills up** — CLAUDE.md survives compaction, conversation doesn't
8. **Batch similar tasks** — one agent session handling 5 reviews beats 5 separate sessions

---

## QUICK REFERENCE CARD

```
╔══════════════════════════════════════════════════════════════╗
║                   FLEET QUICK REFERENCE                      ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  START A FEATURE:                                            ║
║  "Plan and implement [feature] with an agent team"           ║
║                                                              ║
║  REVIEW CODE:                                                ║
║  /pr-review                                                  ║
║                                                              ║
║  SECURITY AUDIT:                                             ║
║  "Run a security audit on [scope] with an agent team"        ║
║                                                              ║
║  PERFORMANCE CHECK:                                          ║
║  "Use @performance-auditor to analyze [scope]"               ║
║                                                              ║
║  PLAN SPRINT:                                                ║
║  /sprint-planning                                            ║
║                                                              ║
║  BRAINSTORM:                                                 ║
║  "Use @brainstorm-facilitator to ideate on [topic]"          ║
║                                                              ║
║  EVALUATE TECHNOLOGY:                                        ║
║  /tech-radar  or  /decision-matrix                           ║
║                                                              ║
║  FIX A BUG:                                                  ║
║  "Debug [issue] — reproduce, isolate, fix, test"             ║
║                                                              ║
║  MAINTAIN FLEET:                                             ║
║  /memory-curator  →  /skill-factory  →  fleet health check   ║
║                                                              ║
║  UPGRADE FLEET (MONTHLY):                                    ║
║  /fleet-upgrade  or  "Use @fleet-engineer for full audit"    ║
║                                                              ║
║  EXTRACT LEARNINGS:                                          ║
║  /compound-loop  →  /self-evolve                             ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
```
