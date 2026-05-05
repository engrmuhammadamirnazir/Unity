---
type: pattern
tags: [pricing, sales, ecosire, manifest-drift, fleet-wide]
aliases: [website-prices-not-manifest, ecosire-canonical-price-source]
source-workspaces: [D:/Development, D:/ECOSIRE.COM]
created: 2026-05-05
updated: 2026-05-05
---

# Quote clients using website prices, not module manifest prices

## Rule

When generating client quotes / proposals for ECOSIRE App Store modules, **use ecosire.com prices (canonical = `D:/ECOSIRE.COM/_update_product_prices.sql`), not the prices written in `__manifest__.py` files in `D:/Development/odoo*/server/addons/<module>/`**.

## Why

Discovered 2026-05-05 during Edwin Ardila's 10-module bundle proposal. First version of the quote used manifest prices ($499 × 9 + $249 = **$4,740**). Amir caught it: 4 modules are listed LOWER on ecosire.com than in their manifests — AliExpress $249, Temu $349, TikTok $349, Walmart $349. True list was **$4,040** ($700 less).

The Wave A-M pump (April 24-25, 2026) bumped manifest versions to v19.0.2.x.0 and pushed prices to $499 across 94 modules, but ecosire.com was selectively re-synced afterwards — some modules kept the lower public price as competitive positioning, while others moved up.

Quoting from manifest = quoting a public price the client can verify is wrong by visiting ecosire.com. Embarrassing, costs trust, and it leaves money on the table from the wrong direction (looks greedy, not generous).

## How to apply

For any client quote or proposal touching App Store modules:

1. **Canonical source:** `D:/ECOSIRE.COM/_update_product_prices.sql` — has every module slug + current website price (UPDATE statements).
2. **For modules not in the SQL file:** WebFetch `https://www.ecosire.com/products/<slug>` and extract the price. The slug is the manifest folder name with underscores → hyphens (e.g., `targetplus_store_management` → `targetplus-store-management`).
3. **The Drizzle seed at `D:/ECOSIRE.COM/packages/db/src/seeds/products.ts` is OUTDATED** — has tier-based stub prices ($99/$149/$199/$250) that don't reflect post-pump reality. Don't read that as truth.
4. **DO NOT** read the module's `__manifest__.py` `price` field as the public price for quoting. Manifest prices are dev-side only — they're what we INTEND to charge but may not be what's currently listed.
5. If website price is HIGHER than manifest (rare) — still use website. It's the public number.

## Action item (deferred)

After every future Wave-style pump upgrade in `D:/Development`, update `D:/ECOSIRE.COM/_update_product_prices.sql` AND apply it to the live website DB in the same session: `psql -f _update_product_prices.sql <prod-db>`. Otherwise quote-time corrections will keep happening.

## Cross-references

- Source feedback: `~/.claude/projects/D--Development/memory/feedback_quote_clients_use_website_prices_not_manifest.md`
- Sister rule: [[feedback_webkul_comparison_leverage_module_gap]]
- Triggered during: `~/.claude/projects/D--Development/memory/edwin_ardila_proposal_2026_05_05.md`
- D:/Development CLAUDE.md updated with one-liner pointer in pricing section.
