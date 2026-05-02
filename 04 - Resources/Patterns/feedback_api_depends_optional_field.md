---
name: api.depends path into optional / not-installed field crashes lazily on first write
description: HARD RULE — @api.depends('rel.field_provided_by_optional_module') causes ValueError "Wrong @depends" on first write to ANY model in the database, even when compute body has runtime hasattr-style guards. Found via shoppingfeed_demo login 500.
type: feedback
client: portfolio
updated: 2026-05-02
originSessionId: a910aa3c-fa39-48a3-be41-eabdfa882f1e
---
## The rule

Never put a related-field path into `@api.depends` whose terminal field is provided by an OPTIONAL module that may not be installed. Even when the compute body defensively checks `if 'optional_field' in product._fields`, the decorator's path is parsed at trigger-tree resolution time and crashes:

```
ValueError: Wrong @depends on '_compute_X' (compute method of field <model>.<field>).
            Dependency field '<missing>' not found in model <related>.
```

## Why

Odoo's `Registry._field_triggers` is registry-level and **lazy-loaded on first model write** (any model — not just the one with the broken compute). The trigger tree builder resolves every stored compute's `depends` to find what to recompute on writes. When it hits a path whose final field doesn't exist on the related model, it raises and refuses to build the tree.

That single broken depends takes down the **entire database** for any write — including `res.users.log` creation during login, which is why the surface symptom is "login throws HTTP 500".

## How to apply

1. **Audit at module-developer time.** For every `@api.depends('rel_field.X', ...)`, confirm `X` is on `rel_field`'s model in your manifest's `depends` set, not in some optional sister module.
2. **If the field is genuinely optional** (e.g., `product_brand_id` from the OCA `product_brand` app, MRP-only fields, sale-coupon-only fields, etc.), pick one of:
   - **(A) Drop it from `@api.depends`** — accept that the score won't auto-recompute on that field's change. Usually fine for indicators driven by nightly cron. *Used 2026-05-02 on `shoppingfeed_store_management/models/shoppingfeed_feed_quality.py`.*
   - **(B) Add the optional module to manifest `depends`** — pulls in a hard dependency portfolio-wide, heavier but most correct.
   - **(C) Dynamic depends** — override `_setup_complete` / register depends conditionally on `'X' in self.env['related']._fields` at registry build time. More complex but matches the runtime guard's intent.
3. **Keep the runtime guard either way.** The decorator and the body have to be consistent:
   ```python
   @api.depends('odoo_product_id', 'odoo_product_id.barcode', ...)
   def _compute_X(self):
       for rec in self:
           product = rec.odoo_product_id
           if product and 'product_brand_id' in product._fields:
               brand = product.product_brand_id
               ...
   ```

## Detection symptoms

- `POST /web/login` returns **HTTP 500** even though `GET /web/login` is HTTP 200
- Server log shows `ValueError: Wrong @depends on ... Dependency field 'X' not found in model Y`
- Stack trace passes through `Registry.get_trigger_tree` → `Registry._field_triggers` → `field.resolve_depends`
- Reproducible by ANY write to ANY model, not just the model with the broken compute

## Fleet-wide audit query

```bash
# find all @api.depends referencing fields outside manifest depends — needs per-module review
grep -rn "@api.depends" /opt/ecosire/addons/*/models/ | \
  grep -oE "'[a-z_]+\.[a-z_]+'" | sort -u
```

## Sister gotchas

- [[feedback_module_pump_static_validation_insufficient]] — `py_compile` + `ET.parse` + `ast.literal_eval(manifest)` doesn't catch this. Only `odoo-bin -i <module> --stop-after-init` catches it (and then only if you trigger a write during the install). 6 bug categories that pass static but crash registry.
- Earlier 2026-05-02c Wave N hotfix wave (`feedback_module_pump_static_validation_insufficient`) added "decorator signature drift" + "_inherit field refs to non-existent base fields" — this rule extends those with the **lazy first-write trigger** angle.

## Reusable in

ANY Odoo workspace where modules cross-reference fields from other modules (Ecosire portfolio, Obliq, Suleman Remittance, Oenoteca, Sahara). Especially relevant when the module declares "optionally enrich if this other app is installed" features.
