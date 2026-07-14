---
name: project-initialiser
description: "Use to onboard a new or existing project: explore, detect the domain, scaffold the template, set up docs (re-homing any that came from an older or different workspace), install frameworks and the test framework, fix the gitignore, and commit."
---

# Project Initialiser

Onboard a repo end-to-end. Owns the **initial** doc creation (it does the deep exploration); `maintain-docs` only updates docs afterwards. Small projects don't need heavy docs — the user opts in per doc (step 4). **Change nothing before the user approves the plan (step 5).**

1. **Explore & gather context**
   - **Existing code → run `codegraph init -i` now.** This is a required setup action, not optional exploration — index the repo *before* asking any structural question. Then answer structural questions by querying codegraph directly; do NOT spawn Explore subagents for what the graph knows.
   - New / empty → skip codegraph (nothing to index yet; note it so the user can run `codegraph init` once code exists).

2. **Inventory what exists** — code, tests, docs, template files, and whether a domain plugin is already present. This decides what to scaffold vs. merge — and flag any docs that came from a different or older workspace layout (they get re-homed in step 7, not rewritten).

3. **Detect the domain** — identify it, then check `~/.claude/domains/{x}-domain/`. A usable master is a **built plugin** (has `.claude-plugin/plugin.json` + skills), not just a recipe. If the folder is missing, or holds only a recipe (`Domain-Recipe.md`) with no built plugin, ask the user whether to run `domain-initialiser` first.

4. **Choose optional docs** — ask the user a single checkbox question (skip any that already exist). Explain the lifespan split once: `docs/` holds durable truth, `artefacts/` (created on first use) holds frozen process history.
   - **behaviour doc?** (`docs/behaviour.md` — product semantics as a rulebook; **recommend it for anything with user interaction** — it's the anti-drift SSOT specs write deltas against)
   - **decisions log?** (`docs/decisions.md` — append-only ADR-lite; near-zero maintenance, **recommend for any non-trivial project**)
   - architecture document? (`docs/architecture.md`)
   - developer docs? (`docs/dev.md` — setup, env, build/debug workflows, dependency quirks; the engineering knowledge codegraph/tests don't capture)
   - product docs? (`docs/product/` — end-user guides / reference; replaces the old wiki, single source for any published site)
   - styleguide / design system? (offer only for projects with a UI)
   - changelog? (`CHANGELOG.md` — **only if the project has releases / external users; default off** — otherwise the git history is the record)

   Also: `ASSETS.md` is created **only if** the project has (or will have) a frontend that uses assets — decide this from the inventory, don't ask. Record all decisions; they gate scaffolding, the AGENTS.md doc map, and `maintain-docs` later. A "no" on a doc means `maintain-docs` ignores it too.

5. **Plan & pause** — explain what is planned (domain, what gets scaffolded vs. merged, which optional docs, test framework). Get user feedback. Proceed only after approval.

6. **Install domain (project-scoped), frameworks & test framework**

   ```bash
   cp -r ~/.claude/domains/{x}-domain {repo}/.claude/skills/{x}-domain
   ```

   - Optionally pre-approve its MCP in `{repo}/.claude/settings.json`:

     ```json
     { "enableAllProjectMcpServers": true }
     ```

     (or list servers in `enabledMcpjsonServers`). Loads as `{x}-domain@skills-dir` on the next session.
   - Install the project's frameworks and runtime packages, then the unit + UI test framework named in `{x}-domain/Domain-Recipe.md`.
   - **Audit the git tree before anything is staged** — run `git status` and make sure `.gitignore` excludes everything that must never be committed: installed packages (`node_modules/`, `.venv/`, `vendor/`, …), build output (`dist/`, `build/`, `target/`, …), logs, caches, and local env files. Create or fix `.gitignore` now; if such files are already tracked, untrack them (`git rm --cached`).

7. **Scaffold docs** — copy the whole `~/.claude/project_TEMPLATE/*` in one pass (`cp -rn`, never clobber existing), then **delete the optional docs the user didn't choose** (`docs/behaviour.md`, `docs/decisions.md`, `docs/architecture.md`, `docs/dev.md`, `docs/product/`, `CHANGELOG.md`, and `ASSETS.md` if no frontend). Copy-then-prune is fewer tool calls than selective copying. Note: `behaviour.md`, `decisions.md`, `architecture.md`, `dev.md`, and `product/index.md` are filled in place. **On-demand artifacts are not scaffolded** — they're seeded from their skill's own `templates/` when first produced: the styleguide (`docs/design/Styleguide.html`, step 11 via `ui-design`), and all workflow run artifacts — specs, plans, e2e files, reports — under `artefacts/{sprint}/` (via `plan` / `spec-design` / `e2e` / the workflows). So `docs/design/` and `artefacts/` start absent and appear only when used. Fill `AGENTS.md` (domain, outline, code style — single source), then **trim its Doc map to list only the docs that remain.** If a domain was installed (step 6), add its memory import to the project `CLAUDE.md` so domain memory loads here: `@~/.claude/domains/{x}-domain/DOMAIN-MEMORY.md`. (Project memory is native — `~/.claude/projects/<repo>/memory/` — nothing to scaffold.)

   **Docs repair (existing docs from another or older workspace).** If step 2 flagged docs that follow a different or outdated layout, do **not** re-author them: read only their **outline / context files** (headings, front matter, any index) to learn what each doc is, then **re-home them into the current structure** — move and rename files into their matching template slots. Watch specifically for the **old (pre-lifespan-split) layout** and map it across:
   - `docs/artefacts/**` → `artefacts/**` (artefacts moved out of `docs/`, which is now durable-only)
   - `docs/architecture/Architecture.md` → `docs/architecture.md` · `docs/developer/Developer-Docs.md` → `docs/dev.md` · `docs/wiki/` → `docs/product/`
   - old per-sprint `Changelog.md` files → fold into the root `CHANGELOG.md` (if the project opted into one) or drop them; scattered `Key decisions` sections → seed `docs/decisions.md`
   - a stray `SPEC.md` → `artefacts/{sprint}/spec-{feature}.md`

   **Move/rename only — never edit their content** (the one exception: consolidating decisions into `decisions.md` is a re-home, not a rewrite). When a doc's target slot is ambiguous, ask the user directly rather than guessing.

8. **ASSETS.md** (only if the frontend condition in step 4 holds) — dispatch a subagent to explore the **asset tree only** (codegraph does not cover assets) and fill `ASSETS.md`.

9. **Architecture draft** (only if chosen) — lay down a **draft skeleton, not a finished doc**: derive the section structure of `docs/architecture.md` from the domain recipe and the macro picture (existing code → read core files for the big picture only; new project → the intended shape). Mark it **`Status: draft`** at the top and each subsystem **`planned`**; don't write speculative internals. `maintain-docs` fills subsystems concretely and flips them to `implemented` as they get built. The architecture *decisions* — a new project's design, or a big change — belong to `sprint-cycle`'s planning (recorded in `docs/decisions.md`), not here; recommend `sprint-cycle` as the next step after init. Leave `behaviour.md` and `decisions.md` (if chosen) as their seeded templates — they fill up as features ship, not at init.

10. **Product docs** (only if chosen) — fill `docs/product/index.md` and add the pages it lists, scaled to the project.

11. **Styleguide** (only if chosen) — invoke `ui-design` at the styleguide level; it seeds `docs/design/Styleguide.html` from its template and fills it.

12. **Commit** — if the directory isn't a git repo yet, `git init` first, then commit. (Open a PR if the repo uses that flow.)
