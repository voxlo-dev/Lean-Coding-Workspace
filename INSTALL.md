# Install Guide — Lean Coding Workspace

Installs the workspace into one or more agent harnesses on **this** machine. Followable by an agent
or by hand, before anything is installed — which is why it is a guide and not a skill. **Read it
whole, from the clone or a raw download** (`curl -fsSL`), never through a summarising web fetch: a
summary drops the steps.

**Agent: go ahead without asking** through steps 1–2 and the sync's inventory — they only read and
clone. Every write that touches an existing setup is asked first, with a backup offered.

Everything mechanical belongs to the `workspace-sync` skill (step 3); this guide holds the clone and
the once-only personalisation. Re-syncing later is that skill alone — come back here only for a
machine that has never had the workspace.

**At your own risk.** The workspace drives agents that run commands, install third-party skills and
MCP servers, and change files and git state. It ships without warranty (`LICENSE`); review what an
agent proposes before approving it.

## Compatibility

The workspace **takes the place of** a harness's global setup rather than sitting beside one —
installing it never destroys that setup (see below). It is not compatible with another workspace framework (a hand-grown global `CLAUDE.md` / `AGENTS.md` rule set,
a workflow plugin with session hooks such as superpowers, BMAD or spec-kit installed whole) nor with
a project doc layout of its own: `project-init` migrates a project's docs into this workspace's
`docs/` · `backlog/` · `artefacts/` split, with your OK, and there is no way back but git.

Nothing is overwritten silently: the sync lists every file it did not write, offers a dated backup
before its first write, and asks per file — keep, merge, or replace.

## 1. Prerequisites

- `git`, and at least one harness with a folder in `adapters/` — its `MANIFEST.md` says what is verified.
  Another goes through the repo's `harness-onboard` skill first.
- Optional, for projects on a PR flow: the `gh` CLI (step 3 installs and authenticates it if asked).

## 2. Clone

```bash
git clone https://github.com/voxlo-dev/Lean-Coding-Workspace lean-coding-workspace
cd lean-coding-workspace
```

Already in a clone → skip. Everything below runs **from the clone's root**.

## 3. Sync the workspace

> Read `.agents/skills/workspace-sync/SKILL.md` and follow it.

A harness whose manifest's **Project → Skill roots** row lacks `.agents/skills/` finds the repo's
own skills only through a link per folder, made once per clone — git cannot track it:
`ln -s ../../.agents/skills/{name} {project-agent-dir}skills/{name}` (Windows:
`mklink /J {project-agent-dir}skills\{name} .agents\skills\{name}`).

Take the backup it offers on a machine with any existing harness setup. Stop where it stops: `gh auth login` and the harness restart are the user's to do, and skills are
discovered on session start, so **restart each harness before relying on them.**

## 4. Personalise `AGENTS.md` — once per machine

`{home}/AGENTS.md` (`{home}` per the manifest's **Home** row) is loaded in every session of
that harness. It ships with three sections still empty; fill them once and a later sync preserves
them. Keep every answer short — this text is paid for in every session, forever.

- **System Info** — detect it, don't ask: OS + version, CPU / RAM / GPU, installed runtimes and
  languages, shells, editors. A few lines.
- **User Info** — ask in plain chat, **never** as a multiple-choice tool call, so the user can answer
  freely or decline: preferred spoken language · role · experience level and strong areas · favourite
  languages, frameworks and tools. **"No answer" is always fine** — write only what was given.
- **RULES** — offer, don't impose: further rules on language, version control (commit, branch and PR
  style) and code style. Declined → the template defaults stand.

Several harnesses each have their own `AGENTS.md`; write the same content into each.

## 5. Review

Show the user what landed per harness and what went into `AGENTS.md`, and let them adjust before
first real use. Then open a project and run `project-init` on it.

## If something is off

- Clone or download blocked (a sandbox or proxy denying `github.com`) → the user allows `github.com`
  and `raw.githubusercontent.com` in that sandbox's network settings; nothing here works around it.
- A capability that installed but exposes nothing is the usual failure — re-run `workspace-sync` as
  in step 3 and let its verification name what is broken. It is a repo skill: found by name once
  linked per step 3, by path always.
- Editing an *installed* file is never the fix: installs are overwritten on every sync. Change
  `workspace_TEMPLATE/` in this clone and sync again.
