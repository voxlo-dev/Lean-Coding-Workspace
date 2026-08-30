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

**Version control:** {e.g. solo dev projects (default): compact commits `<type>: <subject & scope>` in very few words · types `feat` `fix` `docs` `refactor` `test` `chore` · one branch per sprint, `<sprint-slug>`, no folders · **merge straight to `main`** at sprint close, no PR or review round unless asked} {e.g. opensource / enterprise: conventional commits `<type>(<scope>): <subject>` · branches `<type>/<short-slug>` · one topic per PR, small and reviewable, tests green before merge, links its spec, reviewed before merge}

**Code style:**

- **Comments sit one level above the code** — what a thing is for and why it exists. Never a line-by-line walk-through, never usage examples or sample values (they rot the moment the code moves). Needs more than ~5 lines? Then it isn't a comment: write it in `docs/` (usually `dev.md`), leave a one-liner pointing there.
- **Write only what gets read again** — before any comment, doc section or artifact note: will anyone read it · is it relevant to someone other than the user · will it still be true in a month. A "no" anywhere → it goes in the chat, not in a file.
- **Present state only** — docs and comments describe what *is*: no "not", "no longer", "used to", no removal notes, no record of what was tried. Exceptions: `docs/decisions.md` (`superseded by`) and `artefacts/`.
- **Abstraction over minimal-diff** — the smallest change is not automatically the best one, and near-duplicate code hurts more than extra effort does. A feature resembling existing code gets **one shared abstraction representing both**: refactor into that shape rather than bolting the feature on beside it. Weigh against YAGNI — abstract over *real* duplication, never a speculative one.
- **UX first on any UI change, however small** — never wire a feature in by the path of least effort. Each time: does this hurt the UX, should the layout or grouping be reworked, is every element unambiguous and placed by its relevance, can something be simplified? Accept UI churn to keep the experience clean. (`ui-design` owns the detail.)

**Templates — fill, then strip.** A `CONTRACT` header or guidance block instructs whoever fills the file; it is not content. Delete it on the first real fill, leaving one line pointing at its template. A file still empty keeps it.

**MD syntax:** `-` for list bullets. Directory trees in the Unicode form `├──` `│` `└──`, with aligned `←` comments. Tables: standard pipe syntax only — header row, a `| --- | --- |` separator, single spaces between columns, never padded for alignment.

## Mandatory plugins (installed by the `workspace-install` skill)

| Plugin | Reach for it |
| --- | --- |
| superpowers | Brainstorm & spec phases; authoring any new reusable skill |
| codegraph | Every structural / "how does X work" / impact question in an **indexed** project — it *replaces* file-reading exploration, so never spawn an Explore subagent for what the graph knows. Self-describes via its MCP server |
| context7 | The default source for **upstream** docs (libraries, frameworks, SDKs, APIs) at implementation time — over recall, over WebSearch. No overlap with codegraph (*your* code) or maintain-docs (*your* docs) |
| github *(optional)* | PRs, reviews, issues, repo search, secret scanning; dead without its token (`workspace-install` sets it up with `gh`). **No Actions, no release creation** — those, tagging and local git are `gh` |
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

Every unit of work is a **ticket** in `backlog/` (`T-NNN-{slug}.md`, from `~/.claude/skills/plan/templates/TICKET_TEMPLATE.md` — capturing one needs no `plan` run): *what* and *why*, category, importance, effort, dependencies. Tickets stay **live**: sharpen one whenever understanding improves. **The file never moves**; boards only *index* it, and a ticket is indexed in exactly one place:

- **`backlog/backlog.md`** — living, survives sprints. Columns **Draft** · **Backlog**: everything not yet pulled. Open *decisions* live here too, as `decision` tickets. Carries the **next free `T-NNN`** at the top.
- **`artefacts/{sprint}/sprint.md`** — frame + board: every ticket pulled into the sprint, one line each carrying a status token **open · active · to test · done**. The board *is* the sprint scope.

**`artefacts/{sprint}/` stays live until `close-sprint` freezes the sprint** — correct a spec, a report or the board while the sprint runs; only a closed folder is history. Correcting is not annotating: no progress notes, no status commentary, no record of what was tried.

Moves: `plan` and any run capture → Draft/Backlog · `open-sprint` pulls → the board at `open` · the build workflows → `to test` · `e2e`/user → `done` · `close-sprint` distils, then **dissolves** the done tickets (files deleted — that's what keeps `backlog/` bounded) and carries the rest back. **A fix done on the spot needs no ticket** — capture only what isn't being done now.

**Decisions** get two homes, written in one move the moment one is settled: the reasoning as a section in `artefacts/{sprint}/sprint-decisions.md`, and one line in `docs/decisions.md` — the flat, append-only index that makes "what is already decided here?" a single read, carrying the **next free `NNNN`** at its top — and the one durable doc allowed to link into `artefacts/`; every other stands on its own.

## Workflows (skills — invoke, don't read files)

Claude may invoke these when the user names one; the user can also run them with `/name`. Each skill's own description says what it does — pick by it, don't re-derive. Only `workspace-install` is user-only (`disable-model-invocation`): it writes to the global workspace and is also the repair/sync path, so never `cp` the template over a live workspace by hand. Bringing an already-initialised *project* onto the current structure is `project-initialiser`'s docs-migration step — individual work, with the user, never a fixed recipe.

**The workflow gate applies only to software development** — building or changing code, features, bugfixes. Non-dev work (writing, research, general questions, one-off shell tasks) skips it: act directly, with these rules relaxed to fit the task. For development it is **mandatory**.

**Checkpoint first.** `CHECKPOINT.local.md` (project root, written by `checkpoint`) is the previous chat's handout, imported by the project's `CLAUDE.md` so it is already in context: act on it, then **delete the file**.

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
| close-sprint → open-sprint | at a sprint boundary, in that order |
| release | a version's worth of scope sits on `main` and the project publishes — after the close, never inside a sprint |

**Preflight — before starting any workflow:**

- **Project initialised?** No `AGENTS.md` / template docs → recommend `project-initialiser` first.
- **Which sprint?** `AGENTS.md` → **Current sprint**; run artifacts land in `artefacts/{sprint}/`. **A sprint is not a run:** it holds many runs, plans and specs — stay in the active one until its scope is done, then `close-sprint` → `open-sprint`. **Nor is it a release:** a version spans as many sprints as it needs, and `release` cuts it from `main` after. A lone fix or maintenance pass needs no sprint.
- **Clean git tree?** Dirty → surface it and recommend committing, gitignoring or reverting so the run starts clean.
