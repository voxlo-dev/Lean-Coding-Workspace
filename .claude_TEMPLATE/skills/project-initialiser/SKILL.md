---
name: project-initialiser
description: "Use to onboard a new or existing project: explore, detect the domain, scaffold the template, set up docs, install the test framework, and commit."
---

# Project Initialiser

Onboard a repo end-to-end. Owns the **initial** doc creation (it does the deep exploration); `maintain-docs` only updates docs afterwards. Small projects don't need heavy docs — the user opts in per doc (step 4). **Change nothing before the user approves the plan (step 5).**

1. **Explore & gather context**
   - Existing code → `codegraph init -i`, then answer structural questions by querying codegraph directly. Do NOT spawn Explore subagents for what the graph knows.
   - New / empty → skip codegraph.

2. **Inventory what exists** — code, tests, docs, template files, and whether a domain plugin is already present. This decides what to scaffold vs. merge.

3. **Detect the domain** — identify it, then check `~/.claude/domains/{x}-domain/`. A usable master is a **built plugin** (has `.claude-plugin/plugin.json` + skills), not just a recipe. If the folder is missing, or holds only a recipe (`Domain-Recipe.md`) with no built plugin, ask the user whether to run `domain-initialiser` first.

4. **Choose optional docs** — ask the user a single checkbox question (skip any that already exist):
   - architecture document?
   - wiki?
   - changelog?
   - styleguide / design system? (offer only for projects with a UI)

   Also: `ASSETS.md` is created **only if** the project has (or will have) a frontend that uses assets — decide this from the inventory, don't ask. Record all decisions; they gate scaffolding, the AGENTS.md doc map, and `maintain-docs` later. A "no" on architecture means `maintain-docs` ignores architecture too.

5. **Plan & pause** — explain what is planned (domain, what gets scaffolded vs. merged, which optional docs, test framework). Get user feedback. Proceed only after approval.

6. **Install domain (project-scoped) + test framework**

   ```bash
   cp -r ~/.claude/domains/{x}-domain {repo}/.claude/skills/{x}-domain
   ```

   - Optionally pre-approve its MCP in `{repo}/.claude/settings.json`:

     ```json
     { "enableAllProjectMcpServers": true }
     ```

     (or list servers in `enabledMcpjsonServers`). Loads as `{x}-domain@skills-dir` on the next session.
   - Install the unit + UI test framework named in `{x}-domain/Domain-Recipe.md`.

7. **Scaffold docs** — copy the whole `~/.claude/project_TEMPLATE/*` in one pass (`cp -rn`, never clobber existing), then **delete the optional docs the user didn't choose** (`docs/architecture/`, `docs/wiki/`, `CHANGELOG.md`, and `ASSETS.md` if no frontend). Copy-then-prune is fewer tool calls than selective copying. Note: `Architecture.md` and the wiki `Home.md` are filled in place. **On-demand artifacts are not scaffolded** — they're seeded from their skill's own `templates/` when first produced: the styleguide (`docs/design/Styleguide.html`, step 11 via `ui-design`), feature specs and e2e files (`docs/specs/`, via `spec-design` / `e2e`). So `docs/design/` and `docs/specs/` start absent and appear only when used. Fill `AGENTS.md` (domain, outline, code style — single source), then **trim its Doc map to list only the docs that remain.** If a domain was installed (step 6), add its memory import to the project `CLAUDE.md` so domain memory loads here: `@~/.claude/domains/{x}-domain/DOMAIN-MEMORY.md`. (Project memory is native — `~/.claude/projects/<repo>/memory/` — nothing to scaffold.)

8. **ASSETS.md** (only if the frontend condition in step 4 holds) — dispatch a subagent to explore the **asset tree only** (codegraph does not cover assets) and fill `ASSETS.md`.

9. **Architecture** (only if chosen) — for an existing codebase, read the core source files and fill `docs/architecture/Architecture.md` at the **macro level only** (big picture). For a new project, or whenever the user wants a *designed* rather than reverse-engineered architecture, invoke `project-designer` to design it and write the doc, then continue. Details accrue later via `maintain-docs`.

10. **Wiki** (only if chosen) — fill `docs/wiki/Home.md` and add the pages it lists, scaled to the project.

11. **Styleguide** (only if chosen) — invoke `ui-design` at the styleguide level; it seeds `docs/design/Styleguide.html` from its template and fills it. Skip if `project-designer` already set it during the architecture step (9).

12. **Commit** — if the directory isn't a git repo yet, `git init` first, then commit. (Open a PR if the repo uses that flow.)
