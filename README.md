# Lean Coding Workspace

**Turn your coding agent into a teammate that follows a process.**

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Agent-agnostic](https://img.shields.io/badge/agent-agnostic-6b46c1)
![Plain Markdown](https://img.shields.io/badge/plain-Markdown-black)

A ready-made workspace for your AI coding agent: one lean instruction file plus a set of skills
that take every change from idea to shipped code the same way — shape, spec, build, test,
document, release.

## Quick start

Open your coding agent anywhere and paste:

```text
Fetch https://raw.githubusercontent.com/voxlo-dev/Lean-Coding-Workspace/main/INSTALL.md and follow it.
```

The agent installs everything, asks a few questions about you and your machine, and tells you when
to restart it. You need Git and an agent tool with a folder in [`adapters/`](adapters/). Prefer
doing it by hand? Follow [`INSTALL.md`](INSTALL.md). Verified on Windows; Linux and macOS reports welcome.

**Read [SECURITY.md](SECURITY.md) first.**

## Why you want it

- **Plain Markdown, no scripts.** Nothing to build, nothing running in the background — every file
  is an instruction your agent reads.
- **A curated toolkit, not a zoo.** Two helpers, picked for the job: [codegraph](https://github.com/colbymchenry/codegraph)
  answers "how does this work" from a code graph instead of reading half the repo, and
  [context7](https://github.com/upstash/context7) brings current library docs instead of stale recall.
- **Your workflow, your choice.** Use the built-in workflows, or add others from a curated
  [skill catalog](.agents/skills/workspace-sync/references/bundles.md) — superpowers, OpenSpec,
  BMAD and more install straight into the workspace. Every skill is yours to change.
- **Agile, the way you know it from work.** Backlog, sprints, a board, reviews and releases — the
  agent keeps the rhythm, you make the calls.
- **Nothing gets forgotten.** Knowledge moves up in stages — a short handout between chats, tickets
  for open work, a folder per sprint, lasting docs for what shipped, memory for the rest.
- **Domains on demand.** Specialist skills and knowledge for Unity, Android, web or academic writing
  load only in the projects that need them. Build your own with `/domain-init`.
- **Light on context — made for the small plan.** About 2.3k tokens are always loaded; everything
  else loads only when used, so a small subscription goes a long way.
- **Runs in any agent tool.** New tool? The `harness-onboard` skill lets your agent probe it and
  work out the differences by itself.
- **Clean split between repo and install.** This repo is the source you fork and edit; the
  installed workspace holds only what your agent actually reads. No dead weight — every file has a job.

## How a session goes

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/session-flow-dark.svg">
    <img src="assets/session-flow.svg" alt="Install once per machine and run /project-init once per project. Then sprints repeat: /open-sprint plans tickets and opens a branch; every new chat picks a workflow; /minimal-workflow handles small fixes, /dynamic-workflow takes a feature from an approved spec to tested, documented code; until the sprint scope is done the next run starts in a fresh chat, then /close-sprint updates the docs and merges. When a few sprints make a version, /release ships it, production only after you accept the risks." width="880">
  </picture>
</p>

## Where everything lives

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/skills-and-docs-dark.svg">
    <img src="assets/skills-and-docs.svg" alt="A ticket's journey from idea through Draft and Backlog onto the sprint board (open, active, to test, done), until its outcome moves into the docs and the file is deleted. Below, the four places everything lives: open work in backlog/, the sprint folder, the lasting docs in docs/, and memory, each file labelled with the skill that writes it." width="1000">
  </picture>
</p>

## Also in the box

| Skill | What it does |
| --- | --- |
| `/shape` | turns a fuzzy idea or brainstorm into concrete tickets |
| `spec-design` · `ui-design` | the spec and test strategy · look and feel, styleguide, mockups |
| `e2e` | end-to-end tests that grow into a reusable script |
| `maintain-docs` · `maintain-memory` | keep docs and memory current, and prune what's no longer true |
| `/checkpoint` | ends a chat with a short handout for the next one |
| `/domain-init` | builds a domain for one kind of work |
| `/dispatch-configurator` | lets other agents or local models take over single steps |

## Make it yours

Fork the repo, edit `workspace_TEMPLATE/`, and re-run the `workspace-sync` skill from your clone —
it also repairs a broken install. Your personal rules in the installed `AGENTS.md` survive every sync.

## Contributing & license

Fixes, adapters and generally useful improvements are welcome — personalised or niche features are
not; see [CONTRIBUTING.md](CONTRIBUTING.md). Working *on* the repo starts at [`AGENTS.md`](AGENTS.md).

[MIT](LICENSE) © Leonard Müller
