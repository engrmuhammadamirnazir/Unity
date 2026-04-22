---
type: business
tags: [business, managed-hosting, saas, hetzner]
status: live
created: 2026-04-22
updated: 2026-04-22
---

# Ecosire Managed Hosting

**Launched 2026-04-17.** Concierge Odoo 19 hosting service — dedicated instance + managed infrastructure + daily backups + patching + monitoring + staging. No setup fee, no lock-in. The manual/concierge precursor to [[../ECOSIRE.IO/README|ECOSIRE.IO]].

## Offer

| Tier | USD/mo | What's included |
|------|--------|-----------------|
| **Baseline** | $50 | Dedicated Odoo 19 instance + PG15 + Nginx TLS + daily backups + monitoring + patching + staging |
| **Scaled** | $100 | For larger tenants (e.g., 28+ users, multi-company) — CPX51-sized VM, enhanced backup retention |

No setup fee. No lock-in. Runs on **Hetzner** (CPX42 baseline, CPX51 for scaled).

## First pitches

- **Diamond Investment Group (STIG)** — `91.99.75.227`, $4K implementation bundle. M1 delivered + paid. Hosting continues post-M2.
- **Amalfi Foods** — Bahrain, 5-co, 28 users. $100/mo proposed. $750 bundled migration + v19 upgrade fee conditional on 12-mo commitment. Awaiting discovery call.
- **Diamond/STIG first in the door** — the offer was first drafted for them (2026-04-17 session when Diamond asked about hosting).

## Stack per tenant

- Hetzner Cloud VPS (CPX42 baseline, CPX51 for scaled)
- Docker Odoo 19 CE + PG15
- Nginx HTTP/2 with Let's Encrypt TLS (certbot auto-renew)
- UFW + fail2ban firewall
- Daily backups to Hetzner Storage Box (BX11) — per-tenant subfolder
- Staging environment on the same VPS

## Where detail lives

- **Memory pointer:** `~/.claude/projects/D--Development/memory/ecosire_managed_hosting.md`
- **Per-client sessions:** see each active client's README (Diamond, Amalfi) under `02 - Projects/Active Clients/`.

## Current focus

- Build playbook for tenant onboarding (1-click provisioning TBD).
- Integrate monitoring/alerting (Grafana? Healthchecks.io?).
- Eventually: fold into ECOSIRE.IO platform as the service tier.

## Cross-workspace dependencies

- Shared with the 215-module line — clients on Managed Hosting typically also consume custom Odoo modules from `D:/Development`.
- Backup strategy is shared across all hosted tenants.

## Open questions

- Pricing for enterprise tenants beyond $100/mo (Ghitha SWF scope for Amalfi = $30-60K group-ERP).
- Automation level vs manual ops — scaling hands-on concierge beyond ~10 tenants won't work; that's when ECOSIRE.IO needs to replace it.
