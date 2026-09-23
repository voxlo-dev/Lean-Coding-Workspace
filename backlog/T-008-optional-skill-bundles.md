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

- **Catalog, repo-owned:** `.agents/skills/workspace-sync/references/bundles.md`, one pipe-table
  row per entry (seed below); install turns its rows into a multi-select question, "Other" takes
  any repo URL. Adding a bundle = adding a row.
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

## Catalog seed

Surveyed 2026-09-23.

| id | repo | kind | install | entry | pick when |
| --- | --- | --- | --- | --- | --- |
| superpowers | obra/superpowers | workflow | `skills/*` minus `using-superpowers`, `diagnosing-superpowers`; no hooks, no plugin | `brainstorming` | strict brainstorm → plan → subagent TDD, heavyweight |
| mattpocock | mattpocock/skills | workflow | `skills/{engineering,productivity}/*`, flattened | `grill-with-docs` (`setup-matt-pocock-skills` first) | small composable skills: grill → spec → tickets |
| addy-skills | addyosmani/agent-skills | workflow | `skills/*` minus `using-agent-skills`, plus `references/`; `agents/*` optional; no commands | `spec-driven-development` | full lifecycle with web, perf and security checklists |
| anthropic-skills | anthropics/skills | helper skills | `skills/{webapp-testing,mcp-builder,skill-creator}` | — | web-app testing, MCP servers, skill authoring |
| frontend-design | anthropics/claude-plugins-official | helper skill | `plugins/frontend-design/skills/frontend-design` | `frontend-design` | distinctive UI craft; `ui-design` uses it where installed |
| feature-dev | anthropics/claude-plugins-official | workflow, one harness | plugin install | `/feature-dev` | explore → architect → build → review |
| agency-agents | msitarzewski/agency-agents | agents | `engineering/*`, `testing/*` via its `scripts/install.sh --tool {target}` | — | specialist persona subagents for review and testing |
| openspec | Fission-AI/OpenSpec | workflow | `skills/openspec-{explore,propose,apply-change,update-change,sync-specs,archive-change}`; needs the npm `openspec` CLI, `openspec init` per project | `openspec-propose` | brownfield changes with living specs: propose → apply → archive |
| bmad | bmad-code-org/BMAD-METHOD | workflow | a core subset of `skills/*` (never `web-bundles/`, `tools/tests/`); needs `uv`, `bmad setup` per project | `bmad` | greenfield product work: PRD, architecture, epics, persona roles |

Excluded: `github/spec-kit` (no skill folders — CLI-generated, `.specify/` constitution competes with `AGENTS.md`) · `affaan-m/ECC` (hooks on six lifecycle events, always-loaded rules, clashes with `e2e-runner`) · `ComposioHQ/awesome-claude-skills` (link list, no license) · `shanraisshan/claude-code-best-practice` (guide) · `Shubhamsaboo/awesome-llm-apps` (tutorials).

Verify at build time: addy-skills' relative `references/` paths after copying · agency-agents' name rewrite (names contain spaces) for OpenCode and Codex · anthropics/skills — Apache-licensed skills only, the document skills are proprietary · mattpocock's setup writes an `AGENTS.md` section and its own issue convention beside `backlog/` — say so in its row · BMAD: pick the core subset from its `bmod-method` record, check `render_skill.py` resolves from `~/.agents/skills/`, point its `project_knowledge` away from `docs/` · openspec and bmad each keep their own project folder (`openspec/`, `_bmad/`) — say so in their rows.
