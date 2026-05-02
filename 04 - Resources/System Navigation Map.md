---
type: resource
tags: [system, navigation, development, infrastructure, ai-agent, reference]
created: 2026-03-02
updated: 2026-05-02
---

# System Navigation Map — Complete Laptop File & Software Organization

> Master reference for AI agents to locate any file, project, repo, or tool on this system.
> Machine: Windows 11 Pro, HP laptop. Owner: Muhammad Amir Nazir.

---

## Drive Layout

| Drive | Type | Total | Free | Purpose |
|-------|------|-------|------|---------|
| **C:** | NTFS | 200 GB | 65 GB | OS, user profile, Dropbox, tools (post 2026-05-02 cleanup; was 3.66 GB free pre-cleanup) |
| **D:** | NTFS | 275 GB | 80 GB | Development, projects, modules, customer data, dev-tool caches (`D:\caches\`), Docker disk image (`D:\Software\Docker\`) |

---

## 1. C: Drive — OS & User Profile

### Key User Directories

```
C:\Users\Amir Nazir\
├── Desktop\                          Empty (clean desktop)
├── Documents\                        GreenHill Timbers docs, PowerShell profiles
├── Downloads\                        Installers, PDFs, invoices, proposals
├── Dropbox\                          SYNCED — business docs, vault, credentials
│   ├── Unity\                        THIS VAULT (git-tracked) — renamed from "Muhammad Amir Obsidian Vault" 2026-04-22
│   ├── ECOSIRE (PRIVATE) LIMITED\    Brand assets, SECP docs, letterhead
│   ├── ONETECA FINE WINES\           Client accounting data, bank statements
│   ├── Customers Data\               Client-specific data by name
│   ├── Commands\                     SSH, Git, server password cheatsheets
│   ├── Keys\                         SSH/PEM keys
│   ├── Software\                     PuTTY, PuTTYgen
│   ├── Muhammad Amir\                Personal docs, certifications, ID
│   ├── FBR Returns\                  Tax returns
│   └── Medical Reports\              Health records
├── OneDrive\                         Microsoft OneDrive (minimal use)
├── .openclaw\                        OpenClaw AI agent workspace
├── .claude\                          Claude Code config, project memories
└── Apple\                            Apple device sync
                                      (.cursor/.kiro/.trae/.windsurf/.antigravity/.codeium all REMOVED 2026-05-02 — user uses VS Code only)
```

### Sensitive Files (DO NOT output values)

| File | Location |
|------|----------|
| Server passwords | `Dropbox/Commands/server admins passwords.txt` |
| Customer passwords | `Dropbox/Commands/Customer Passwords.txt` |
| PAT tokens | `Dropbox/Commands/PAT.txt` |
| Enterprise codes | `Dropbox/Commands/Enterprise Codes.txt` |
| SSH keys | `Dropbox/Keys/` |
| Vault credentials | `03 - Areas/Credentials & Access/` (6 files) |

### Dev tool cache locations (relocated 2026-05-02 to keep C: from filling)

| Tool | Cache/Store path | Mechanism |
|------|------------------|-----------|
| **npm** | `D:\caches\npm` | global `~/.npmrc` (`npm config get cache` to verify) |
| **Playwright** | `D:\caches\ms-playwright` | `PLAYWRIGHT_BROWSERS_PATH` user env var |
| **pip** | default `%LOCALAPPDATA%\pip` (back on C:) | run `pip cache purge` periodically |
| **pnpm** | not installed (corepack disabled) | if installing, `pnpm config set store-dir D:\caches\pnpm` BEFORE first use |
| **Docker Desktop** | `D:\Software\Docker\DockerDesktopWSL\disk\docker_data.vhdx` (~16 GB) | Set via Docker Desktop Settings → Resources → Advanced → Disk image location |

**Hibernation OFF** (`powercfg -h off` ran 2026-05-02; Fast Startup also disabled as side-effect).

Future Claude sessions in any workspace must respect these paths and avoid creating heavy build artifacts under `C:\Users\Amir Nazir\` unless explicitly needed. See `~/.claude/CLAUDE.md` for the rules section visible in every session.

---

## 2. D: Drive — Development & Projects

### Top Level

```
D:\
├── Development\                      PRIMARY DEVELOPMENT workspace
│   ├── CLAUDE.md                     Dev workspace instructions (654 lines)
│   ├── odoo19\server\                Odoo 19 primary dev (port 8072, db: odoo19)
│   ├── odoo18\server\                Odoo 18 backport target (port 8069)
│   ├── odoo17\server\                Odoo 17 backport target (port 8070)
│   ├── Ecosire Solutions\            Publishing staging (41 module repos)
│   ├── scripts\                      Utility scripts (backporting, publishing, testing, fixes)
│   └── .claude\                      Claude Code memory for this project
│
├── ECOSIRE.COM\                      ECOSIRE.COM platform (Turborepo monorepo)
│   ├── CLAUDE.md                     Platform instructions
│   ├── apps\api\                     NestJS 11 backend (38 modules, port 3001)
│   ├── apps\web\                     Next.js 16 frontend (204 pages, port 3000)
│   ├── apps\docs\                    Docusaurus (port 3002)
│   ├── apps\odovation\               Odovation brand site (port 3010)
│   ├── apps\muhammadamir\            MuhammadAmir brand site (port 3020)
│   └── .claude\                      Claude Code memory
│
├── Ecosire Customer Service\         CLIENT PROJECT FILES
│   ├── Active Clients\               6 active clients
│   │   ├── Ai Content Automation\    ACE platform (FastAPI + Next.js + WordPress)
│   │   ├── Sahara Properties\        Odoo 19 property management
│   │   ├── Alvi Dental\              WooCommerce integration modules
│   │   ├── Dts Connector\            DTS logistics connector
│   │   ├── PSL\                      Premium Sports Lighting modules
│   │   └── UsTelekom\                Custom modules
│   └── Archive\                      17 archived client folders
│
├── OpenClaw\openclaw\                OpenClaw AI platform source (TypeScript)
│
├── Odoo Modules\                     Module archives by version
│   ├── 12\ through 19\              ZIP files per version
│
├── Odoo App Store Covers\            21 PNG cover images for store modules
│
├── R&D\                              Research & development (git: ecosire/marketplaces)
│
├── Software\                         Software installers & tools
│   ├── Installed\                    Direct Print, Telegram, Warp, Windsurf
│   ├── Obsidian\                     Obsidian installer
│   ├── Scripts\                      Utility scripts
│   └── Telegram\                     Telegram portable
│
└── tmp\                              Temp files, openclaw logs
```

---

## 3. Git Repository Map

### Ecosire Store Management Modules (33 repos)

All at `github.com/ecosire/{module_name}` | Branches: `main`, `19.0`, `18.0`, `17.0`
Local path: `D:\Development\Ecosire Solutions\{Display Name}\{module_name}\`

| # | Module | GitHub Repo | Price |
|---|--------|------------|-------|
| 1 | AliExpress Store Management | `ecosire/aliexpress_store_management` | $99 |
| 2 | Allegro Store Management | `ecosire/allegro_store_management` | $149 |
| 3 | Amazon Store Management | `ecosire/amazon_store_management` | $499 |
| 4 | BigCommerce Store Management | `ecosire/bigcommerce_store_management` | $149 |
| 5 | Bol.com Store Management | `ecosire/bolcom_store_management` | $149 |
| 6 | Catch Store Management | `ecosire/catch_store_management` | $99 |
| 7 | Cdiscount Store Management | `ecosire/cdiscount_store_management` | $99 |
| 8 | Coupang Store Management | `ecosire/coupang_store_management` | $99 |
| 9 | Daraz Store Management | `ecosire/daraz_store_management` | $99 |
| 10 | eBay Store Management | `ecosire/ebay_store_management` | $199 |
| 11 | Etsy Store Management | `ecosire/etsy_store_management` | $149 |
| 12 | Flipkart Store Management | `ecosire/flipkart_store_management` | $99 |
| 13 | Fruugo Store Management | `ecosire/fruugo_store_management` | $99 |
| 14 | Kaufland Store Management | `ecosire/kaufland_store_management` | $99 |
| 15 | Lazada Store Management | `ecosire/lazada_store_management` | $99 |
| 16 | Magento Store Management | `ecosire/magento_store_management` | $199 |
| 17 | MercadoLibre Store Management | `ecosire/mercadolibre_store_management` | $99 |
| 18 | Noon Store Management | `ecosire/noon_store_management` | $99 |
| 19 | OpenCart Store Management | `ecosire/opencart_store_management` | $149 |
| 20 | Otto Store Management | `ecosire/otto_store_management` | $399 |
| 21 | Ozon Store Management | `ecosire/ozon_store_management` | $99 |
| 22 | PrestaShop Store Management | `ecosire/prestashop_store_management` | $149 |
| 23 | Rakuten Store Management | `ecosire/rakuten_store_management` | $99 |
| 24 | SHEIN Store Management | `ecosire/shein_store_management` | $149 |
| 25 | Shopee Store Management | `ecosire/shopee_store_management` | $99 |
| 26 | Shopify Store Management | `ecosire/shopify_store_management` | $199 |
| 27 | Temu Store Management | `ecosire/temu_store_management` | $149 |
| 28 | TikTok Store Management | `ecosire/tiktok_store_management` | $149 |
| 29 | Tokopedia Store Management | `ecosire/tokopedia_store_management` | $99 |
| 30 | Walmart Store Management | `ecosire/walmart_store_management` | $149 |
| 31 | Wix Store Management | `ecosire/wix_store_management` | $149 |
| 32 | WooCommerce Store Management | `ecosire/woocommerce_store_management` | $199 |
| 33 | Zalando Store Management | `ecosire/zalando_store_management` | $399 |

### Other Ecosire Repos

| Repo | GitHub | Local Path | Description |
|------|--------|-----------|-------------|
| Ecosire License Client | `ecosire/ecosire_license_client` | `D:\Development\Ecosire Solutions\Ecosire License Client\` | License enforcement for all modules |
| Private Enterprise | `ecosire/private_enterprise` | `D:\Development\Ecosire Solutions\Private Enterprise\` | Odoo Enterprise customizations |
| Branding Management | `ecosire/branding_management` | `D:\Development\Ecosire Solutions\Branding Management\` | White-label branding module |
| Property Management | `ecosire/Property-Management-System` | `D:\Development\Ecosire Solutions\Property Management System\` | Sahara Properties module |
| SaaS Business Mgmt | `ecosire/saas_business_management` | `D:\Development\Ecosire Solutions\SaaS Business management\` | SaaS platform management ($499) |
| ECOSIRE.COM | `ecosire/ecosire.com` | `D:\ECOSIRE.COM\` | Platform monorepo |
| AI Content Automation | `ecosire/ai_content_automation` | `D:\Ecosire Customer Service\Active Clients\Ai Content Automation\` | ACE platform |
| Odoo DTS Connector | `ecosire/odoo_dts_connector` | `D:\Ecosire Customer Service\Archive\Odoo DTS Connector\` | Logistics connector |
| R&D / Marketplaces | `ecosire/marketplaces` | `D:\R&D\` | Research & marketplace experiments |
| OpenClaw (Ecosire fork) | `ecosire/openclaw` | `D:\Development\Ecosire Solutions\OpenClaw\` | Ecosire's OpenClaw Odoo module |
| OpenClaw (upstream) | `openclaw/openclaw` | `D:\OpenClaw\openclaw\` | OpenClaw AI platform source |

### Personal Repos

| Repo | GitHub | Local Path |
|------|--------|-----------|
| Unity (hive brain) | `engrmuhammadamirnazir/Unity` | `C:\Users\Amir Nazir\Dropbox\Unity\` |

---

## 4. CLAUDE.md Files (AI Agent Context)

| File | Location | Purpose |
|------|----------|---------|
| Unity vault CLAUDE.md | `Dropbox\Unity\CLAUDE.md` | Vault structure, security rules, naming conventions |
| Development CLAUDE.md | `D:\Development\CLAUDE.md` | Odoo dev workflow, module structure, scripts, version diffs (654 lines) |
| ECOSIRE.COM CLAUDE.md | `D:\ECOSIRE.COM\CLAUDE.md` | Monorepo architecture, NestJS/Next.js patterns, build rules |
| OpenClaw CLAUDE.md | `D:\OpenClaw\openclaw\CLAUDE.md` | OpenClaw contribution rules (points to AGENTS.md) |
| OpenClaw server-methods | `D:\OpenClaw\openclaw\src\gateway\server-methods\CLAUDE.md` | Gateway API implementation patterns |
| Sahara Properties CLAUDE.md | `D:\Ecosire Customer Service\Active Clients\Sahara Properties\CLAUDE.md` | Server SSH, deployment, Bitnami config |
| ACE CLAUDE.md | `D:\Ecosire Customer Service\Active Clients\Ai Content Automation\ai-content-engine\CLAUDE.md` | ACE platform architecture, tech stack |
| Odoo 19 Claude.md | `D:\Development\odoo19\Claude.md` | Odoo 19 dev rules, ORM patterns, OWL conventions |

---

## 5. Claude Code Project Memories

> **Unified backup:** [[Claude Code Unified Memory]] — All 8 project memories consolidated into one Obsidian note (prevents knowledge loss)

| Project | Memory Path | Key Files |
|---------|------------|-----------|
| **Dropbox (this vault)** | `~\.claude\projects\C--Users-Amir-Nazir-Dropbox\memory\` | MEMORY.md |
| **Home dir** | `~\.claude\projects\C--Users-Amir-Nazir\memory\` | (empty) |
| **Development** | `~\.claude\projects\D--Development\memory\` | MEMORY.md, environment.md, odoo19_reference.md, odoo_version_diffs.md |
| **ECOSIRE.COM** | `~\.claude\projects\D--ECOSIRE-COM\memory\` | MEMORY.md, muhammadamir.md, production-server.md, product-pricing-updates.md |
| **AI Content Automation** | `~\.claude\projects\D--Ecosire-Customer-Service-Active-Clients-Ai-Content-Automation\memory\` | MEMORY.md, agent-runtime.md, auth.md, production.md, scanner.md |
| **Sahara Properties** | `~\.claude\projects\D--Ecosire-Customer-Service-Active-Clients-Sahara-Properties\memory\` | MEMORY.md |
| **AI Content Engine (SaaS)** | `~\.claude\projects\D--Ai-Content-Automation-Engine\memory\` | MEMORY.md |
| **Odoo 19 addons** | `~\.claude\projects\D--Development-odoo19-server-addons\memory\` | MEMORY.md |

---

## 6. OpenClaw AI Agent Workspace

Full details in [[OpenClaw Agent Strategy]]

```
C:\Users\Amir Nazir\.openclaw\
├── openclaw.json                     Main config (agents, channels, skills, gateway)
├── agents\                           Agent session data
├── cron\jobs.json                    Cron job definitions (50+ jobs)
├── credentials\                      Auth credentials
├── workspace\
│   ├── SOUL.md                       Core agent personality
│   ├── MEMORY.md                     Persistent agent memory
│   ├── IDENTITY.md                   Zain Shahzad identity
│   ├── AGENTS.md                     Agent behavior guide
│   ├── HEARTBEAT.md                  Heartbeat config
│   ├── TOOLS.md                      Local tool config (whisper, voice)
│   ├── USER.md                       Amir's contact info
│   ├── skills\                       33 custom skills (SKILL.md each, ~22K lines)
│   ├── agents\                       Sub-agent workspaces (9 agents)
│   │   ├── hamza\                    Hamza Ali (Senior Odoo Developer)
│   │   ├── saad\                     Saad Raza (Business Development Manager)
│   │   ├── hira\                     Hira Nawaz (Community & Content Manager)
│   │   ├── kashif\                   Kashif Omar (Operations Analyst)
│   │   ├── waqar\                    Waqar Ahmed (QA & Security Analyst)
│   │   ├── bilal\                    Bilal Farooq (Accounting & Bookkeeping)
│   │   ├── fatima\                   Fatima Malik (Shopify eCommerce)
│   │   ├── usman\                    Usman Tariq (GoHighLevel CRM)
│   │   └── nadia\                    Nadia Hassan (AI Agent Services)
│   ├── secrets\                      Moltbook API keys, credentials
│   └── memory\                       Daily logs, state files, Moltbook data
└── gateway.cmd                       Gateway start script
```

---

## 7. Installed Software & Dev Tools

### Development Tools

| Tool | Version | Path |
|------|---------|------|
| **Node.js** | v22.18.0 | `C:\Program Files\nodejs\` |
| **npm** | 10.9.3 | (bundled with Node) |
| **pnpm** | 10.28.2 | (global) |
| **Python** | 3.12.2 | `C:\Users\Amir Nazir\AppData\Local\Programs\Python\Python312\` |
| **pip** | 25.3 | (bundled with Python) |
| **Git** | 2.53.0 | `C:\Program Files\Git\` |
| **Docker** | 29.2.0 | `C:\Program Files\Docker\` |
| **PostgreSQL** | 15+ | `C:\Program Files\PostgreSQL\` |
| **Java** | (installed) | `C:\Program Files\Java\` |
| **GitHub CLI** | (installed) | `C:\Program Files\GitHub CLI\` |
| **Claude Code** | (installed) | (global npm) |
| **OpenClaw** | 2026.2.25 | (global npm) |

### Applications

| App | Category | Location |
|-----|----------|----------|
| **Odoo IoT 19** | ERP | `C:\Program Files\Odoo IoT 19.0.20260115\` |
| **FileZilla** | FTP | `C:\Program Files\FileZilla FTP Client\` |
| **WinRAR** | Archive | `C:\Program Files\WinRAR\` |
| **VLC** | Media | `C:\Program Files\VideoLAN\` |
| **OBS Studio** | Recording | `C:\Program Files\obs-studio\` |
| **Bitvise SSH** | SSH | `C:\Program Files (x86)\Bitvise SSH Client\` |
| **MobaXterm** | SSH/Terminal | `C:\Program Files (x86)\Mobatek\` |
| **AnyDesk** | Remote | `C:\Program Files (x86)\AnyDesk\` |
| **PuTTY** | SSH | `D:\Software\putty.exe` |
| **MetaTrader 5** | Trading | `C:\Program Files\MetaTrader 5\` |
| **NordVPN** | VPN | `C:\Program Files\NordVPN\` (+ NordUpdater) |
| **Bitdefender** | Security | `C:\Program Files\Bitdefender Agent\` |
| **Dropbox** | Sync | `C:\Program Files\Dropbox\` |
| **Samsung** | Device | `C:\Program Files\Samsung\` |
| **Windsurf** | IDE | `D:\Software\Installed\Windsurf\` |
| **Warp** | Terminal | `D:\Software\Installed\Warp\` |
| **Telegram** | Messaging | `D:\Software\Installed\Telegram Desktop\` |
| **Direct Print** | Printing | `D:\Software\Installed\Direct Print Client\` |

### Odoo Environments

| Version | Path | Port | Database | PostgreSQL |
|---------|------|------|----------|------------|
| **Odoo 19** | `D:\Development\odoo19\server\` | 8072 | odoo19 | localhost:5432, user: openpg |
| **Odoo 18** | `D:\Development\odoo18\server\` | 8069 | (db manager) | localhost:5432, user: openpg |
| **Odoo 17** | `D:\Development\odoo17\server\` | 8070 | (db manager) | localhost:5432, user: openpg |

---

## 8. Production Servers

| Server | IP | Provider | Purpose | Access |
|--------|----|----------|---------|--------|
| ECOSIRE.COM | 13.223.116.181 | AWS EC2 t3.large | Platform (NestJS + Next.js + Docker) | SSH + PEM |
| ACE (AI Content) | 3.28.95.67 | AWS EC2 t3.large | AI Content Engine (FastAPI + Docker) | SSH + PEM |
| Sahara Properties | 158.252.2.128 | VPS | Bitnami Odoo 19 | SSH + password |

Credentials stored in `03 - Areas/Credentials & Access/Server Access Credentials.md`

---

## 9. Active Client Projects Quick Reference

| Client | Product | Local Path | Git | Server |
|--------|---------|-----------|-----|--------|
| **Oenoteca Fine Wines** | Odoo accounting | `Dropbox\ONETECA FINE WINES\` | — | Client's Odoo instance |
| **Sahara Properties** | Odoo 19 property mgmt | `D:\Ecosire Customer Service\Active Clients\Sahara Properties\` | `ecosire/Property-Management-System` | 158.252.2.128 |
| **AI Content Automation** | FastAPI + WordPress | `D:\Ecosire Customer Service\Active Clients\Ai Content Automation\` | `ecosire/ai_content_automation` | 3.28.95.67 |
| **Alvi Dental** | WooCommerce + Odoo | `D:\Ecosire Customer Service\Active Clients\Alvi Dental\` | — | Client instance |
| **DTS Connector** | Logistics integration | `D:\Ecosire Customer Service\Active Clients\Dts Connector\` | `ecosire/odoo_dts_connector` | — |
| **PSL** | Custom Odoo modules | `D:\Ecosire Customer Service\Active Clients\PSL\` | — | — |
| **UsTelekom** | Custom Odoo modules | `D:\Ecosire Customer Service\Active Clients\UsTelekom\` | — | — |

---

## 10. Ecosire Product Portfolio Summary

| Product | Type | Tech Stack | Revenue Model |
|---------|------|-----------|---------------|
| **33 Store Management Modules** | Odoo App Store | Python/Odoo 19/18/17, OWL JS | One-time $99-$499 per module |
| **SaaS Business Management** | Odoo module | Python/Odoo 19 | $499 one-time |
| **Branding Management** | Odoo module | Python/Odoo 19 | Licensed |
| **Ecosire License Client** | Odoo module | Python/Odoo 19 | License enforcement |
| **Private Enterprise** | Odoo module | Python/Odoo 19 | Enterprise customizations |
| **ECOSIRE.COM Platform** | SaaS | NestJS + Next.js + PostgreSQL | Subscription |
| **AI Content Engine (ACE)** | SaaS | FastAPI + Next.js + WordPress | Client service |
| **OpenClaw Agent Swarm** | AI Agents | OpenClaw + 6 agents | Lead generation (internal) |

---

## Related

- [[OpenClaw Agent Strategy]] — AI agent swarm for lead generation
- [[ECOSIRE.COM Platform]] — SaaS platform details
- [[Ecosire - Odoo Module Library]] — Full module catalog
- [[Ecosire - Production Servers]] — Server infrastructure
- [[Ecosire - Domain Portfolio]] — 13 domains
- [[MOC - Ecosire]] — Company knowledge map
- [[MOC - Command Cheatsheets]] — 22+ dev command references
