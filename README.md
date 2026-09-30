# Lean Coding Workspace

**Turn your coding agent into a teammate that follows a process.**

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Harnesses](https://img.shields.io/badge/works%20with-Claude%20Code%20·%20Codex%20·%20OpenCode-6b46c1)
![Plain Markdown](https://img.shields.io/badge/plain-Markdown-black)

Lean Coding Workspace is a ready-made setup for **Claude Code, Codex and OpenCode**: one lean
instruction file plus a set of skills that take every change from idea to shipped code the same
way — shape, spec, build, test, document, release. It is plain Markdown. Nothing to build, no
runtime, no lock-in: install it once and every project you open works the same.

## Why you want it

Coding agents write good code and are bad at everything around it. They forget yesterday's
session, re-argue decisions you already made, let docs rot, and burn tokens reading half the repo
to answer a simple question. This workspace fixes exactly that:

- **A process, not vibes.** Pick a workflow — a quick fix or a full feature pipeline — and the agent
  walks it step by step: spec, code, green tests, docs, memory. You choose the size, never whether.
- **Nothing gets lost between chats.** Open work lives in tickets, decisions in a one-line index,
  truth about the product in `docs/`. The next session reads them instead of asking you again.
- **Docs that stay true.** Every fact has exactly one home, and each sprint folds what shipped
  back into the docs — no stale second copy.
- **Cheap on tokens.** The always-loaded core is about 2.3k tokens; everything else is a skill,
  loaded only when used. A code graph answers structural questions instead of mass file reads.
- **Your harness, your models.** The same workspace runs in three harnesses, and single steps can
  be dispatched to other agents or local models.

## How a session goes

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/session-flow-dark.svg">
    <img src="assets/session-flow.svg" alt="Install once per machine and run /project-init once per project. Then sprints repeat: /open-sprint plans tickets and opens a branch; every new chat picks a workflow; /minimal-workflow handles small fixes, /dynamic-workflow takes a feature from an approved spec to tested, documented code; /close-sprint updates the docs and merges. When a few sprints make a version, /release ships it, production only after you accept the risks." width="880">
  </picture>
</p>

1. **Set up a project once** with `/project-init` — it explores the code, scaffolds the docs you
   want and installs a test framework.
2. **Plan a sprint** with `/open-sprint` — your ideas become tickets on a board, and a branch opens.
3. **Build** — for each ticket the agent asks which workflow to use and recommends one, then runs it.
4. **Close the sprint** with `/close-sprint` — finished work is distilled into the docs and merged.
5. **Ship** with `/release` when a version's worth of work is ready.

Not writing code? Research, writing or a one-off shell task skip all of this — the agent just helps.

## The workflows

| Skill | Use it for |
| --- | --- |
| `/minimal-workflow` | a small, well-scoped change or bugfix — no spec |
| `/dynamic-workflow` | real features: spec → build → green tests → optional end-to-end test → docs → memory |
| `/shape` | a fuzzy idea or brainstorm that has to become concrete tickets |

Supporting skills, mostly called by the workflows for you:

| Skill | What it does |
| --- | --- |
| `/project-init` | onboard a repo: explore, scaffold docs, install test framework, migrate existing docs |
| `/open-sprint` · `/close-sprint` | plan a sprint and open its branch · wrap it up, update docs, merge |
| `/release` | changelog, version bump, security & data gate (backup, migration test), PR, CI, ship, tag — a production target only after you accept its risks |
| `spec-design` · `ui-design` | write the spec and test strategy · design the look, styleguide and mockups |
| `e2e` | end-to-end testing: grows a reusable test script, drives the app by agent only where a script can't |
| `maintain-docs` · `maintain-memory` | keep docs and memory current — and prune what's no longer true |
| `/checkpoint` | end a chat cleanly with a short handout the next chat picks up |
| `/domain-init` | build a domain bundle, e.g. for Unity or web frontends (see below) |
| `/dispatch-configurator` | find the agents and models on your machine that can take over single steps |

## What a project looks like

```
project/
├── AGENTS.md              ← the project guide for agents and contributors
├── backlog/               ← open work: one ticket per file, plus the backlog index
├── docs/                  ← lasting truth: behaviour, decisions, architecture, dev notes, release runbook
└── artefacts/{sprint}/    ← one folder per sprint: board, specs, test reports — frozen when it closes
```

You opt in per doc, so a small project stays small. Tickets are plain files that never move; a
board only lists them, and their line shows the status — **open · active · to test · done**.
When a sprint closes, finished tickets are deleted once their outcome lives in the docs, so the
backlog only ever holds live work.

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/skills-and-docs-dark.svg">
    <img src="assets/skills-and-docs.svg" alt="A ticket's journey from idea through Draft and Backlog onto the sprint board (open, active, to test, done), until its outcome moves into the docs and the file is deleted. Below, the four places everything lives: open work in backlog/, the sprint folder, the lasting docs in docs/, and memory, each file labelled with the skill that writes it." width="1000">
  </picture>
</p>

## Domains and memory

- **Domains** bundle what one kind of work needs — skills, agents, MCP servers, notes — for Unity,
  Android, web or even academic writing. They stay dormant until a project uses them, so the Unity
  tools only load in Unity repos. None ship with the repo; `/domain-init` builds the ones you need.
- **Memory** is plain Markdown at three levels — project, domain, global — kept lean and pruned by
  the agent. Machine-specific facts go to memory, general engineering knowledge to `docs/dev.md`.

## Install

> [!WARNING]
> **At your own risk, without warranty** ([MIT](LICENSE), [SECURITY.md](SECURITY.md)): the workspace
> drives agents that run commands, install third-party code and change files, git state and — via
> `/release` — live environments. It **replaces** your harness's global setup and is **not
> compatible** with other workspace frameworks or project doc layouts
> ([Compatibility](INSTALL.md#compatibility)). Nothing existing is overwritten without asking, and a
> backup is offered first.

Open Claude Code, Codex or OpenCode anywhere and say:

> Fetch https://raw.githubusercontent.com/voxlo-dev/Lean-Coding-Workspace/main/INSTALL.md and follow it.

The agent clones this repo, installs the workspace, sets up the required tools and asks a few
questions about you and your machine. **Restart your harness afterwards** — new skills load in a
fresh session. Prefer doing it by hand? Clone the repo and follow [`INSTALL.md`](INSTALL.md).

**You need:** Git and at least one of the three harnesses. The install adds
[codegraph](https://github.com/colbymchenry/codegraph) and [context7](https://github.com/upstash/context7),
and offers the `gh` CLI for pull requests and releases plus optional third-party
[skill bundles](.agents/skills/workspace-sync/references/bundles.md). Verified on Windows; Linux
and macOS commands are in place but unproven — reports welcome.

## Make it yours

- **Change the template, not the install.** Fork the repo, edit `workspace_TEMPLATE/`, and re-run
  the `workspace-sync` skill from your clone — it also repairs a broken install.
- **Your personal rules survive updates.** User info, system info and custom rules in the installed
  `AGENTS.md` are kept on every sync.
- **Another harness?** The repo's `harness-onboard` skill probes it and adds an adapter.

## Contributing & license

Fixes, adapters and generally useful improvements are welcome — personalised or niche features are
not; see [CONTRIBUTING.md](CONTRIBUTING.md). Working *on* the repo starts at [`AGENTS.md`](AGENTS.md).
Security notes and reporting: [SECURITY.md](SECURITY.md).

[MIT](LICENSE) © Leonard Müller
