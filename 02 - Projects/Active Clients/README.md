---
type: index
tags: [clients, active, index]
created: 2026-04-22
updated: 2026-04-22
---

# Active Clients — Index

Top active clients with dedicated sub-folders. For the full portfolio table see [[Ecosire - Client Portfolio]].

## Folders

- [[Sahara Properties/README|Sahara Properties]] — Ajman · PMS v19.0.6.0.5 live · Bitnami Odoo 19
- [[Obliq Furniture/README|Obliq Furniture]] — Dubai · obliq_automation v19.0.7.0.0 · Hetzner (migrated)
- [[Oenoteca Fine Wines/README|Oenoteca Fine Wines]] — Houston · oenoteca_customizations v1.2.0 · Hetzner (migrated)
- [[Diamond Investment Group/README|Diamond Investment Group (STIG)]] — 11 entities · $4K project · M1 paid + M2 UAT pass · Quicken referral
- [[Amalfi Foods/README|Amalfi Foods / Farhoud Ventures]] — Bahrain · 5-co 28-user Odoo 17 → v19 · NDA signed · discovery
- [[Remittance (Suleman Arif)/README|Remittance (Suleman Arif)]] — remittance_management v19.0.8.2.4 live · UAT kickoff
- [[Alvi-Dental/README|Alvi Dental]] — 3-tenant Hetzner shared · UAT ongoing

## Convention for new entries

When a client grows beyond a single row in the portfolio table (dedicated server, multi-session work, $1K+ contract, or recurring deployments), create a folder under `Active Clients/` with at minimum a `README.md` containing:

1. **Quick facts table** — server, SSH key path, domain, module versions, contract value
2. **Recent state** — bullet list, dated
3. **Where detail lives** — pointers to `~/.claude/projects/*/memory/` files + code paths
4. **Open threads** — unresolved issues
5. **Next actions** — checkbox list

Optional sub-folders inside a client folder:

- `Meetings/` — meeting notes dated `YYYY-MM-DD Topic.md`
- `Proposals/` — proposal PDFs + drafts
- `Deployments/` — deploy runbooks + post-deploy notes
- `Server Details.md` — single reference note (IP, paths, services, backup strategy) — **never credential values**

## Rule

- **Credential values never live here.** Reference `Unity/03 - Areas/Credentials & Access/Server & Key Inventory.md` by path.
- **Session memory is still the operational store** for day-to-day work. These READMEs are the 30-second-orientation layer. Bulk detail still lives in `~/.claude/projects/D--Development/memory/` and per-client session logs.
