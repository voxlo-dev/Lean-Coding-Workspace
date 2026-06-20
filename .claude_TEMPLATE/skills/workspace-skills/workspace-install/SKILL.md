---
name: workspace-install
description: "Use to bootstrap or repair the global workspace: copy the template into ~/.claude, collect system + user info into the global CLAUDE.md, install the four mandatory plugins, verify them, and capture the user's working rules. Explicit-invoke."
disable-model-invocation: true
---

# Workspace Install

Bootstrap `~/.claude` from this template, then personalise it. **Never clobber existing user files** — copy only what's missing and merge the rest by hand. Pause where the user must act (steps 5, 8).

## 1. Copy the template into `~/.claude`

- Inventory first: list which template files already exist in `~/.claude` (those are the only merge candidates).
- Copy everything missing, leave existing files untouched:

  ```bash
  cp -rn {workspace}/.claude_TEMPLATE/. ~/.claude/
  ```

  (`-n` = no-clobber. Run from the workspace, or use the absolute path.)
- For files that exist in both **and** differ, `diff` each and merge by hand. The global `CLAUDE.md` is always a manual merge — it carries collected info from the steps below.

## 2. Collect system info → global CLAUDE.md

Detect OS + version, CPU/RAM/GPU, and the default dev environment (shell, primary languages/runtimes installed, editor). Write a short summary into the **System Info** section of `~/.claude/CLAUDE.md`.

## 3. Interview the user → global CLAUDE.md

Ask briefly — one pass, **"no answer" always allowed** for any item:

- role / job
- experience level and strong areas
- favourite languages, frameworks, tools

Write what they give into the **User Info** section. Skip anything they decline.

## 4. Install the mandatory plugins

Each via its own installer / README:

- **superpowers** — https://github.com/obra/superpowers
- **codegraph** — https://github.com/colbymchenry/codegraph
- **claude-mem** — https://github.com/thedotmack/claude-mem
- **headroom** — https://github.com/chopratejas/headroom

## 5. Restart — pause

Ask the user to restart Claude Code (and the terminal), then resume. **Stop here** until they confirm.

## 6. Verify the plugins

Confirm each of the four loaded; report which succeeded and which failed. For any failure, start brief troubleshooting — but get the user's OK before any tool calls.

## 7. Collect working rules → global CLAUDE.md (optional)

Offer to capture the user's defaults on: language, version control (commit/branch/PR style), and code style. Skippable — if they decline, keep the template defaults. Otherwise fill the matching **RULES** subsections.

## 8. Done — pause for review

Summarise what was installed and what was written into `CLAUDE.md`. Ask the user to review and adjust before first use.
