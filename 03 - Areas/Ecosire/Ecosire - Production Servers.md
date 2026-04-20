---
type: reference
tags: [ecosire, servers, production, aws, hetzner, infrastructure]
created: 2026-03-02
updated: 2026-04-10
---

# Ecosire — Production Servers

> All production server details, deployment commands, and architecture.

---

## Server Inventory

| # | Server | IP | Domain | Purpose | Stack | Status |
|---|--------|----|--------|---------|-------|--------|
| 1 | ECOSIRE.COM | 13.223.116.181 | ecosire.com | Main SaaS Platform | EC2 t3.large, Ubuntu 24.04 | LIVE |
| 2 | ECOSIRE.AI | 54.197.92.23 | ecosire.ai | AI Content Engine | EC2 t3.large, Ubuntu 24.04 | LIVE |
| 3 | Sahara Properties | 3.232.201.222 | saharaproperties.ecosire.com | Client Odoo 19 | Bitnami | LIVE |
| 4 | Obliq + Oenoteca | 52.206.177.34 | — | Client Odoo 19 (SHARED) | Bitnami | LIVE |
| 5 | Demo Server | 37.27.2.10 | demo.ecosire.com | Product showcase (201 modules) | Hetzner CPX42 | LIVE |
| 6 | Remittance | 52.28.45.137 | remittanceaccounting.ecosire.com | Financial services | Bitnami Odoo 19 Enterprise | LIVE |
| 7 | Quicken/Soovah | 5.78.207.108 | odoo.soovah.com | Wayfair connector | Docker Odoo 19 | LIVE |
| 8 | Hetzner Main | 23.88.13.113 | (pending) | Multi-client prod box | Hetzner EX44, Ubuntu 24.04.3 | PROVISIONED |

---

## Server 1: ECOSIRE.COM (13.223.116.181)

### Connection
```bash
powershell.exe ssh -i "C:\path\to\ecosire.pem" ubuntu@13.223.116.181
```

### Architecture: Hybrid (Docker + PM2 + Nginx)

**Docker Services:**

| Container | Port | Purpose |
|-----------|------|---------|
| PostgreSQL 17 | 5432 | Primary database |
| Redis 7 | 6379 | Caching |
| Authentik Server | 9000 | OIDC Authentication |
| Authentik Worker | — | Background auth tasks |
| Uptime Kuma | 3005 | Status monitoring |
| Glitchtip | — | Error tracking |

**PM2 Processes:**

| App | Port | Framework |
|-----|------|-----------|
| ecosire-web | 3000 | Next.js 16 |
| ecosire-api | 3001 | NestJS 11 |
| ecosire-docs | 3002 | Docusaurus |
| muhammadamir-web | 3020 | Next.js 16 |
| odovation-web | 3010 | Next.js 16 |

**Domain → Backend Mapping:**

| Domain | Backend Port |
|--------|-------------|
| ecosire.com | 3000 |
| api.ecosire.com | 3001 |
| docs.ecosire.com | 3002 |
| odovation.com | 3010 |
| muhammadamir.com | 3020 |
| auth.ecosire.com | 9000 |
| status.ecosire.com | 3005 |

**Key Paths:**
- Deploy: `/opt/ecosire/app/`
- Infrastructure: `/opt/ecosire/infrastructure/`
- Docker env: `/opt/ecosire/infrastructure/.env`
- App env: `/opt/ecosire/app/.env.local`
- SSL: `/etc/ssl/cloudflare/ecosire.com.{pem,key}`
- Nginx: `/etc/nginx/sites-available/ecosire.conf`

**Deployment (Quick):**
```bash
powershell.exe ssh -i ecosire.pem ubuntu@13.223.116.181 "cd /opt/ecosire/app && git pull && pnpm install && pnpm build && pm2 restart all --update-env"
```

**Important Notes:**
- `NEXT_PUBLIC_*` vars inlined at build time — must rebuild on change
- `pm2 restart all --update-env` if env vars changed
- www redirect via Cloudflare (never Nginx)
- SSH user is `ubuntu@` (NOT `ecosire@`)

---

## Server 2: ECOSIRE.AI (54.197.92.23)

### Connection
```bash
ssh -i "D:\Ecosire Customer Service\Active Clients\Ai Content Automation\aicontentgeneration.pem" ubuntu@3.28.95.67
```

### Architecture: Full Docker (11 Containers)

| Container | Image | Port |
|-----------|-------|------|
| ace-postgres | postgres:16-alpine | 5432 |
| ace-redis | redis:7-alpine | 6379 |
| ace-api | custom (FastAPI) | 8000 |
| ace-celery-worker | custom | — |
| ace-celery-beat | custom | — |
| ace-dashboard | custom (Next.js) | 3000 |
| ace-nginx | nginx:1.27-alpine | 80, 443 |
| ace-authentik | authentik:2024.12.3 | 9000 |
| ace-authentik-worker | authentik:2024.12.3 | — |
| ace-authentik-db | postgres:16-alpine | 5432 |
| ace-authentik-redis | redis:7-alpine | 6379 |

---

## Server 3: Sahara Properties (3.232.201.222)

### Connection
```bash
ssh -i "sahara.pem" bitnami@3.232.201.222
```

### Architecture: Bitnami Odoo 19
- Stack: Bitnami Odoo 19, 8 GB RAM, 60 GB disk
- SSL: Let's Encrypt (auto-renew)
- Enterprise Modules: 1,423
- Custom addons: `/bitnami/odoo/addons`
- Module: property_management_system v19.0.3.10.0

---

## Server 4: Obliq + Oenoteca (52.206.177.34) — SHARED

> **CRITICAL WARNING**: Oenoteca and Obliq SHARE this server. Any operation on one can affect the other.

- **Obliq DB**: obliq_furniture
- **Oenoteca DB**: oenoteca
- Stack: Bitnami Odoo 19

---

## Server 5: Demo Server (37.27.2.10) — Hetzner CPX42

### Connection
```bash
ssh -i ~/.ssh/hetzner_demo root@37.27.2.10
```

Key persisted (2026-04-18) in three synced locations (all 600 perms, ed25519, SHA256 of pub in `authorized_keys`):
- **Primary:** `~/.ssh/hetzner_demo` (Windows path: `C:\Users\Amir Nazir\.ssh\hetzner_demo`)
- **Legacy path** (older scripts/memory reference it): `D:\tmp\hetzner_demo`
- **Dropbox backup** (auto-syncs across devices): `C:\Users\Amir Nazir\Dropbox\Keys\SSH Keys\hetzner_demo`
- **Master copy:** 1Password vault `IXX3GXRTJ5BIFB66JCVPGF2BLE`, item `cbsjap2gfvfx4m54vzya56nulq`

See [[Demo Server SSH Key]] for the raw key material (private — contained in this vault under Credentials & Access).

### Architecture
- **Hardware**: Hetzner CPX42 (8 vCPU / 16 GB RAM / 320 GB NVMe, Ubuntu 24.04)
- **Hostname**: `ubuntu-16gb-hel1-4`
- **Domain**: demo.ecosire.com (wildcard `*.demo.ecosire.com` → same IP)
- **Databases**: 202 (201 store modules + powerbi_connector + noon_demo running v19.0.2.0.0 premium as of 2026-04-18)
- **Architecture**: Subdomain-per-platform, `dbfilter=^%d_demo$`
- **Addons path** (actual, per config): `/opt/odoo19/addons,/opt/ecosire/enterprise,/opt/ecosire/addons` — NOT `/opt/odoo/extra-addons/` as older docs claimed
- **Odoo binary**: `/opt/odoo19/odoo-bin` with **system python3.12** (NO venv — `#!/usr/bin/env python3`)
- **Config**: `/etc/odoo-demo.conf` (admin_passwd `ecosire_admin_2026`, db_user `odoo`, db_password `ecosire_demo_2026`)
- **Lead Capture**: SQLite + CRM sync via `/opt/ecosire/lead_api.py` (systemd `ecosire-leads`)
- **PostgreSQL**: Tuned for 202 concurrent DBs
- **Service**: `systemctl restart odoo-demo`

### Module upgrade flow (bare-copy addons — no .git)
```bash
# 1. Clone fresh from GitHub
cd /tmp && rm -rf pull_<module>
GIT_SSH_COMMAND='ssh -i ~/.ssh/id_ed25519' \
  git clone --depth 1 --branch <target_branch> \
  git@github.com:ecosire/<module>.git pull_<module>

# 2. Back up the current copy OUTSIDE addons path (critical — Odoo chokes on dotted dir names inside addons_path)
mv /opt/ecosire/addons/<module> /root/<module>.bak_$(date +%Y%m%d_%H%M%S)

# 3. Copy new version in + fix ownership
cp -r pull_<module>/<module> /opt/ecosire/addons/<module>
chown -R odoo:odoo /opt/ecosire/addons/<module>

# 4. Stop service, upgrade DB schema, restart
systemctl stop odoo-demo
sudo -u odoo /opt/odoo19/odoo-bin -c /etc/odoo-demo.conf -d <db_name> \
  -u <module> --stop-after-init --no-http --workers=0 \
  --logfile=/tmp/upgrade.log --log-level=info
systemctl start odoo-demo
```

### Verified 2026-04-18 (noon_store_management v19.0.2.0.0 deploy)
- Upgrade path above works end-to-end; noon_demo upgraded 19.0.1.0.0 → 19.0.2.0.0, 29 → 46 models, full demo data seeded.
- GitHub deploy key "Demo Server Helsinki" on server's `~/.ssh/id_ed25519` authorized for `ecosire/*` org repos.
- `dbfilter` routes `noon.demo.ecosire.com` to `noon_demo` automatically.

---

## Server 6: Remittance (52.28.45.137)

### Connection
```bash
ssh -i remittance.pem bitnami@52.28.45.137
```

### Architecture: Bitnami Odoo 19 Enterprise
- **Domain**: remittanceaccounting.ecosire.com + remtest.ecosire.com
- **Module**: remittance_management v19.0.5.0.2
- **Enterprise**: private_enterprise installed
- **SSL**: Configured
- **Workers**: Must have `workers >= 1` explicitly (Bitnami gotcha)
- **wkhtmltopdf**: 0.12.6.1-3.bookworm_amd64 installed from GitHub releases

---

## Server 7: Quicken/Soovah (5.78.207.108)

- **Domain**: odoo.soovah.com
- **Stack**: Docker Odoo 19
- **Module**: wayfair_store_management v3.7.0
- **Data**: 2,110 orders, payout reconciled
- **Known Issue**: Docker resets web.base.url to localhost — MUST fix+freeze for license

---

## Server 8: Hetzner Main (23.88.13.113) — PROVISIONED

### Purpose: Multi-client production box (future)
- **Hardware**: Hetzner EX44 (Falkenstein, Germany)
- **OS**: Ubuntu 24.04.3
- **SSH**: `ssh -i ~/.ssh/ecosire_main root@23.88.13.113` (ed25519 key)
- **Status**: PROVISIONED — root SSH verified, host keys pinned
- **Planned Use**: Alvi Dental migration (v18→v19), future client consolidation
- **Note**: demo.ecosire.com (CPX42, 201 DBs) NEVER consolidates with paying-client production box

---

## Authentik Auth Servers

| Server | URL | Admin | Used By |
|--------|-----|-------|---------|
| ECOSIRE.COM | https://auth.ecosire.com | akadmin | Main platform |
| ECOSIRE.AI | https://auth.ecosire.ai | akadmin | AI Content Engine |

---

## SSL Certificates

| Server | Provider | Type | Expiry |
|--------|----------|------|--------|
| ECOSIRE.COM | Cloudflare | Origin Certificate | Long-lived |
| AI Content | Cloudflare | Origin Certificate | 2041 |
| Sahara Properties | Let's Encrypt | Auto-renew | Rolling |
| Remittance | Let's Encrypt | Auto-renew | Rolling |

---

## Related

- [[ECOSIRE.COM Platform]] — Main platform details
- [[Ecosire - Client Portfolio]] — Client inventory
- [[MOC - Credentials & Access]] — Credentials index
