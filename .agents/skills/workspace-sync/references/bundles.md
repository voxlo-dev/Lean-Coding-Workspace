# Skill bundle catalog

Offered by `workspace-sync` step 5, one row per entry. **Maintained by hand:** adding a bundle is
adding a row.

- **kind:** `workflow` gets a row in the workflow gate · `skills` / `agents` are helpers, discovered
  by their own descriptions · `plugin` installs through a target's plugin mechanism, where it has one
- **take:** exactly what gets installed, relative to the source repo; everything else stays behind
- **needs:** tooling to install once per machine · `per project:` setup the bundle's entry skill runs
  and the folder it keeps in a repo

| id | source | kind | take | needs | entry | pick when |
| --- | --- | --- | --- | --- | --- | --- |
| superpowers | https://github.com/obra/superpowers | workflow | `skills/*` minus `using-superpowers`, `diagnosing-superpowers` | per project: `docs/superpowers/` | `brainstorming` | strict brainstorm → plan → subagent TDD, heavyweight |
| mattpocock | https://github.com/mattpocock/skills | workflow | `skills/{engineering,productivity}/*`, flattened | per project: `setup-matt-pocock-skills` first — writes an `AGENTS.md` section, `docs/agents/`, its own issue convention | `grill-with-docs` | small composable skills: grill → spec → tickets |
| addy-skills | https://github.com/addyosmani/agent-skills | workflow | `skills/*` minus `using-agent-skills`; each `../../references/{file}` a skill cites → copied into that skill's `references/`, path rewritten to `references/{file}` | — | `spec-driven-development` | full lifecycle with web, perf and security checklists |
| openspec | https://github.com/Fission-AI/OpenSpec | workflow | `skills/openspec-{explore,propose,apply-change,update-change,sync-specs,archive-change}` | `npm i -g @fission-ai/openspec` · per project: `openspec init` → `openspec/` | `openspec-propose` | brownfield changes with living specs: propose → apply → archive |
| bmad | https://github.com/bmad-code-org/BMAD-METHOD | workflow | `skills/*` — ~32 long descriptions, the heaviest entry | `uv` · per project: `bmad setup` → `_bmad/`, `_bmad-output/`; point its `project_knowledge` away from `docs/` | `bmad` | greenfield product work: PRD, architecture, epics, persona roles |
| feature-dev | https://github.com/anthropics/claude-plugins-official | plugin | plugin `feature-dev` | — | `/feature-dev` | guided explore → architect → build → review |
| anthropic-skills | https://github.com/anthropics/skills | skills | `skills/{webapp-testing,mcp-builder,skill-creator}` | — | — | web-app testing, MCP servers, skill authoring |
| frontend-design | https://github.com/anthropics/claude-plugins-official | skills | `plugins/frontend-design/skills/frontend-design` | — | — | distinctive UI craft; `ui-design` uses it where installed |
| agency-agents | https://github.com/msitarzewski/agency-agents | agents | `engineering/*`, `testing/*`, converted by its `scripts/install.sh --tool {target}` | — | — | specialist persona subagents for review and testing |

Rejected — re-check before re-adding: `affaan-m/ECC` (hooks on six lifecycle events, always-loaded
rules, its own `e2e-runner`) · `github/spec-kit` (no skill folders, CLI-generated; its `.specify/`
constitution competes with `AGENTS.md`) · `ComposioHQ/awesome-claude-skills` (link list, no
license) · `shanraisshan/claude-code-best-practice` (a guide) · `Shubhamsaboo/awesome-llm-apps`
(tutorials).
