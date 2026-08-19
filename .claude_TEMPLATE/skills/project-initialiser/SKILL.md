---
name: project-initialiser
description: "Use to onboard a new or existing project: explore, detect the domain, scaffold the template, set up docs (re-homing any that came from an older or different workspace), install frameworks and the test framework, fix the gitignore, and commit."
---

# Project Initialiser

Onboard a repo end-to-end. Owns the **initial** doc creation (it does the deep exploration); `maintain-docs` only updates them afterwards. Small projects need light docs — the user opts in per doc (step 4). **Act only once the user approves the plan (step 5).**

**Already initialised, just on an older layout?** Then this is a **migration**: run steps 1–2, go straight to the docs migration in step 7, scaffold only what is genuinely missing.

1. **Explore & gather context** — existing code → **run `codegraph init -i` now**, required setup rather than optional exploration: index *before* any structural question, then query the graph instead of spawning Explore subagents. New / empty repo → skip it, and note that the user can run `codegraph init` once code exists.

2. **Inventory what exists** — code, tests, docs, template files, an already-present domain plugin. This decides what to scaffold vs. merge; flag docs on a different or older layout for step 7.

3. **Detect the domain** — identify it, then check `~/.claude/domains/{x}-domain/`. A usable master is a **built plugin** (`.claude-plugin/plugin.json` + skills), not just a recipe. Folder missing, or only a `Domain-Recipe.md` → ask whether to run `domain-initialiser` first.

4. **Choose optional docs** — one checkbox question, skipping any that exist. Explain the lifespan split once: `docs/` holds durable truth, `artefacts/` (created on first use) frozen process history.
   - **behaviour doc?** `docs/behaviour.md` — product semantics as a rulebook, the anti-drift SSOT specs write deltas against. **Recommend for anything with user interaction.**
   - **decisions log?** `docs/decisions.md` — append-only one-line index, reasoning per sprint in `sprint-decisions.md`. Near-zero maintenance, **recommend for any non-trivial project**.
   - architecture document? `docs/architecture.md`
   - developer docs? `docs/dev.md` — setup, env, build/debug workflows, dependency quirks: the engineering knowledge codegraph and tests don't capture.
   - product docs? `docs/product/` — end-user guides / reference, single source for any published site.
   - styleguide / design system? (offer only with a UI)
   - changelog? `CHANGELOG.md` — **only with releases / external users, default off**; otherwise git history is the record.

   `ASSETS.md` is created **only if** the project has (or will have) a frontend using assets — decide it from the inventory. Record all decisions: they gate scaffolding, the AGENTS.md doc map and `maintain-docs` later — a "no" means `maintain-docs` ignores that doc too.

5. **Plan & pause** — present the plan (domain, scaffold vs. merge, optional docs, test framework), get feedback, proceed only after approval.

6. **Install domain (project-scoped), frameworks & test framework**

   ```bash
   cp -r ~/.claude/domains/{x}-domain {repo}/.claude/skills/{x}-domain
   ```

   - Optionally pre-approve its MCP in `{repo}/.claude/settings.json` via `{ "enableAllProjectMcpServers": true }` (or list servers in `enabledMcpjsonServers`). Loads as `{x}-domain@skills-dir` next session.
   - Install the project's frameworks and runtime packages, then the unit + UI test framework named in `{x}-domain/Domain-Recipe.md`.
   - **Audit the git tree before anything is staged** — `git status`, and make `.gitignore` exclude installed packages (`node_modules/`, `.venv/`, `vendor/`), build output (`dist/`, `build/`, `target/`), logs, caches, local env files. Untrack anything already tracked (`git rm --cached`).

7. **Scaffold docs** — copy all of `~/.claude/project_TEMPLATE/*` in one pass (`cp -rn`, no-clobber), then **delete the optional docs the user didn't choose**; copy-then-prune costs fewer tool calls than selective copying. `behaviour.md`, `decisions.md`, `architecture.md`, `dev.md` and `product/index.md` are filled in place. **`backlog/backlog.md` and `backlog/TICKET_TEMPLATE.md` always stay** — leave both, empty. **On-demand artifacts stay unscaffolded**: the styleguide (step 11) and every run artifact under `artefacts/{sprint}/` are seeded from their own skill's `templates/` when first produced, so `docs/design/` and `artefacts/` start absent. Fill `AGENTS.md` (domain, outline, code style — single source) and **trim its Doc map to the docs that remain**. With a domain installed, add its memory import to the project `CLAUDE.md`: `@~/.claude/domains/{x}-domain/DOMAIN-MEMORY.md`. (Project memory is native — nothing to scaffold.)

   **Docs migration (docs from an older or foreign layout).** **There is no fixed recipe** — every project carries its own doc history, and a canned list of renames is wrong more often than right. Derive it with the user:

   1. **Inventory** every doc-ish file, also outside `docs/`: loose plans, TODO lists, wikis, per-sprint folders, notes in the README. Identify each from its **outline only** (headings, front matter, index) — you are placing files, so the outline is enough.
   2. **Classify** each one; the kinds are what generalises, not the paths:
      - **Re-home** — still valid, only its slot changed: move/rename, content untouched.
      - **Rewrite in place** — `AGENTS.md` / `README.md` survive `cp -rn` and keep the OLD shape unless rewritten: rebuild the **Doc map** as the tiered table (only docs that exist), collapse living context to the one-line **Current sprint** pointer, repoint stale links.
      - **Consolidate / split** — scattered `Key decisions` sections into `docs/decisions.md`, a `TODO.md` into tickets, per-sprint changelogs into the root `CHANGELOG.md` (or dropped, if there is none).
      - **No counterpart** — say so plainly; leave it, fold it in, or drop it, the user's call.
   3. **Propose the whole mapping as a short list and get the user's OK before touching files.** Ask about anything ambiguous.
   4. Execute, as a **separate commit** from anything else this run does.

   Four rules hold whatever the old layout was:
   - **`docs/` is durable-only** — a finished spec or plan is process history: re-home it to `artefacts/`, then mine it for tickets.
   - **No work item stays loose in the docs** — bugs, todos, backlog sections and open decisions all become tickets in `backlog/`, indexed in `backlog.md` (**Draft** unless clearly refined).
   - **Decisions split by settled vs open** — open ones become `decision` tickets; settled ones become numbered `accepted` index lines in `docs/decisions.md`, their reasoning collected in one `artefacts/pre-init/sprint-decisions.md` (standing in for the sprints the project never had).
   - **`behaviour.md` is written, not moved.** Default: leave the seeded template, it fills as features ship. **Exception, the user's choice:** where substantial behaviour is buried in checked-off specs or a behaviour-like doc, offer a one-time distillation pass. This is the only place init writes durable behaviour content, and it exists to free truth trapped in frozen specs.

8. **ASSETS.md** (only if step 4's frontend condition holds) — dispatch a subagent to explore the **asset tree only** (codegraph doesn't cover assets) and fill it.

9. **Architecture draft** (only if chosen) — a **draft skeleton, not a finished doc**: derive the section structure from the domain recipe and the macro picture (existing code → core files for the big picture only; new project → the intended shape). Mark the doc **`Status: draft`** and each subsystem **`planned`**, keeping the internals for `maintain-docs` to fill from real code as they get built. Architecture *decisions* belong to `open-sprint`'s planning; recommend it as the next step. Leave `behaviour.md` and `decisions.md` as seeded templates.

10. **Product docs** (only if chosen) — fill `docs/product/index.md` and add the pages it lists, scaled to the project.

11. **Styleguide** (only if chosen) — invoke `ui-design` at the styleguide level; it seeds and fills `docs/design/Styleguide.html`.

12. **Commit** — `git init` first if the directory isn't a repo yet. (PR instead, if the repo uses that flow.)
