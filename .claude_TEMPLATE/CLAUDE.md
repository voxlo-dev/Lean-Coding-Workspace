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

**Language rules:** Keep all Markdown files English only.
{e.g. answer in german in chats}

**Tool call rules:**: Default to the most capable shell of the operating system (e.g powershell for windowes / bash for Linux), if one shell does not work use another.

**Version Control rules:** {e.g. Conventional Commits — `<type>(<scope>): <subject>`
  Types: `feat` `fix` `docs` `refactor` `test` `chore`
  e.g. `feat(auth): add token refresh` · `fix(api): handle empty payload`

- **Branching:** `<type>/<short-slug>`
  e.g. `feature/token-refresh` · `fix/empty-payload` · `chore/bump-deps`
- **Pull requests:** one topic per PR, small and reviewable; tests green before
  merge; link the spec/issue.
  e.g. title `feat(auth): add token refresh`, body references `docs/specs/...`}

**General codestyle rules**
{e.g. keep code comments short and precise}

**Syntax rules:** Always use `-` for normal list bullets in markdown files. For directory trees and annotated structure blocks. Use the pretty Unicode form: `├──`, `│`, `└──`, with aligned `←` comments. Markdown tables must use standard pipe-table syntax only: a header row, a separator row like this: `| --- | --- {...} |`, and unaligned columns separated with single whitespaces. Never use additional whitespaces for aligning. Example below.

## Mandatory plugins (installed by the `workspace-install` skill)

| Plugin | Role | When it acts |
| --- | --- | --- |
| superpowers | Brainstorming, planning, skill-creation framework | Brainstorm/spec phases; whenever a new reusable skill is needed |
| codegraph | Pre-indexed code knowledge graph (MCP) | Any structural / "how does X work" / impact question in an **indexed** project. Self-describes via its MCP server — do not duplicate its guidance here |
| headroom | In-session context-budget management **only** | Automatic. Handles in-session budget only — long-term memory is the native `MEMORY.md` system below, not headroom |

Policy: codegraph replaces file-reading exploration. In an indexed project, answer
structural questions by querying codegraph directly — do **not** spawn Explore
subagents for what the graph already knows.

## Domains

Master domain plugins live **inert** in `~/.claude/domains/{x}-domain/`. That folder is
NOT a skills directory, so masters never auto-load globally. Each master bundles its
recipe (`Domain-Recipe.md`), skills, agents, and `.mcp.json`.

`project-initialiser` copies the matching master into the repo's
`.claude/skills/{x}-domain/`, where it loads **project-scoped** — only in that repo.
Its MCP servers go through project `.mcp.json` per-server approval, so e.g. the Unity
MCP runs only in Unity repos.

Available masters: {}. Create one with the `domain-initialiser` skill.

## Memory

Long-term memory is **native Markdown** — no plugin. The `maintain-memory` skill curates
it; `headroom` covers in-session budget only. Three scopes, pick the narrowest:

- **Project** — `~/.claude/projects/<repo>/memory/`, auto-loaded every session (native).
- **Domain** — `~/.claude/domains/{x}-domain/DOMAIN-MEMORY.md`, imported by domain projects.
- **Global** — `~/.claude/memory/MEMORY.md`, imported here so it loads in every project:

@~/.claude/memory/MEMORY.md

`maintain-memory` runs at each workflow's memory step: writes new facts to the right
scope and **prunes stale ones**. Imported (domain/global) memory loads in full — keep lean.

## Workflows (skills — invoke, don't read files)

These load as skills — Claude may invoke one when you name it, and you can also run it with `/name`. (Only `workspace-install` is user-only via `disable-model-invocation`.)

**If the user named a concrete workflow or skill, use it directly.** Otherwise, at the start of a new chat:

1. Check memory for relevant context — the project `MEMORY.md` (and any imported domain/global memory) already loaded; consult it.
2. Ask the user which workflow to use — with a recommendation inferred from their prompt and that context. Do not start work before they choose.

Options:

- **minimal-workflow** — a single, small, well-scoped change or bugfix
- **spec-workflow** — feature work needing a spec / multiple work packages
- **orchestrator-workflow** — large, parallelisable work that warrants the full autonomous pipeline (pair-plan → spec → auto-scaled impl → E2E loop); heavier than spec-workflow
- **localagent-workflow** — full feature build that must stay robust on a weak/local (~30B) model: sequential, context-frugal per-unit TDD loop; also runnable by an external local-model runner
- **superpowers** — invoke `superpowers/using-superpowers` for the full brainstorm → plan → implement framework
- **no workflow** — use no workflow skill; relax these rules and let the agent work freely

Onboarding a new/existing project → recommend `project-initialiser` first.

Before starting the chosen workflow, make sure relevant memory context is loaded.
