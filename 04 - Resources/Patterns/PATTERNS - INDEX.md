---
type: moc
tags: [patterns, gotchas, hive-mind, cross-project]
aliases: [Patterns MOC, Fleet Patterns]
created: 2026-05-03
updated: 2026-05-03
---

# Fleet Patterns — Cross-Project Gotchas

> Reusable bugs, mistakes, and conventions worth knowing in EVERY workspace.
> These are the canonical, fleet-wide patterns. Workspace-specific lessons live
> in `~/.claude/projects/<workspace>/memory/feedback_*.md`. Patterns promoted to
> Unity are the ones that apply broadly.
>
> For the comprehensive index of ALL 645+ lessons across every workspace, see
> `~/.claude/lessons/INDEX.md` (auto-generated daily).
>
> For semantic / graph search: `/graphify query "topic"`.

## How to use

1. Hit Ctrl+P in Obsidian and search by name.
2. Run `grep -l "term" 04\ -\ Resources/Patterns/*.md` from terminal.
3. Wikilinks in Session Log entries are clickable.

## How to add

1. Identified in `~/.claude/projects/<ws>/memory/feedback_<topic>.md`.
2. If it's reusable in any other workspace, copy it here.
3. Add a one-line entry below.
4. The next `hive-mind-sync` push commits it.

## The principle

**Lessons propagate, artifacts stay scoped.** What lives here are *patterns* — universal gotchas, rules, and conventions that apply in every workspace. What does NOT live here (and is NOT auto-broadcast across workspaces) are project-specific *implementations* — skills, agents, and hooks that encode a workspace's particular models, server names, or conventions. Sharing what we *learned* is right; copying what we *built* is wrong. See [[feedback_propagate_learning_not_artifacts]] in the source memory for the full reasoning.

## Canonical patterns (newest first)

### Hive-mind / Fleet
- [[feedback_propagate_learning_not_artifacts]] — Don't auto-broadcast workspace skills/agents to peer workspaces. Each workspace's `.claude/skills/` is scoped to that codebase. Cross-workspace propagation = bloat + drift + false uniformity. Broadcast LESSONS via `~/.claude/lessons/`, not implementations.

### Odoo / ORM
- [[feedback_aggregator_currency_axis_bug]] — Custom Odoo SQL aggregators over `account_move_line` that group by `currency_id` MUST use `SUM(amount_currency)` not `SUM(debit-credit)`. Otherwise foreign-currency-tagged AMLs get attributed to the wrong currency bucket and totals silently double-count.
- [[feedback_je_foreign_amount_as_company_debit_credit]] — Don't pass foreign-currency amounts as `debit`/`credit` (those are always company-currency). Convert via the operator's quoted rate AND set `amount_currency`/`currency_id` for the foreign side.
- [[feedback_auto_link_settlement_double_count_collision]] — `_auto_link_settlement_on_create` fires on `Settlement.create()` and creates ANOTHER settlement, double-counting. Use a `skip_auto_settle=True` context flag on programmatic creates.
- [[feedback_api_onchange_doesnt_fire_programmatic_set]] — `@api.onchange` only fires on UI edits, never on `record.write()` or `record.<field> = X` from code. For programmatic flows, use a helper method that the onchange ALSO calls.
- [[feedback_api_depends_optional_field]] — `@api.depends('rel.field_from_optional_module')` raises `ValueError: Wrong @depends` on first write to ANY model when the optional module is absent — even with runtime hasattr guards. Lazy registry crash. Drop from depends, or hard-add manifest dep.
- [[feedback_odoo_currency_rate_cron_stuck]] — `res.currency.rate.update_currency_rates_cron` defaults to `nextcall` in the past for fresh DBs. Manually trigger once in `odoo-bin shell` and reset `nextcall`.

### SQL / Data wipes
- [[feedback_db_wipe_ref_vs_name_pattern]] — When wiping `account.move` rows, match on `name` (move number, always starts with journal code) NOT `ref` (free-text, often "Payment for X" / "Reversal of: X" without journal-code prefix). REF-based ILIKE leaves orphans.

### Deploy / Bitnami / Filesystem
- [[feedback_odoo_bin_run_as_odoo_user_not_daemon]] — Bitnami Odoo workers run as `odoo` user but `bnsupport-tool` and some scripts run as `daemon`. Run upgrades as `sudo -u odoo odoo-bin -u <module>` — running as `daemon` breaks filestore ownership and causes silent attachment failures post-upgrade.

## Related infra

- `~/.claude/lessons/INDEX.md` — comprehensive 645-lesson index across ALL workspaces
- `~/.claude/lessons/by-pattern/<pattern>.md` — categorized rollups (orm, sql, deploy, security, etc.)
- `~/.claude/scripts/lessons_rebuild.py` — regenerates the index daily
- `~/.claude/scripts/fleet_broadcast.py` — propagates skills/agents across workspaces
- `~/.claude/scripts/backup_all_workspaces.py` — daily backup of every `.claude/` to GitHub

## Wired skills

- `/hive-mind-load` — runs at session start to load Unity context
- `/hive-mind-sync` — runs at session end to append Session Log + push canonical facts
- `/hive-mind-query` — query Unity's knowledge graph
- `/graphify` — semantic graph over `~/.claude/lessons/`
