---
type: pattern
tags: [pattern, agents, parallel-execution, content-pipeline, ecosire-com, claude-code]
aliases: [Shard-merge pattern, Parallel writer pattern]
created: 2026-05-04
updated: 2026-05-04
applies-to: [any workspace, content pipelines, parallel subagent dispatch]
---

# Subagent shard-merge pattern for parallel content writers

When dispatching N parallel content-writing (or registry-updating) subagents that all need to add entries to a single shared JSON/registry file, **DO NOT** have the agents edit the shared file directly. Each agent writes to its own shard file in a coordination directory; the parent merges shards atomically after all agents return.

## Why

Successfully used 2026-05-04 (ECOSIRE.COM SEO push: 5 parallel content writers each adding ~20 blog post titles + descriptions to `apps/web/messages/en.json`). Direct concurrent writes to a shared JSON would cause:

- **Last-write-wins data loss** — one agent reads en.json, second agent writes a key, first agent writes back overwriting
- **JSON corruption mid-write** if interleaved
- **Difficult auditing** — can't tell which agent wrote which entry without git blame
- **Agent failure mid-write** leaves the file in undefined state

## How to apply

### 1. Coordinator (parent agent) sets up shard dir before dispatching

```bash
mkdir -p _content_pipeline/shards
```

### 2. Each subagent gets a unique shard path in its prompt, and is FORBIDDEN from editing the shared file

```
Write your batch entries to `_content_pipeline/shards/<your-cluster-name>.json`
as `{"<slug>": {...}, ...}`. DO NOT edit the shared en.json directly.
```

### 3. Each subagent's workflow

- Create shard file with `{}` if it doesn't exist
- For each item it produces, read shard, append, write back
- Final report: shard path + entry count

### 4. After all subagents return, coordinator merges atomically

```python
for shard_path in glob.glob('_content_pipeline/shards/*.json'):
  shard = json.load(open(shard_path))
  for slug, entry in shard.items():
    if slug in posts:
      collisions.append(slug)
      continue   # never overwrite — log instead
    posts[slug] = entry
# write to .tmp, validate JSON, then os.replace() — atomic
```

### 5. Shard files become traceability artifacts — keep them after merge

Audit which agent produced which entry. Re-run translation/validation per-shard if anything goes wrong.

## What every agent prompt MUST say (for this pattern)

- "DO NOT touch the shared registry directly — write only to your shard"
- "DO NOT touch artifacts outside your assigned slugs/keys"
- The exact shard path + the JSON shape expected
- Verification step: agent must confirm its shard has N entries before reporting "done"

## Limitations

- Only works when entries are **independent** (no cross-references between shards)
- For **coupled changes**, sequential is correct
- Pairs naturally with **post-merge content-quality gates** (validate-blog-i18n.mjs etc.) — those run AFTER merge but BEFORE deploy and catch issues that any individual shard would have looked clean for in isolation

## Concrete instances

- **2026-05-04 ECOSIRE.COM SEO push** — 5 wave-1 + 2 wave-2 writers, 180 articles total, 100% successful merges, 0 collisions, 0 lost entries. See [[project_seo_push_180_articles_2026_05_04]] in D:/ECOSIRE.COM project memory.

## Cross-references

- [[PATTERNS - INDEX]]
- [[feedback_propagate_learning_not_artifacts]] — why this pattern is captured at Unity (cross-workspace) level rather than per-workspace
