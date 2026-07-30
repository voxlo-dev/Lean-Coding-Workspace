---
name: workspace-install
description: "Use to bootstrap or repair the global workspace: copy the template into ~/.claude, collect system + user info into the global CLAUDE.md, install the mandatory plugins, verify them, and capture the user's working rules. Explicit-invoke."
disable-model-invocation: true
---

# Workspace Install

Bootstrap or repair `~/.claude` from this template, then personalise it. **Never clobber existing user files** — copy only what's missing and merge the rest by hand. Pause where the user must act (steps 5, 8).

**Bootstrap note:** on a fresh machine this skill does not yet live in `~/.claude`, so the very first run can't be a normal `Skill` invocation — it's Read from the workspace and followed by hand. That's expected.

**`{workspace}`** = the directory holding `.claude_TEMPLATE` (usually the current working directory). Substitute the real path.

**Fresh vs repair:** start by inventorying current state (step 1). Everything below is idempotent — **skip any step whose result already holds** (file already correct, plugin already working) and only act on what's missing or broken. Tell the user which steps you're skipping and why.

## 1. Inventory & copy the template into `~/.claude`

- `diff -r --strip-trailing-cr` the template against `~/.claude` (live copies may carry different line endings). What's missing is a fresh copy, what differs is a merge candidate — and the inventory itself tells you whether this is a bootstrap or a repair. Report it before touching anything.
- Copy everything missing, leave existing files untouched:

  ```bash
  cp -rn {workspace}/.claude_TEMPLATE/. ~/.claude/
  ```

  (`-n` = no-clobber. Run from `{workspace}`, or use the absolute path.)
- **On a repair, `-n` is not enough** — a skill whose template version changed keeps the old installed copy. Two kinds of file, treat them differently:
  - **workspace-owned** — `skills/`, `project_TEMPLATE/`: overwrite from the template (`cp -r` without `-n`). A user edit inside the installed copy is lost **by design**; real customisations belong in the workspace repo. Deletions don't happen by themselves — a skill renamed or dropped in the template leaves its old folder behind and still loads, so remove those explicitly.
  - **user-owned** — `memory/`, `projects/`, `domains/`, `settings.json`, `.mcp.json`: never touch. A domain master is generated, not templated (`domain-initialiser` rebuilds one on request).
- **`CLAUDE.md` is always a manual merge**, never a copy: take the template's structural changes (new sections, reworded rules) and leave the user-filled ones alone — **User Info**, **System Info**, custom **RULES**, **Available masters**.
- New skill *folders* are usually discovered only in the next session — say so rather than claiming they're live.

## 2. Collect system info → global CLAUDE.md

Detect OS + version, CPU/RAM/GPU, and the default dev environment (shells, primary languages/runtimes installed, editors). Write a short summary into the **System Info** section of `~/.claude/CLAUDE.md`. (Repair: only update if stale.)

## 3. Interview the user → global CLAUDE.md

Ask in plain chat — **not** the question tool — so the user can answer freely or skip. One short message covering:

- preferred spoken language
- role / job
- experience level and strong areas
- favourite programming languages, frameworks, tools

**"No answer" is always fine.** Pause for their reply, then write what they gave into the **User Info** section. Skip anything they decline. (Repair: skip if already filled.)

## 4. Install the mandatory plugins

Install only the ones not already working. Each via its own installer / README:

- **codegraph** — https://github.com/colbymchenry/codegraph
- **superpowers**, **context7**, **github**, **plugin-dev** — from the `claude-plugins-official` marketplace (add via `/plugin`; `context7`/`github` are MCP-backed, `plugin-dev` is the authoring toolkit)

Long-term memory is **native** (no plugin) — step 1 already seeded `~/.claude/memory/MEMORY.md` and its `@import` in `CLAUDE.md`. See the `maintain-memory` skill.

## 5. Restart — pause

**Only if step 4 installed or changed anything.** Ask the user to restart Claude Code (and the terminal), then resume; **stop here** until they confirm. If nothing changed (all plugins were already present), say so and skip the restart.

## 6. Verify the plugins

Confirm each mandatory plugin is actually **working**, not merely present:

- its skills/commands/MCP tools are discoverable this session, and
- its entry point runs (no failing hook, no error on invoke).

Report which passed and which failed. For any failure, propose a brief troubleshooting plan and **get the user's OK before any tool calls**. Common trap: an orphaned or dependency-incomplete plugin-cache directory shadowing the working one — check the cache for stale/duplicate versions when a plugin is "installed" but its hook or tools silently fail.

## 7. Offer to capture working rules → global CLAUDE.md (optional)

The install is essentially complete after step 6. Now just **ask whether the user wants to define further rules** on: language, version control (commit/branch/PR style), and code style. If yes, collect them in plain chat and fill the matching **RULES** subsections. If no, keep the template defaults and move on.

## 8. Done — pause for review

Summarise what was installed/changed and what was written into `CLAUDE.md`. Ask the user to review and adjust before first use.
