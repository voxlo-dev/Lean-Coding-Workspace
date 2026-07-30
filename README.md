# Claude Workspace

An opinionated setup for [Claude Code](https://claude.com/claude-code): a global instruction file
plus a set of skills that turn "ask an AI to code" into a repeatable process — plan, spec,
implement, test, document, ship.

It is **plain Markdown**. Nothing to build, no runtime, no lock-in. You install it once into
`~/.claude/`, and from then on every project you work on follows the same workflow.

## Why

Coding agents are strong at writing code and weak at everything around it. Left alone they lose
the thread across sessions, re-litigate settled decisions, let docs rot, and answer structural
questions by reading half the repo. This workspace fixes that with three ideas:

- **Workflows over vibes.** Development work goes through a named pipeline whose steps are fixed.
  You pick the size of the pipeline, not whether to have one.
- **Every fact has exactly one home.** Durable truth in `docs/`, open work in `tickets/`, process
  history in `artefacts/`, machine-specific facts in memory. No fact lives in two places, so
  nothing silently goes stale.
- **A lean always-loaded core.** The global instruction file says *when* something applies and
  *where* the rest lives — about 2.3k tokens. Everything else is a skill, loaded only when used.

## Requirements

- Claude Code (CLI, desktop, or IDE extension)
- Git
- Five plugins, installed for you by the install skill: `superpowers`, `codegraph`, `context7`,
  `github`, `plugin-dev`

## Install

```bash
git clone <this-repo> claude-workspace
```

Then open Claude Code **in that folder** and say:

> Read `.claude_TEMPLATE/skills/workspace-install/SKILL.md` and follow it.

The skill isn't in `~/.claude/` yet, so this first run is read-and-follow by hand — that's
expected. It copies the template into `~/.claude/`, detects your system, interviews you briefly
(role, languages, preferred conversation language), installs the plugins, and verifies they
actually work rather than merely exist.

**Restart Claude Code afterwards.** New skill folders are only discovered in a fresh session.

Re-running `/workspace-install` later is also the **repair and sync path**: it overwrites what the
workspace owns (`skills/`, `project_TEMPLATE/`), merges `CLAUDE.md` section by section, and never
touches `memory/`, `projects/`, `domains/` or your settings.

## How a session goes

```
new project ──→ /project-initialiser ──→ /open-sprint ─┐
                                                       │
                    ┌──────────────────────────────────┘
                    ↓
              pick a workflow ──→ spec ──→ implement ──→ test ──→ docs + memory ──→ commit/PR
                    ↑                                                                   │
                    └───────────────── next run, same sprint ───────────────────────────┘
                                                       │
                                    release done ──→ /close-sprint ──→ /open-sprint
```

Concretely, at the start of a chat Claude checks the project's memory, then asks which workflow to
use and recommends one. Before it starts it runs a short preflight: is the project initialised,
which sprint are we in, is the git tree clean, and should it pause before each commit or run
autonomously.

Non-development work — writing, research, a one-off shell task — skips all of this. The workflow
gate applies to code only.

## The workflows

| Skill | Reach for it |
| --- | --- |
| `/minimal-workflow` | one small, well-scoped change or bugfix — no spec |
| `/dynamic-workflow` | the default for real features: spec → implement → optional e2e → docs → memory → PR |
| `/orchestrator-workflow` | (experimental) large, parallelisable work; pair-planning, work packages, parallel subagents, E2E loop |
| `/localagent-workflow` | (experimental) a build that must stay robust on a weak or local (~30B) model: sequential, context-frugal, forced TDD |
| `/plan` | **first**, whenever a fuzzy idea or brainstorm has to become concrete work. Output is tickets, never a plan file |

Supporting skills, mostly invoked by the workflows rather than by you:

| Skill | Role |
| --- | --- |
| `/project-initialiser` | onboard a repo: explore, detect domain, scaffold docs, install test framework, migrate existing docs |
| `/open-sprint` · `/close-sprint` | open and close a sprint (see below); also the entry point for a brand-new project |
| `spec-design` | stage 1 of `dynamic-workflow`: brainstorm, design the UI, decide the test + implementation strategy, write the spec |
| `e2e` | optional end-to-end validation stage; drives the feature once, then writes the automation |
| `ui-design` | look and feel — colors, typography, layout, mockups, design system |
| `maintain-docs` · `maintain-memory` | the docs and memory steps; both prune as well as write |
| `/domain-initialiser` | build a domain master (see below) |
| `/workspace-install` | install, repair, sync |

## Work items — the Markdown kanban

Every unit of work is a **ticket** file: `tickets/T-NNN-{slug}.md`, holding *what* and *why*,
category, importance, effort and dependencies. A ticket is written once and then frozen.

Tickets carry **no status field**. Their position on a board *is* the status, and each ticket is
indexed in exactly one place:

- **`backlog.md`** — living, survives sprints. Columns **Draft** · **Backlog**: everything open.
  Open *decisions* live here too, as `decision` tickets.
- **`artefacts/{sprint}/sprint.md`** — the sprint file: plan on top, board underneath. Columns
  **Active** · **To Test** · **Done**. Active *is* the sprint scope, and the board freezes when the
  sprint closes.

A fix you do on the spot needs no ticket. The board captures what *isn't* being done right now.

**A sprint is not a run.** It is the scope of a release and holds many runs, plans and specs.
`/open-sprint` pulls tickets from the backlog onto a fresh board and cuts a branch;
`/close-sprint` clears the board, optionally reviews the whole sprint diff, distils what shipped
into the durable docs, cuts the changelog, and merges.

## What a project looks like

```
project/
├── AGENTS.md              ← domain, structure, code style, current sprint (for contributors & agents)
├── README.md              ← for your users
├── backlog.md             ← index of every open ticket
├── tickets/               ← one file per unit of work, frozen once written
├── docs/                  ← DURABLE: truth about the shipped system
│   ├── behaviour.md       ←   how the product behaves, as a rulebook
│   ├── decisions.md       ←   settled decisions, one line each
│   ├── architecture.md    ←   planned/implemented structure
│   ├── dev.md             ←   setup, env, build/debug, dependency quirks
│   └── product/           ←   end-user docs
└── artefacts/{sprint}/    ← EPHEMERAL: specs, e2e files, reports — frozen per sprint
```

Docs are split by **lifespan**, and you opt in per doc — a small project doesn't need all of them.
`docs/` is versioned truth, `tickets/` is open work, `artefacts/` is process history. That split is
what keeps the whole thing from turning into a swamp.

## Domains

A **domain** bundles everything specific to one kind of development — Unity, web frontend, Android —
as its own plugin: conventions, skills, agents, language server and MCP config.

Masters live **inert** in `~/.claude/domains/{x}-domain/`, so nothing domain-specific loads
globally. `project-initialiser` copies the matching master into a repo's
`.claude/skills/{x}-domain/`, where it loads project-scoped: the Unity MCP runs in Unity repos and
nowhere else.

No masters ship with this repo — you build the ones you need with `/domain-initialiser`.

## Memory

Long-term memory is native Markdown, curated by `maintain-memory`, in three scopes: **project**
(auto-loaded per repo), **domain**, and **global**. The rule that keeps it useful: machine-bound
facts (absolute paths, local installs, personal tool setup) belong in memory; system-independent
engineering knowledge belongs in `docs/dev.md`. Never both.

## Making it yours

The whole workspace is Markdown — fork it and edit. Two things worth knowing:

- **Edit the template, not the install.** `skills/` and `project_TEMPLATE/` in `~/.claude/` are
  overwritten on every sync. Change `.claude_TEMPLATE/` in the repo, then re-run
  `/workspace-install`.
- **`CLAUDE.md` is shared.** Your **User Info**, **System Info** and custom **RULES** survive a
  sync; the structural parts get merged. Keep it lean — it is loaded in every single session, and
  every token here is a token you pay for forever.

## Repo layout

```
.claude_TEMPLATE/          ← the source of truth, mirrored into ~/.claude/ by workspace-install
├── CLAUDE.md              ←   the always-loaded global instruction file
├── skills/                ←   workflows and supporting skills
├── project_TEMPLATE/      ←   scaffold copied into each new project
├── domains/               ←   domain master scaffold
└── memory/                ←   global memory seed
```
