# T-009 — Trial the caveman skill

- **Summary:** measure whether caveman's output-brevity skill cuts billed cost without hurting answer quality, then decide on a catalog row
- **Category:** chore
- **Importance:** low
- **Effort:** S
- **Depends on:** none

## Why

The only token saver with independent positive evidence (JetBrains: −8.5% output tokens, quality
flat) and zero infrastructure — MIT skill, no hook, no proxy. Its terse register may not suit the
user's conversations; only a real run shows that. Background: [`token-savers.md`](token-savers.md).

## What

- Install the skill alone from https://github.com/JuliusBrussee/caveman — never its proxy (BSL-1.1,
  rewrites traffic).
- Run the measurement in [`token-savers.md`](token-savers.md#measuring-a-candidate), and judge the
  answers' readability in German and English.
- Worth it → a `skills` row in `.agents/skills/workspace-sync/references/bundles.md`; not → a
  line in `token-savers.md`.
