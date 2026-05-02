---
name: Always run odoo-bin upgrades as `sudo -u odoo`, NEVER `sudo -u daemon`
description: On Bitnami Odoo deployments, the runtime workers run as the `odoo` user but the legacy bitnami init scripts use `daemon`. Running `odoo-bin -u <module>` as `daemon` creates new files (compiled assets, attachment filestore writes) owned by `daemon`, which the runtime `odoo` workers then can't read or overwrite. Symptom: HTTPS asset bundle requests return 500 with PermissionError on filestore paths.
type: feedback
updated: 2026-05-02
originSessionId: 2026_05_02_remittance_v10_0_30
---

## The rule

When running `odoo-bin <subcommand>` on a Bitnami Odoo server, **always** use `sudo -u odoo`, never `sudo -u daemon`:

```bash
# CORRECT
sudo -u odoo LD_LIBRARY_PATH=/opt/bitnami/postgresql/lib \
  /opt/bitnami/odoo/venv/bin/python /opt/bitnami/odoo/bin/odoo-bin \
  -c /opt/bitnami/odoo/conf/odoo.conf -d <dbname> -u <module> \
  --stop-after-init --no-http

# WRONG — creates files owned by daemon, breaks asset bundles for workers
sudo -u daemon LD_LIBRARY_PATH=/opt/bitnami/postgresql/lib \
  /opt/bitnami/odoo/venv/bin/python /opt/bitnami/odoo/bin/odoo-bin \
  ...
```

**Why:** Bitnami's `/opt/bitnami/ctlscript.sh` runs `odoo` daemon as the `odoo` user (verified via `ps -ef | grep "/opt/bitnami/odoo/venv/bin/python"`). Old Bitnami docs and some auto-generated guides reference `daemon`, but that's stale — current installs use `odoo`. Asset bundle compilation, attachment filestore writes, and any new file operations during upgrade need to be owned by the same user the workers run as.

**How to apply:**

1. **Default to `sudo -u odoo`** for any `odoo-bin -u`, `odoo-bin -i`, `odoo-bin shell` invocation on Bitnami servers.
2. **Verify worker user** before deploying: `ps -ef | grep "/opt/bitnami/odoo/venv/bin/python" | grep -v grep | head -1` — the leftmost non-root user is what owns the runtime.
3. **If you accidentally ran as daemon**, fix the filestore ownership:
   ```bash
   sudo chown -R odoo:odoo /bitnami/odoo/data/filestore/<dbname>/
   ```
   Then restart Odoo. Workers will regenerate any newly-created daemon-owned asset bundles.
4. **Symptom to recognise**: HTTPS requests return 500 on `/web/assets/<hash>/web.assets_web.min.css` etc. with `PermissionError: [Errno 13] Permission denied: '/bitnami/odoo/data/filestore/<dbname>/<XX>/<hash>'` in the Odoo log. The site loads HTML but is unstyled and crashes JS.

**Real incident (2026-05-02 Suleman remittance v10.0.28 → v10.0.29):**

While deploying v10.0.28 (the first prod deploy after the recent surge in iteration), I ran `odoo-bin -u remittance_management` as `sudo -u daemon` (copied from old runbook). The upgrade succeeded (DB write privileges via PG were fine). But the new compiled asset bundles for `web.assets_web.min.css` got written to the filestore as `daemon:daemon`. After restarting Odoo, workers (running as `odoo`) couldn't write the next asset hash for the next request → returned 500. User saw the prod site `https://remittanceaccounting.ecosire.com/` showing unstyled HTML with 500s on every CSS/JS resource. remtest worked because it used a different filestore that was still odoo-owned.

Detection: tailed `/opt/bitnami/odoo/log/odoo-server.log` and saw repeated `PermissionError: [Errno 13] Permission denied: '/bitnami/odoo/data/filestore/remittanceaccounting/...`.

Fix: chowned the filestore back to `odoo:odoo` and re-ran the v10.0.30 upgrade as `sudo -u odoo`. Both DBs went HTTP 200 and the issue stayed fixed.

**Reusable across:** any Bitnami Odoo deployment (Suleman, Obliq, Oenoteca if migrated, Sahara if migrated). Non-Bitnami Docker/Hetzner deployments may have different conventions — check the worker user explicitly there too.

**Sister rule (graceful shell cleanup):** When chaining background `nohup ... &` jobs across SSH for upgrade orchestration, the SSH session can drop on long upgrades (5+ min), leaving server-side `while pgrep ...; do sleep N; done` loops orphaned. Always include a cleanup pass at end-of-session: `kill -TERM` the orchestrator chain PIDs, leave the children to be reaped by init. Never use `kill -9` unless TERM has failed twice — graceful is cheaper.
