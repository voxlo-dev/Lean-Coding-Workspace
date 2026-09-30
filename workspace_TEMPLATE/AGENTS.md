# Global Lean Coding Workspace

Always loaded. Says *when* something applies and *where* the rest lives — what a skill does is its own description. `{…}` marks placeholders. Three are fixed: **`{home}`** = this harness's home (`~/.claude` · `~/.codex` · `~/.config/opencode`), **`{project-agent-dir}`** = its per-repo directory (`.claude/` · `.codex/` · `.opencode/`), and **`~/.agents/`** = the workspace's own harness-neutral home: `skills/` (per repo `.agents/skills/`), `memory/`, `domains/`, `project_TEMPLATE/`, `DISPATCH-GUIDE.md` (machine-local: which model runs which role; absent = no dispatch). `{home}` keeps only what its harness reads at a fixed path — the instruction file, `agents/`, `adapter/`. **Anything the workspace owns lives once, in `~/.agents/`**; a second copy is drift.

**Mantra:** *Workflows are life, skills & tools are your friends & helpers, context clutter is death*

## User Info

Assume the user is capable but terse — they type slower than you read.
{More collected by `INSTALL.md`'s personalisation step}

## System Info

{Collected by `INSTALL.md`'s personalisation step}

## RULES

**Priority:** this file and a project's `AGENTS.md` outrank any agent default, habit or built-in preference; on conflict follow them. **User-defined rules outrank everything, including this file.**
{Add custom user defined rules}

**Language:** all Markdown English — keep a crisp term with no English equivalent (*Lastenheft*, *Pflichtenheft*) rather than a lossy paraphrase. English without asking: **subagent handoffs, briefs, reports** · **code, identifiers, comments**. Only two follow the user: **conversation → their preferred language**, **user-facing UI strings → their call per project** (ask once, record it in `AGENTS.md`).

**Tool calls:** the OS's most capable shell (PowerShell on Windows, bash elsewhere), another if it fails. A **large file** goes through the file tools (`Write`/`Edit`), never a heredoc or redirect — quoting and encoding mangle it.

**Version control:** {e.g. solo dev projects (default): compact commits `<type>: <subject & scope>` in very few words · types `feat` `fix` `docs` `refactor` `test` `chore` · one branch per sprint, `<sprint-slug>`, no folders · **merge straight to `main`** at sprint close, no PR or review round unless asked} {e.g. opensource / enterprise: conventional commits `<type>(<scope>): <subject>` · branches `<type>/<short-slug>` · one topic per PR, small and reviewable, tests green before merge, links its spec, reviewed before merge}

**Code style:**

- **Comments sit one level above the code** — what a thing is for and why it exists; never a line-by-line walk-through, usage examples or sample values. Over ~5 lines → `docs/` (usually `dev.md`), plus a one-line pointer.
- **Write only what gets read again** — before any comment, doc section or artifact note: will anyone read it · is it relevant beyond the user · will it still be true in a month. Any "no" → the chat, not a file.
- **Present state only** — docs and comments describe what *is*: no "no longer", "used to", removal notes or record of what was tried. Exceptions: `docs/decisions.md` (`superseded by`) and `artefacts/`.
- **Abstraction over minimal-diff** — a feature resembling existing code gets **one shared abstraction representing both**, refactored into that shape rather than bolted on beside it. YAGNI: abstract over *real* duplication only.
- **UX first on any UI change, however small** — rework layout or grouping where it serves the experience, keep every element unambiguous and placed by relevance; accept UI churn over the path of least effort (`ui-design` owns the detail).

**Templates — fill, then strip.** A `CONTRACT` block instructs whoever fills the file: delete it on the first real fill, leaving one line pointing at its template. A file still empty keeps it.

**MD syntax:** `-` bullets · directory trees in `├──` `│` `└──` with aligned `←` comments · pipe tables with a `| --- | --- |` separator, single spaces, never padded.

## Required capabilities

`workspace-sync` installs these per target; one it cannot provide stays **visible as missing**, never silently becomes an instruction.

| Capability | Reach for it |
| --- | --- |
| codegraph | every structural / "how does X work" / impact question in an **indexed** project — it *replaces* file-reading exploration, so never spawn an Explore subagent for what the graph knows |
| context7 | the default source for **upstream** docs (libraries, frameworks, SDKs, APIs) at implementation time — over recall, over web search |
| github *(optional)* | PRs, reviews, issues, repo search, secret scanning; dead without its token. **No Actions, no release creation** — those, tagging and local git are `gh` |

## Domains

A domain is a **capability bundle** — skills, agents, MCP servers, memory, doc sources — that never changes a workflow. Masters live **inert** in `~/.agents/domains/{x}/`; `project-init` projects one or several into a repo, `domain-init` builds one.

Available masters: {}. Create one with the `domain-init` skill.

## Memory

Native Markdown, curated and pruned by `maintain-memory`. Three scopes, narrowest first: **project** `{home}/projects/<repo>/memory/` (only where the harness stores one) · **domain** `~/.agents/domains/{x}/DOMAIN-MEMORY.md`, the master, never a projected copy · **global** `~/.agents/memory/MEMORY.md`. A harness is **pointed** at the one file, never given a copy; where it can't be, the file is **readable but not loaded** — open it before relying on memory, and say it wasn't in context rather than that there was none. Machine-bound facts → memory; generally true engineering knowledge → `docs/dev.md`. Never both.

## Work items & decisions

Every unit of work is a **ticket** `backlog/T-NNN-{slug}.md` (from `shape`'s `templates/TICKET_TEMPLATE.md`; capturing one needs no `shape` run). The file never moves; it is indexed on exactly one board — `backlog/backlog.md` (**Draft** · **Backlog**, carrying the next free `T-NNN`) until pulled, then `artefacts/{sprint}/sprint.md` with a status token **open · active · to test · done**; `close-sprint` dissolves the done ones. **A fix done on the spot needs no ticket.** `artefacts/{sprint}/` stays correctable until `close-sprint` freezes it — correcting, never annotating.

A **settled decision** is written in one move: its reasoning as a section in `artefacts/{sprint}/sprint-decisions.md`, one line in `docs/decisions.md`. An open one is a `decision` ticket.

## Workflows (skills — invoke, don't read files)

Invoke one when the user names it; the user can also run it as `/name` where supported. Pick by each skill's own description. Installing or repairing the workspace is `INSTALL.md` and `workspace-sync` in the workspace repo, started by the user alone — never copy the template over a live workspace by hand. An initialised project on an older layout is `project-init`'s docs migration, with the user.

**The workflow gate applies only to software development** — building or changing code. Non-dev work (writing, research, questions, one-off shell tasks) skips it: act directly, rules relaxed to fit. For development it is **mandatory**.

**Checkpoint first.** `CHECKPOINT.local.md` (project root, written by `checkpoint`) is the previous chat's handout, already in context via the project's instruction file: act on it, then **delete the file**.

**Named a concrete workflow or skill? Use it directly.** Otherwise, at the start of a new chat: consult the loaded memory, then **ask which workflow to use**, recommending one inferred from the prompt. Do not start before the user chooses.

| Pick | When |
| --- | --- |
| minimal-workflow | one small, well-scoped change or bugfix |
| dynamic-workflow | feature work needing a spec — the default for real features |
| {bundle workflows} | {one row per workflow bundle in `~/.agents/BUNDLES.md`, written by `workspace-sync`} |
| no workflow | none of these; relax the rules and work freely |
| shape | **first**, whenever a fuzzy idea, draft or brainstorm transcript has to become concrete work |
| close-sprint → open-sprint | at a sprint boundary, in that order |
| release | a version's worth of scope sits on `main` and the project publishes — after the close, never inside a sprint |

**Preflight — before starting any workflow:**

- **Project initialised?** No `AGENTS.md` / template docs → recommend `project-init` first.
- **Which sprint?** `AGENTS.md` → **Current sprint**; run artifacts land in `artefacts/{sprint}/`. A sprint holds many runs — stay in it until its scope is done, then `close-sprint` → `open-sprint`; a version spans as many sprints as it needs. A lone fix or maintenance pass needs no sprint.
- **Clean git tree?** Dirty → surface it and recommend committing, gitignoring or reverting so the run starts clean.
