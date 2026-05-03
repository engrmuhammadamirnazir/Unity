---
type: pattern
tags: [pattern, i18n, translation, brand-names, ecosire-com, ecosire-ai, content-quality]
aliases: [Brand-name translation audit, Drizzle translation gotcha]
created: 2026-05-04
updated: 2026-05-04
applies-to: [ECOSIRE.COM, ECOSIRE.AI, ECOSIRE.IO, brand sites, any client multilingual content]
---

# Audit brand-name translations after Google Translate

After running any Google-Translate-based pipeline (`apps/web/scripts/translate-missing.mjs` or equivalent), audit non-EN locale files for brand-name mistranslations before considering the translation step "done."

## Why

Google Translate has no concept of brand-name proper nouns when the brand is also a common English verb or noun.

**Observed 2026-05-04 (ECOSIRE.COM SEO push):** "Drizzle ORM" became:
- es: "Rocíe ORM" (sprinkle/spray)
- de: "ORM beträufeln" (drizzle/drip on)
- pt: "Regue Drizzle ORM" (water/sprinkle)
- tr: "Drizzle ORM'yi dağıtın" (deploy/distribute)

Even when Google preserves the brand once, it can still also insert the wrong verb. Hand-correction needed in 4-of-8 auto-translated locales.

## Brands at risk

Any brand that is also a common word in some target language:

| Brand | Risk language(s) | Wrong-form examples |
|-------|------------------|---------------------|
| Drizzle (ORM) | es, de, pt, tr | rocíe, beträufeln, regue |
| Wave (accounting) | es, fr, de | ola, vague, Welle |
| Tally (ERP) | es, fr, de | cuenta, décompte, Zählung |
| Drift (chat) | es, de | deriva, Drift→treiben |
| Notion | es | noción |
| Forge / Anchor / Bolt / Stripe / Block | varies | varies |
| Vault | es, fr, de | bóveda, voûte |
| Harvest / Pulse / Slack | varies | varies |

**Heuristic:** if the brand is < 7 letters and is also in a generic English dictionary, audit it.

## How to apply

1. **Maintain a brand allow-list** — strings to NEVER translate, or to revert if translated. Ideally `translate-missing.mjs` learns to wrap brands in a non-translatable marker (e.g. `<x>Drizzle</x>`) before sending to Google.
2. **After translate, grep each non-EN file** for `posts.<slug-with-brand-substring>.title` and confirm the brand name still appears literally (case-insensitive).
3. **For X-vs-Y comparison slugs**, brands appear in BOTH the slug AND the title — use a regex sweep that compares EN slug brand tokens vs translated title tokens.
4. **When mistranslated**, hand-correct title + description together. Don't try to regex-replace blindly — Google often inserts the brand AND keeps the wrong verb (you also need to remove the verb).
5. **Run after EVERY auto-translate**, not just when first noticed. New articles introduce new brand names every push.

## Workspaces this applies to

- **ECOSIRE.COM** — primary case study (1,197 posts × 11 locales)
- **ECOSIRE.AI** — translates content briefs / drafts
- **ECOSIRE.IO** — multilingual marketing
- **Brand sites** (odovation, muhammadamir) — if brands are introduced there
- **Client sites** with multilingual content — same risk surface

## Cross-references

- [[feedback_translator_breaks_icu_placeholders]] — sister problem with `{var}` placeholders being translated
- [[feedback_propagate_learning_not_artifacts]] — why this pattern goes to Unity but the workspace's translate-missing.mjs script does not
- [[PATTERNS - INDEX]]
