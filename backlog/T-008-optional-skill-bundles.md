# T-008 — Optional skill bundles

- **Summary:** let the user name third-party skill bundles at install; `workspace-sync` installs them and offers each as a workflow beside `dynamic-workflow`
- **Category:** feature
- **Importance:** medium
- **Effort:** M
- **Depends on:** none — the core no longer calls any third-party skill

## Why

The workspace ships one opinionated workflow set. Lighter or different bundles
(`mattpocock/skills`, `addyosmani/agent-skills`, `obra/superpowers`, picks from
`ComposioHQ/awesome-claude-skills`) should be a choice per user, without the core depending on any.

## What

- **Manifest, user-owned:** `~/.agents/BUNDLES.md` — per bundle: source, ref, the skills taken
  (a curated list is a pick of single skills, not a whole repo), entry-point skill + one-line *when*.
  `INSTALL.md` asks once; `workspace-sync` installs and updates from it.
- **Plain skill folders into `~/.agents/skills/`**, linked like every other skill. A bundle that
  ships only as a plugin with hooks (superpowers' SessionStart bootstrap) is flagged before install:
  always-loaded injection fights the workflow gate.
- **One-way dependency:** workspace skills never invoke a bundle skill. Bundles are peers, picked
  only at the workflow gate — one row each in the global `AGENTS.md` via a `{bundle workflows}`
  placeholder the sync fills.
- **Name collisions** (`plan`, `tdd`, `code-review`, …) are detected and asked about, never
  overwritten silently.
- `README.md` requirements and install section name the option.
