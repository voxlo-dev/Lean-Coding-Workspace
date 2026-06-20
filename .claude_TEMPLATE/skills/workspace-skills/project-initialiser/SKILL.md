---
name: project-initialiser
description: "Use to onboard a new or existing project: explore, detect the domain, scaffold the template, set up docs, install the test framework, and commit. Explicit-invoke."
disable-model-invocation: true
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

7. **Scaffold docs** — copy the whole `~/.claude/project_TEMPLATE/*` in one pass (`cp -rn`, never clobber existing), then **delete the optional docs the user didn't choose** (`docs/architecture/`, `docs/wiki/`, `CHANGELOG.md`, and `ASSETS.md` if no frontend). Copy-then-prune is fewer tool calls than selective copying. Note: `ARCHITECTURE.md` and the wiki `Home.md` are filled in place (not templates); `SPEC_TEMPLATE.md` and `Domain-Recipe_TEMPLATE.md` stay templates, copied on demand by `spec-workflow` / `domain-initialiser` — leave them as-is. Fill `AGENTS.md` (domain, outline, code style — single source), then **trim its Doc map to list only the docs that remain.**

8. **ASSETS.md** (only if the frontend condition in step 4 holds) — dispatch a subagent to explore the **asset tree only** (codegraph does not cover assets) and fill `ASSETS.md`.

9. **Architecture** (only if chosen) — read the core source files and fill `docs/architecture/ARCHITECTURE.md` at the **macro level only** (big picture). Details accrue later via `maintain-docs`.

10. **Wiki** (only if chosen) — fill `docs/wiki/Home.md` and add the pages it lists, scaled to the project.

11. **Commit** — if the directory isn't a git repo yet, `git init` first, then commit. (Open a PR if the repo uses that flow.)
