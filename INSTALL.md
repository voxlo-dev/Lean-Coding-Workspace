# Install Guide — Lean Coding Workspace

Installs the workspace into one or more agent harnesses on **this** machine. Followable by an agent
or by hand, and fetchable before anything is cloned — which is why it is a guide and not a skill.

Everything mechanical belongs to the `workspace-sync` skill (step 3); this guide holds the clone and
the once-only personalisation. Re-syncing later is that skill alone — come back here only for a
machine that has never had the workspace.

## 1. Prerequisites

- `git`, and at least one supported harness installed: **Claude Code** · **Codex** · **OpenCode**.
- Optional, for projects on a PR flow: the `gh` CLI (step 3 installs and authenticates it if asked).

## 2. Clone

```bash
git clone https://github.com/voxlo-dev/Lean-Coding-Workspace lean-coding-workspace
cd lean-coding-workspace
```

Already in a clone → skip. Everything below runs **from the clone's root**.

## 3. Sync the workspace

> Read `.agents/skills/workspace-sync/SKILL.md` and follow it.

Stop where it stops: `gh auth login` and the harness restart are the user's to do, and skills are
discovered on session start, so **restart each harness before relying on them.**

## 4. Personalise `AGENTS.md` — once per machine

`{home}/AGENTS.md` (`~/.claude` · `~/.codex` · `~/.config/opencode`) is loaded in every session of
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
first real use. Then open a project and run `project-initialiser` on it.

## If something is off

- A capability that installed but exposes nothing is the usual failure — re-run `workspace-sync` as
  in step 3 and let its verification name what is broken. It is a repo skill: a harness that reads
  only its own skills directory finds it by name after you link it there, and by path always.
- Editing an *installed* file is never the fix: installs are overwritten on every sync. Change
  `workspace_TEMPLATE/` in this clone and sync again.
