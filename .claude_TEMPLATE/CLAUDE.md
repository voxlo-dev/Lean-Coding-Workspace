# Global Claude Code Workspace

Always loaded. Defines the mandatory toolchain, domain activation, and workflow selection. Keep lean — detail lives in skills (loaded on demand). "{ }" marks placeholders that can be substituted by skills or by demand.

**Mantra:** *Workflows are life, skills & tools are your friends & helpers, context clutter is death*

## User Info

Assume the user is capable, but lazy with words, because he can't type as fast as you.
{More collected by `workspace-install` skill}

## System Info

{Collected by `workspace-install` skill}

## RULES

**Priority rules:** Workspace instructions in this file and in a project's `AGENTS.md` always have priority over any agent-specific defaults, habits, heuristics, or built-in workflow preferences. If there is any conflict or ambiguity, follow the project instructions first and discard the agent's own preference.

**User defined rules:** User defined rules have priority over ANYTHING else including other rules from this file.
{Add custom user defined rules}

**Language rules:** Keep all Markdown files English only — relaxed only where a crisp, well-defined term has no exact English equivalent (e.g. *Lastenheft* / *Pflichtenheft*): keep the original term rather than spend tokens on a lossy paraphrase. Defaults, no asking needed: **subagent handoffs, briefs and reports → English**; **code, identifiers and comments → English**. Only two things follow the user: **conversation → the user's preferred language**, and **user-facing UI strings → the user's call per project** (ask once, record it in `AGENTS.md`).

**Tool call rules:** Default to the most capable shell of the operating system (e.g. PowerShell for Windows / bash for Linux), if one shell does not work use another.

**Version Control rules:** {e.g For personal solo dev projects (default):

- Compact Commits — `<type>: <subject & scope>` - in very few words
  Types: `feat` `fix` `docs` `refactor` `test` `chore`
  e.g. `feat: add token refresh for auth` · `fix: handle empty payload in api`
- Simple Branching - `<sprint-slug>` - no folders, one branch per sprint (user handles merging)
  e.g. `initialise-project` · `dashboard-ui-v2` · `backend-optimisations` }
  {e.g. For opensource / large enterprise projects:
- Conventional Commits: `<type>(<scope>): <subject>`
  Types: `feat` `fix` `docs` `refactor` `test` `chore`
  e.g. `feat(auth): add token refresh` · `fix(api): handle empty payload`
- Branching: `<type>/<short-slug>`
  e.g. `feature/token-refresh` · `fix/empty-payload` · `chore/bump-deps`
- Pull requests: one topic per PR, small and reviewable; tests green before
  merge; link the spec/issue.
  e.g. title `feat(auth): add token refresh`, body references `artefacts/{sprint}/spec_...` }

**General codestyle rules:**

- **Code comments: compact and one level above the code.** Say what a thing is for and why it exists — never a line-by-line walk-through, never concrete usage examples or sample values (they rot the moment the code moves). If explaining it honestly needs more than ~5 lines, it isn't a comment: write it in `docs/` (usually `dev.md`) and leave a one-liner pointing there.
- **Abstraction over minimal-diff.** The smallest change is not automatically the best one — near-duplicate code hurts a clean codebase more than a little extra effort does. When a new feature closely resembles existing code, prefer **one shared abstraction that represents both** over two similar-but-separate components: less redundancy, looser coupling — at the cost of some extra coding and tests. Refactor the existing code into that shape rather than bolting the feature on beside it. (Weigh it against YAGNI: abstract over *real* duplication, not a speculative future one.)
- **UX first on any UI change — even a small one.** Never wire a feature into the UI by the path of least effort. Ask each time: does this hurt the UX? Should the layout be reworked or elements regrouped? Is every element unambiguous and placed by its relevance — can something even be simplified? Accept more UI churn to keep the experience clean. (`ui-design` owns the detail.)

**MD Syntax rules:** Always use `-` for normal list bullets in markdown files. For directory trees and annotated structure blocks. Use the pretty Unicode form: `├──`, `│`, `└──`, with aligned `←` comments. Markdown tables must use standard pipe-table syntax only: a header row, a separator row like this: `| --- | --- {...} |`, and unaligned columns separated with single whitespaces. Never use additional whitespaces for aligning. Example below.

## Mandatory plugins (installed by the `workspace-install` skill)

| Plugin | Role | When it acts |
| --- | --- | --- |
| superpowers | Brainstorming, planning, skill-creation framework | Brainstorm/spec phases; whenever a new reusable skill is needed |
| codegraph | Pre-indexed code knowledge graph (MCP) | Any structural / "how does X work" / impact question in an **indexed** project. Self-describes via its MCP server — do not duplicate its guidance here |
| context7 | Up-to-date upstream library/framework/API docs (MCP) | Implementation steps — look up current library APIs instead of trusting recall; prefer over WebSearch for library docs |
| github | GitHub API operations (MCP) — issues, PRs, reviews, repo search | Any PR / issue / repo workflow (`gh` for local git, this for the GitHub API surface) |
| plugin-dev | Skill / plugin / agent / hook authoring toolkit | Building or refactoring workspace skills, domains, and plugins |

Policy: codegraph replaces file-reading exploration. In an indexed project, answer
structural questions by querying codegraph directly — do **not** spawn Explore
subagents for what the graph already knows.

Policy: context7 is the default source for **upstream** docs (libraries, frameworks,
SDKs, APIs) — consult it at implementation time rather than trusting recall. It does not
overlap codegraph (*your* code) or maintain-docs (*your* project's docs).

## Domains

Master domain plugins live **inert** in `~/.claude/domains/{x}-domain/`. That folder is
NOT a skills directory, so masters never auto-load globally. Each master bundles its
recipe (`Domain-Recipe.md`), skills, agents, and LSP/MCP config (`plugin.json`
`lspServers` + `.mcp.json`) — all vendored in, self-contained.

`project-initialiser` copies the matching master into the repo's
`.claude/skills/{x}-domain/`, where it loads **project-scoped** — only in that repo.
Its MCP servers go through project `.mcp.json` per-server approval, so e.g. the Unity
MCP runs only in Unity repos.

Available masters: {}. Create one with the `domain-initialiser` skill.

## Memory

Long-term memory is **native Markdown** — no plugin. The `maintain-memory` skill curates
it and prunes stale entries. Three scopes, pick the narrowest:

- **Project** — `~/.claude/projects/<repo>/memory/`, auto-loaded every session (native).
- **Domain** — `~/.claude/domains/{x}-domain/DOMAIN-MEMORY.md`, imported by domain projects.
- **Global** — `~/.claude/memory/MEMORY.md`, imported here so it loads in every project:

@~/.claude/memory/MEMORY.md

`maintain-memory` runs at each workflow's memory step: writes new facts to the right
scope and **prunes stale ones**. Imported (domain/global) memory loads in full — keep lean.

**Memory vs. docs — one home, never both.** Machine-bound facts (absolute paths, local
installs, personal tool setup, this-machine-only quirks) → **memory**. System-independent,
generally true engineering knowledge → **`docs/dev.md`**. If a fact is in one, it must not
be in the other; when in doubt, ask whether it would still be true on someone else's machine.

## Work items — the markdown kanban

Every unit of work is a **ticket** file in `tickets/` (`T-NNN-{slug}.md`): *what* and *why*, category, importance, effort, dependencies — written once, then frozen. Tickets carry **no status**; their position on a board is the status, and each is indexed in exactly one place:

- **`backlog.md`** (living, survives sprints) — columns **Draft** · **Backlog**: everything open. Open *decisions* live here too, as `decision` tickets; `docs/decisions.md` only ever receives the settled outcome.
- **The sprint file** `artefacts/{sprint}/sprint-plan.md` (plan + board) — columns **Active** · **To Test** · **Done**: what's in flight. Active *is* the sprint scope. It freezes with the sprint, so `close-sprint` must finish or carry over everything left on it.

Moves: `plan` and any run capture → Draft/Backlog · `open-sprint` pulls → Active · the build workflows → To Test · `e2e`/user → Done · `close-sprint` distils and freezes. **A fix done on the spot needs no ticket** — capture only what isn't being done now.

## Workflows (skills — invoke, don't read files)

These load as skills — Claude may invoke one when you name it, and you can also run it with `/name`. (Only `workspace-install` is user-only via `disable-model-invocation`.)

**The workflow gate applies only to software-development tasks** — building or changing code, features, bugfixes. For non-dev work (writing, research, general questions, one-off shell tasks), skip it: act directly, with these workspace rules relaxed to fit the task. For software development it is **mandatory**.

**If the user named a concrete workflow or skill, use it directly.** Otherwise, at the start of a new chat:

1. Check memory for relevant context — the project `MEMORY.md` (and any imported domain/global memory) already loaded; consult it.
2. Ask the user which workflow to use — with a recommendation inferred from their prompt and that context. Do not start work before they choose.

Options:

- **minimal-workflow** — a single, small, well-scoped change or bugfix
- **dynamic-workflow** — feature work needing a spec. `spec-design` decides the test and implementation strategy once (technique: direct/tdd + execution: inline or subagent-driven); a fixed pipeline then implements, optionally runs e2e, and finishes docs / memory / PR
- **orchestrator-workflow** — (experimental) large, parallelisable work that warrants the full autonomous pipeline (pair-plan → spec → auto-scaled impl → E2E loop); heavier than dynamic-workflow
- **localagent-workflow** — (experimental) full feature build that must stay robust on a weak/local (~30B) model: sequential, context-frugal per-unit TDD loop; also runnable by an external local-model runner
- **superpowers** — invoke `superpowers/using-superpowers` for the full brainstorm → plan → implement framework
- **no workflow** — use no workflow skill; relax these rules and let the agent work freely

Turning a fuzzy idea, a draft, or a brainstorming transcript into a clear plan first → recommend `plan`. It writes a standalone `artefacts/{sprint}/plan_{feature}.md` (the *Lastenheft*, product/UX level) — optionally climbing to high-level domain/architecture decisions when the scope warrants; web research optional. Feeds `spec-design` or any workflow; standalone it is not wired into one. (`open-sprint` reuses `plan` in sprint-file mode for the sprint file itself.) Either way, `plan` cuts the tickets.

Managing a sprint is two skills, run in that order at a release boundary:

- **close-sprint** — the active release scope is done: clear the board (finish or carry over) → optional code review of the whole sprint diff → distil Done → batched docs pass → changelog cut → merge/PR to `main`.
- **open-sprint** — plan the next sprint via `plan` (sprint-file mode) → `artefacts/{sprint}/sprint-plan.md`, pull tickets from `backlog.md` onto its board → branch + `Current sprint` → dynamic-workflow handoff. Also the entry point for a **new project** (nothing to close). It records architecture *decisions*; the architecture *doc* is written by `maintain-docs` once implemented.

**Preflight — before starting any workflow:**

- **Project initialised?** No `AGENTS.md` / template docs → recommend `project-initialiser` first.
- **Which sprint?** Check `AGENTS.md` → **Current sprint** — run artifacts land in `artefacts/{sprint}/`. **A sprint is not a run:** it is the scope of a *release* and holds many runs, plans and specs. Stay in the active sprint for the whole release; only when that release is actually done and a new batch begins, recommend `close-sprint` → `open-sprint`. A lone fix or a maintenance pass needs no sprint.
- **Clean git tree?** Dirty → surface it and recommend committing, gitignoring or reverting so the run starts clean.
- **Autonomy mode?** Ask once — pause for review BEFORE each commit (default) or run autonomously. Applies to the whole run; pass it to the workflow. **Autonomy never covers plans & specs:** a `plan` or `spec` is always validated by the user before it drives implementation, even in autonomous mode — autonomy applies only to the build/commit steps downstream of an approved spec.
