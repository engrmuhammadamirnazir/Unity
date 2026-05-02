---
name: Hive-mind propagates LEARNING not ARTIFACTS — workspace skills/agents stay scoped
description: Cross-workspace fleet broadcast of project-specific skills/agents is the wrong abstraction. What should cross workspace boundaries is the LESSON (the pattern, the gotcha, the rule), not the implementation that incidentally encoded it. Each workspace's skills/agents are tuned to that codebase's conventions, model names, server topology — copying them to peers creates bloat, drift, and false uniformity. Correct broadcast vector: aggregated lessons index + canonical patterns MOC. Don't auto-propagate artifacts.
type: feedback
updated: 2026-05-03
originSessionId: 2026_05_03_hive_mind_infrastructure_overhaul
---

## The rule

**Don't auto-broadcast workspace skills/agents to other workspaces.** Each workspace's `.claude/skills/` and `.claude/agents/` are scoped intentionally — they encode that workspace's models, server names, deploy paths, and conventions. Forcing them on peer workspaces creates:

- **Bloat** — every workspace ends up with N×M skills, most irrelevant.
- **Drift** — skill in workspace A diverges from copy in workspace B; nobody knows which is canonical.
- **False uniformity** — looks like ECOSIRE.COM and ECOSIRE.AI share infrastructure, but they don't.
- **Coupling** — a fix to ECOSIRE.COM's `deploy-shopify` skill silently changes behavior in 4 other workspaces.

What SHOULD cross workspace boundaries is the **lesson**: "Bitnami filestore must be chowned to `odoo` not `daemon`," "Wipe account.move by `name` not `ref`," "`@api.depends` on optional-module field crashes the registry." Those are universal. The skill that happens to encode one is not.

## Why

The mistake is conflating two things:

| Layer | Belongs in | Cross-workspace? | Why |
|-------|-----------|:----------------:|-----|
| **Knowledge** (pattern, rule, gotcha) | `~/.claude/lessons/`, Unity Patterns MOC | ✅ yes | Universal — any workspace can hit the same bug |
| **Artifact** (skill, agent, hook) | `<workspace>/.claude/` | ❌ no (auto) | Scoped — encodes workspace-specific conventions |
| **Truly cross-cutting workflow skill** | `~/.claude/skills/` (user level) | ✅ but EXPLICIT | Author at user level deliberately, not via auto-promotion from a workspace |

The hive-mind = sharing what we **learned**, not what we **built**. The build sometimes encodes the learning, but they aren't the same thing.

## Real-world failure mode this prevents

Imagine ECOSIRE.COM has a skill `deploy-stripe-webhook-handler` that hard-codes `https://ecosire.com/api/stripe/webhook`. An auto-broadcast pushes it into D:/ECOSIRE.AI/.claude/skills/. Months later an agent in ECOSIRE.AI invokes `deploy-stripe-webhook-handler` thinking it's local — and it tries to deploy to the ecosire.com URL, breaking either the production site or silently doing nothing. The agent has no way to know the skill was auto-imported from a sibling.

## How to apply

1. **For workspace `.claude/skills/` and `.claude/agents/`**: mirror them off-machine for backup (good — `~/.claude/workspaces/<ws>/.claude/`), but DO NOT propagate them between workspaces.
2. **For genuinely cross-cutting workflow skills** (the `end-session`, `hive-mind-sync`, `graphify` style): author them at user level (`~/.claude/skills/`) deliberately. They're available everywhere because user-level always is.
3. **For workspace-authored skills that turn out to be cross-cutting**: explicit one-off promotion (read the skill, decide it's universal, manually copy or `mv` to `~/.claude/skills/`). Not bulk, not automatic.
4. **For lessons**: aggregate all workspace `feedback_*.md` into `~/.claude/lessons/INDEX.md` daily, broadcast a read-only `LESSONS.md` into every workspace `.claude/`, promote canonical fleet-wide ones to Unity `04 - Resources/Patterns/`. This IS the right propagation vector.

## Concrete rejection of the design that prompted this rule

Initial `fleet_broadcast.py` had three modes:

| Mode | Was | Now |
|------|-----|-----|
| `--pull-lessons` | broadcast `LESSONS.md` to every workspace | ✅ kept (learning propagation, correct) |
| `--promote-to-user` | bulk copy workspace skills/agents to `~/.claude/` | ❌ removed (auto-promotion of artifacts is wrong) — replaced by `--promote-skill <name>` requiring explicit per-skill choice |
| `--broadcast` | copy ECOSIRE.COM skills/agents to peer workspaces | ❌ removed entirely — wrong abstraction |

## Reusable across

Any environment that has multiple project workspaces sharing `.claude/` infrastructure or any agent/skill ecosystem:
- Multi-tenant agent fleets
- Monorepo subprojects with their own conventions
- Multi-client codebases (Sahara, Obliq, Oenoteca, etc.) where each has tuned tooling
- Plugin marketplaces — same logic for "should I auto-install this"

## Related memories

- [feedback_aggregator_currency_axis_bug.md](feedback_aggregator_currency_axis_bug.md) — the kind of thing that SHOULD broadcast (universal pattern)
- [feedback_db_wipe_ref_vs_name_pattern.md](feedback_db_wipe_ref_vs_name_pattern.md) — same — universal pattern, broadcast via lessons
- The fleet_broadcast.py implementation in `~/.claude/scripts/` — codifies this rule
