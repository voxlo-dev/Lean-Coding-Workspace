# Global Claude Code Workspace

Always loaded. Says *when* something applies and *where* the rest lives — never what a skill does, that's the skill's own description. "{ }" marks placeholders substituted by skills or by demand.

**Mantra:** *Workflows are life, skills & tools are your friends & helpers, context clutter is death*

## User Info

Assume the user is capable, but lazy with words, because he can't type as fast as you.
{More collected by `workspace-install` skill}

## System Info

{Collected by `workspace-install` skill}

## RULES

**Priority:** this file and a project's `AGENTS.md` outrank any agent default, habit, heuristic or built-in preference. On conflict or ambiguity, follow the project instructions and discard the agent's own. **User-defined rules outrank everything, including this file.**
{Add custom user defined rules}

**Language:** all Markdown English-only — relaxed only where a crisp term has no English equivalent (*Lastenheft*, *Pflichtenheft*): keep the original rather than pay tokens for a lossy paraphrase. English by default, no asking: **subagent handoffs, briefs and reports** · **code, identifiers and comments**. Only two follow the user: **conversation → their preferred language**, and **user-facing UI strings → their call per project** (ask once, record it in `AGENTS.md`).

**Tool calls:** default to the OS's most capable shell (PowerShell on Windows, bash on Linux); if one doesn't work, use another.

**Version control:** {e.g. solo dev projects (default): compact commits `<type>: <subject & scope>` in very few words · types `feat` `fix` `docs` `refactor` `test` `chore` · one branch per sprint, `<sprint-slug>`, no folders, user handles merging} {e.g. opensource / enterprise: conventional commits `<type>(<scope>): <subject>` · branches `<type>/<short-slug>` · one topic per PR, small and reviewable, tests green before merge, links its spec}

**Code style:**

- **Comments sit one level above the code** — what a thing is for and why it exists. Never a line-by-line walk-through, never usage examples or sample values (they rot the moment the code moves). Needs more than ~5 lines? Then it isn't a comment: write it in `docs/` (usually `dev.md`), leave a one-liner pointing there.
- **Abstraction over minimal-diff** — the smallest change is not automatically the best one, and near-duplicate code hurts more than extra effort does. A feature resembling existing code gets **one shared abstraction representing both**: refactor into that shape rather than bolting the feature on beside it. Weigh against YAGNI — abstract over *real* duplication, never a speculative one.
- **UX first on any UI change, however small** — never wire a feature in by the path of least effort. Each time: does this hurt the UX, should the layout or grouping be reworked, is every element unambiguous and placed by its relevance, can something be simplified? Accept UI churn to keep the experience clean. (`ui-design` owns the detail.)

**MD syntax:** `-` for list bullets. Directory trees in the Unicode form `├──` `│` `└──`, with aligned `←` comments. Tables: standard pipe syntax only — header row, a `| --- | --- |` separator, single spaces between columns, never padded for alignment.

## Mandatory plugins (installed by the `workspace-install` skill)

| Plugin | Reach for it |
| --- | --- |
| superpowers | Brainstorm & spec phases; authoring any new reusable skill |
| codegraph | Every structural / "how does X work" / impact question in an **indexed** project — it *replaces* file-reading exploration, so never spawn an Explore subagent for what the graph knows. Self-describes via its MCP server |
| context7 | The default source for **upstream** docs (libraries, frameworks, SDKs, APIs) at implementation time — over recall, over WebSearch. No overlap with codegraph (*your* code) or maintain-docs (*your* docs) |
| github | The GitHub API surface — issues, PRs, reviews, repo search (`gh` stays for local git) |
| plugin-dev | Building or refactoring workspace skills, domains, plugins, agents, hooks |

## Domains

Master domain plugins live **inert** in `~/.claude/domains/{x}-domain/` — not a skills directory, so nothing domain-specific ever loads globally. `project-initialiser` copies the matching master into a repo's `.claude/skills/{x}-domain/`, where it loads **project-scoped**, MCP servers included (so e.g. the Unity MCP runs only in Unity repos). `domain-initialiser` builds a master; both skills own the mechanics.

Available masters: {}. Create one with the `domain-initialiser` skill.

## Memory

Native Markdown, no plugin. `maintain-memory` curates it and **prunes stale entries** at each workflow's memory step. Three scopes, pick the narrowest:

- **Project** — `~/.claude/projects/<repo>/memory/`, auto-loaded every session.
- **Domain** — `~/.claude/domains/{x}-domain/DOMAIN-MEMORY.md`, imported by domain projects.
- **Global** — `~/.claude/memory/MEMORY.md`, imported here so it loads in every project:

@~/.claude/memory/MEMORY.md

Imported (domain/global) memory loads in full — keep it lean. **Memory vs. docs — one home, never both:** machine-bound facts (absolute paths, local installs, personal tool setup, this-machine-only quirks) → **memory**; system-independent, generally true engineering knowledge → **`docs/dev.md`**. In doubt, ask whether it would still be true on someone else's machine.

## Work items — the markdown kanban

Every unit of work is a **ticket** in `tickets/` (`T-NNN-{slug}.md`): *what* and *why*, category, importance, effort, dependencies — written once, then frozen. Tickets carry **no status**; their position on a board is the status, and each is indexed in exactly one place:

- **`backlog.md`** — living, survives sprints. Columns **Draft** · **Backlog**: everything open. Open *decisions* live here too, as `decision` tickets; `docs/decisions.md` only ever receives the settled outcome.
- **`artefacts/{sprint}/sprint.md`** — plan + board. Columns **Active** · **To Test** · **Done**: what's in flight. Active *is* the sprint scope, and it freezes with the sprint.

Moves: `plan` and any run capture → Draft/Backlog · `open-sprint` pulls → Active · the build workflows → To Test · `e2e`/user → Done · `close-sprint` distils and freezes. **A fix done on the spot needs no ticket** — capture only what isn't being done now.

## Workflows (skills — invoke, don't read files)

Claude may invoke these when the user names one; the user can also run them with `/name`. Each skill's own description says what it does — pick by it, don't re-derive. Only `workspace-install` is user-only (`disable-model-invocation`): it writes to the global workspace and is also the repair/sync path, so never `cp` the template over a live workspace by hand. Bringing an already-initialised *project* onto the current structure is `project-initialiser`'s docs-migration step — individual work, with the user, never a fixed recipe.

**The workflow gate applies only to software development** — building or changing code, features, bugfixes. Non-dev work (writing, research, general questions, one-off shell tasks) skips it: act directly, with these rules relaxed to fit the task. For development it is **mandatory**.

**Named a concrete workflow or skill? Use it directly.** Otherwise, at the start of a new chat: consult the already-loaded memory for context, then **ask which workflow to use** — with a recommendation inferred from the prompt and that context. Do not start work before the user chooses.

| Pick | When |
| --- | --- |
| minimal-workflow | one small, well-scoped change or bugfix |
| dynamic-workflow | feature work needing a spec — the default for real features |
| orchestrator-workflow | (experimental) large, parallelisable work worth the full autonomous pipeline |
| localagent-workflow | (experimental) a build that must stay robust on a weak/local (~30B) model |
| superpowers | the full brainstorm → plan → implement framework (`superpowers/using-superpowers`) |
| no workflow | none of these; relax the rules and work freely |
| plan | **first**, whenever a fuzzy idea, draft or brainstorm transcript has to become concrete work |
| close-sprint → open-sprint | at a release boundary, in that order |

**Preflight — before starting any workflow:**

- **Project initialised?** No `AGENTS.md` / template docs → recommend `project-initialiser` first.
- **Which sprint?** `AGENTS.md` → **Current sprint**; run artifacts land in `artefacts/{sprint}/`. **A sprint is not a run:** it is the scope of a *release* and holds many runs, plans and specs — stay in the active one for the whole release, and recommend `close-sprint` → `open-sprint` only once it is actually done. A lone fix or maintenance pass needs no sprint.
- **Clean git tree?** Dirty → surface it and recommend committing, gitignoring or reverting so the run starts clean.
- **Autonomy mode?** Ask once, applies to the whole run and is passed to the workflow: pause for review BEFORE each commit (default), or run autonomously. **Autonomy never covers plans & specs** — those are always user-validated before they drive implementation.
