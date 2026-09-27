# T-010 — Settle the `artefacts/` naming

- **Summary:** decide how the `artefacts/` folder relates to the prose word "artifact", and whether existing projects follow a rename
- **Category:** decision
- **Importance:** low
- **Effort:** S–M, by option
- **Depends on:** none

## Why

The template names the sprint folder `artefacts/` (68 hits, 21 files) but writes "artifact" in
prose (18 hits, unrelated to the folder). Reads like a typo, and several initialised projects
already link into `artefacts/` — at least `docs/decisions.md` and the frozen sprint files.

## Options

- **A — keep `artefacts/`** as a fixed proper name, "artifact" in prose; one line in the project
  `AGENTS.md` doc map. No migration. Side benefit: no confusion with the harness's own Artifacts.
- **B — rename to `artifacts/` and migrate** — template sed, plus `git mv` of sprint folders and
  link rewrites in `decisions.md`, frozen sprint files, open tickets; needs a fixed step in
  `project-init`'s otherwise recipe-free docs migration.
- **C — rename, don't migrate** — new projects get the new name; old ones keep `artefacts/` with a
  note in their `AGENTS.md`, so every skill must tolerate either name or read it from `AGENTS.md`.
- **D — a different name altogether** (e.g. `sprints/`, `runs/`) under B or C — removes the
  spelling question and the Artifact collision; the name should say "per-sprint process history".

## What

Pick one, record it in `docs/` or the template's decisions, apply across the template and README
(and `assets/*.svg`) in one commit per the repo's structural-change rule.
