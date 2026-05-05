---
type: pattern
tags: [sales, competitive-pricing, webkul, ecosire, fleet-wide]
aliases: [webkul-module-gap-leverage, prospect-comparison-research-first]
source-workspaces: [D:/Development]
created: 2026-05-05
updated: 2026-05-05
---

# When a prospect compares us with Webkul, research the module-coverage gap first

## Rule

When a prospect cites Webkul (or any single competitor) and asks us to match their pricing, **DO NOT capitulate to per-module price-matching**. Research the competitor's actual module coverage first; the leverage is almost always in the gap.

## Why

Edwin Ardila (2026-05-05) wanted 10 store-management modules and was comparing with Webkul. Naive response would have been "match Webkul's per-module price × 10". Real picture after WebFetch on Webkul's Odoo App Store seller page + their own marketplace site:

- Webkul has Amazon ($349), eBay ($349), Walmart ($399), Shopify ($198), AliExpress dropshipping-only ($219). **5 of 10.**
- Webkul does NOT have Zalando, Miravia, Temu, TikTok Shop, Target Plus. **ECOSIRE-only on those 5.**

Webkul's 5-module total: $1,515.64 (avg $303/module). For the other 5, the prospect would have to either source from us OR fragment across 3-4 minor vendors (Flexigo / Serpent for Temu, ConstaCloud's $567 monolithic 200+ marketplace bundle).

Final ECOSIRE bundle: $2,290 — average $229/module — which **beats Webkul's per-module average AND includes 5 modules they don't sell**. Justified the price hold without giving away the entire margin.

## How to apply

For any prospect comparing us with a single competitor:

1. **Get the binding module list first.** Prospects often shift their list mid-conversation (Edwin replaced 4 modules between his first and second WhatsApp message). Quote on the final list only.
2. **WebFetch each requested module on the competitor.** For Webkul:
   - Odoo App Store: `https://apps.odoo.com/apps/modules/browse?author=Webkul`
   - Their marketplace: `https://store.webkul.com/odoo.html`
   - Per-module URL pattern: usually `https://store.webkul.com/odoo-<platform>-connector.html`
3. **Find the gap.** Tally:
   - Modules where competitor matches us 1:1
   - Modules where competitor's offering is narrower scope (e.g., Webkul AliExpress = dropshipping only, ours = full marketplace sync)
   - Modules ECOSIRE has but competitor doesn't sell at all
4. **Quote the bundle, not modules.** Bundle pricing must:
   - Beat the competitor's average per-module on the OVERLAPPING set (so the comparison isn't ugly)
   - Hold near full margin on the ECOSIRE-only modules where there's no comparison
   - Round to a clean number ending in 0 or 90 (psychological)
5. **Frame the reply** to lead with the coverage gap, not the discount. The prospect's brain registers "they're the only single-vendor option for my complete stack" before "they cost more than X". Once that's anchored, the bundle price feels reasonable.
6. **Include services the competitor charges separately for**: free installation on their stack (Odoo SH push to their branch is trivial for us), 60-day support, all 3 Odoo versions in one license, source code (LGPL-3, no lock-in), lifetime updates. These are real differentiators that justify ~$50-100/module premium without seeming like padding.

## Floor

Don't let prospects negotiate below the bundle floor. The floor IS the offer — there's no "well, maybe a bit lower". If they push back below floor, the response is scope reduction (drop modules) not deeper discount. Otherwise we train every future Webkul-comparing prospect to ask for $-X off our published bundle quote.

## Cross-references

- Source feedback: `~/.claude/projects/D--Development/memory/feedback_webkul_comparison_leverage_module_gap.md`
- Sister rule: [[feedback_quote_clients_use_website_prices_not_manifest]] — fired in same session.
- Triggered during: `~/.claude/projects/D--Development/memory/edwin_ardila_proposal_2026_05_05.md`
