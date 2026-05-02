---
name: Odoo's "Currency: rate update" cron silently stops firing — kick it manually after server migrations or long downtime
description: When `ir_cron.nextcall` falls far in the past, the rate-update cron does NOT auto-catch-up. It will only fire on the schedule from the next time it's manually triggered or `nextcall` is reset. Result: `res.currency.rate` goes stale, `_convert()` returns 0 or fallback values, FX-aware features break silently.
type: feedback
updated: 2026-05-02
originSessionId: 2026_05_02h_remittance_v34_fx_ux
---

## The rule

When deploying multi-currency features that depend on `res.currency.rate`, **always check the freshness of `Currency: rate update` cron and kick it manually if `nextcall` is older than a few days**. Odoo doesn't run "missed" cron jobs on resume — it just runs once and re-schedules from "now".

```sql
SELECT c.id, c.cron_name, c.active, c.nextcall::text, a.code AS action_code
  FROM ir_cron c
  JOIN ir_act_server a ON a.id = c.ir_actions_server_id
 WHERE a.code ILIKE '%currency%' OR c.cron_name ILIKE '%currency%';
```

If `nextcall` is more than ~24 hours old AND `active=true`, it's stuck.

## Why

Odoo's cron scheduler picks up jobs where `nextcall <= NOW()` AND `active=true`. When the cron fires, it advances `nextcall` to the next scheduled time (per `interval_number` + `interval_type`). But if the cron didn't fire for some reason (server downtime, registry crash on startup, paused workers, exception during execution), `nextcall` doesn't advance — it stays in the past.

When the server resumes / cron is re-enabled, the scheduler runs the cron ONCE (catching up on the missed past slot), then advances `nextcall` to "now + interval". So you lose all the missed runs in between.

For currency rates, this means:
- `res.currency.rate` only has rates for dates when the cron actually ran
- `_convert(amount, target, company, date)` falls back to the most recent rate ≤ date
- If the most recent rate is weeks old and the user hits a feature that asks for "today's spot", they get a stale value
- If `res.currency.rate` is empty entirely, `_convert` returns 0 or the source amount unchanged

## Real incident — 2026-05-02 Remittance v10.0.34

User added `fx_spot_hint` computed field to surface today's spot rate inline on the form. After deploy, the audit migration logged:

```
res.currency.rate freshness (≤ today):
    - AED: 0 rates total, latest = NONE
    - EUR: 0 rates total, latest = NONE
    - USD: 0 rates total, latest = NONE
```

Yet `res.company.currency_provider` was correctly set per company (CBUAE for Daleel, ECB for OÜ entities). The cron was just stuck:

```
 id |       cron_name       | active |   nextcall          
----+-----------------------+--------+---------------------
 20 | Currency: rate update | t      | 2026-04-17 13:58:11
```

`nextcall` was ~2 weeks in the past. Cron hadn't fired since.

**Manual kick** via `odoo-bin shell`:

```python
companies = env['res.company'].search([])
for c in companies:
    if c.currency_provider and c.currency_provider != 'none':
        result = c.update_currency_rates()
        print(f'  {c.name} ({c.currency_provider}): {result}')
env.cr.commit()
```

Output:
```
Stock Lot OÜ (ecb): True
Ventrax Advisory OÜ (ecb): True
Daleel General Trading LLC (cbuae): True
```

After: 3 rates each for EUR/USD/AED, latest 2026-05-01.

**Reset cron schedule:**
```sql
UPDATE ir_cron SET nextcall = NOW() + INTERVAL '1 day'
 WHERE cron_name = 'Currency: rate update';
```

Now fires daily going forward.

## Detection signal

- New FX-aware feature ships fine in dev but spot rates show as 0 / NaN / source-amount-unchanged on prod
- `res.currency.rate` table empty or has only old rows
- `ir_cron.nextcall` for `Currency: rate update` more than a few days old
- `res.company.currency_provider` is set but rates aren't refreshing

## Fix sequence

1. Check `currency_provider` is set per company (Settings → Multi-Currencies → Automatic Currency Rates).
2. Trigger immediately via `odoo-bin shell`: `env['res.company'].search([])._run_currency_provider()` OR `companies.update_currency_rates()`.
3. Reset `ir_cron.nextcall` to NOW + interval so it fires on the regular schedule going forward.

## Reusable across

Any Odoo workspace using multi-currency features that depend on live res.currency.rate values:
- Sales/Purchase orders in foreign currency
- Multi-company consolidations
- Bank reconciliation across currencies
- Foreign currency invoicing
- ANY remittance / FX dealer module
- Manufacturing with foreign-sourced components

Especially relevant after: server migrations, OS reboots that lose cron state, registry-init crashes on startup, multi-week server downtime.

## Bonus tip — make the cron self-healing

For modules that critically depend on rate freshness, write a startup hook that checks `ir_cron.nextcall` for the rate-update cron, and if it's >2 days old, kicks it once + resets. Captures the manual fix as automatic recovery.
