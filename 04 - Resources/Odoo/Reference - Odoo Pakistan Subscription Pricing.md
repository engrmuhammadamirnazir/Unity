---
type: reference
category: odoo
tags: [odoo, pricing, subscription, pakistan, sales]
source: "Odoo SA Pakistan regional pricing — user input 2026-05-12"
created: 2026-05-12
updated: 2026-05-12
applies-to: [D:/Development, D:/ECOSIRE.COM, D:/ECOSIRE.AI]
---

# Reference — Odoo Pakistan Subscription Pricing

> Odoo Enterprise per-user subscription rates for Pakistan, as offered by Odoo SA directly. Used when quoting any Pakistan-based client that needs Odoo Enterprise (vs Community, which is free forever).

## Rates (paid by client to Odoo SA — NOT marked up by ECOSIRE)

| Plan | Hosting | Third-party modules | Pakistan rate |
|------|---------|---------------------|---------------|
| Standard — Online (Odoo-hosted) | Odoo SA cloud (Odoo.sh-managed) | **Not allowed** (App Store + custom blocked) | **USD 8.95 / user / month** |
| Custom — Self-hosted | Client infra (or ECOSIRE managed hosting) | **Allowed** (App Store + in-house) | **USD 10.90 / user / month** |

Source: Odoo SA regional pricing for Pakistan as of 2026-05-12. **Verify on `odoo.com/pricing` if any quote is more than ~30 days old** — regional pricing changes periodically.

## Internal-user definition

- An internal user is anyone who logs into Odoo (back-office team, manager, finance, owner). Each consumes one licence.
- Restricted POS-only "cashier" sessions do **not** consume an internal-user licence — a single licence covers all POS terminals at a venue.
- A waiter who only uses POS → no licence required.
- A floor manager who logs into POS AND back-office (inventory, sales reports) → one licence required.

## Why ECOSIRE recommends Custom (self-hosted) over Standard

Standard blocks third-party modules. That means:

- ECOSIRE store-management connectors (Shopify, Amazon, Daraz, etc.) cannot be installed.
- Pakistani-specific plug-ins (FBR / PRA tax filing, JazzCash / EasyPaisa gateways) cannot be installed.
- Food-delivery aggregator connectors (Foodpanda, Cheetay) cannot be installed.

The extra **USD 1.95 / user / month** for Custom is trivial relative to feature flexibility. Custom self-hosted runs on ECOSIRE managed hosting at USD 35 / month flat (or PKR 10,000 / month on PKR pricing).

## Sample TCO (10-user restaurant scenario)

| Users | USD / month | PKR / month (~PKR 285/USD) | USD / year | PKR / year |
|-------|-------------|----------------------------|------------|------------|
| 5     | $54.50      | ~PKR 15,500                | $654       | ~PKR 186K  |
| 10    | $109.00     | ~PKR 31,000                | $1,308     | ~PKR 373K  |
| 15    | $163.50     | ~PKR 46,600                | $1,962     | ~PKR 559K  |

PKR conversion is indicative — actual Odoo SA billing is USD or EUR.

## ECOSIRE pricing tiers built on top (restaurant ERP reference scenario)

| Tier | One-time | Recurring (hosting) | Odoo SA subscription |
|------|----------|---------------------|----------------------|
| A — POS only (no Enterprise) | PKR 50,000 | PKR 10,000 / month | n/a |
| B — Community ERP (Odoo Community, no per-user fee) | USD 350 | USD 35 / month | none (Community is free) |
| C — Enterprise ERP (Custom self-hosted) | USD 500 | USD 35 / month | USD 10.90 / user / month → 10 users ≈ USD 109 / month |

First applied: Brad & Brad Fine Dining ERP Analysis Report 2026-05-12. Tier structure generalises to any single-venue Pakistan restaurant client.

## When this is consulted

- Any Pakistan-based client proposal that needs Odoo Enterprise — read this file before quoting.
- `sales-proposal` agent in `D:/Development/.claude/agents/sales-proposal.md` references this when scoping ERP for Pakistan clients.
- Update this file whenever Odoo SA changes Pakistan regional pricing. Sync change to MEMORY.md index via the `feedback_quote_clients_use_website_prices_not_manifest` pattern (verify before quoting).

## Related

- [[Odoo Community vs Enterprise]] — full feature comparison
- [[Ecosire - Client Portfolio]] — where Brad & Brad is the first Pakistan client on this pricing
- [[Pattern - reportlab table cells must be paragraphs]] — captured the same session (document-generation iteration)
- Workspace memory: `~/.claude/projects/D--Development/memory/reference_odoo_pakistan_pricing.md`
