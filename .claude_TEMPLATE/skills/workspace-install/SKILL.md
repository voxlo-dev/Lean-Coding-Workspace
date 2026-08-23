---
name: workspace-install
description: "Use to bootstrap or repair the global workspace: copy the template into ~/.claude, collect system + user info into the global CLAUDE.md, install the mandatory plugins, verify them, and capture the user's working rules. Explicit-invoke."
disable-model-invocation: true
---

# Workspace Install

Bootstrap or repair `~/.claude` from this template, then personalise it. **Copy only what's missing and merge the rest by hand**, so existing user files survive. Pause where the user must act (steps 5, 8).

**Bootstrap note:** on a fresh machine this skill doesn't live in `~/.claude` yet, so the very first run is Read from the workspace and followed by hand. That's expected.

**`{workspace}`** = the directory holding `.claude_TEMPLATE` (usually the cwd). Substitute the real path.

**Fresh vs repair:** everything below is idempotent — **skip any step whose result already holds** (file correct, plugin working) and act on what's missing or broken. Tell the user which steps you skip and why.

## 1. Inventory & copy the template into `~/.claude`

- `diff -r --strip-trailing-cr` the template against `~/.claude` (live copies may carry different line endings). Missing → a fresh copy, differing → a merge candidate; the inventory itself tells you whether this is a bootstrap or a repair. Report it first.
- Copy everything missing, leaving existing files untouched (`-n` = no-clobber; run from `{workspace}` or use absolute paths):

  ```bash
  cp -rn {workspace}/.claude_TEMPLATE/. ~/.claude/
  ```

- **On a repair, `-n` is not enough** — a skill whose template version changed keeps the old installed copy. Two kinds of file:
  - **workspace-owned** — `skills/`, `project_TEMPLATE/`: overwrite from the template (`cp -r`, no `-n`). A user edit inside the installed copy is lost **by design**; real customisations belong in the workspace repo. Deletions need doing explicitly — a skill renamed or dropped in the template leaves its old folder behind and still loads.
  - **user-owned** — `memory/`, `projects/`, `domains/`, `settings.json`, `.mcp.json`: leave them alone. A domain master is generated, not templated (`domain-initialiser` rebuilds one on request).
- **`CLAUDE.md` is always a manual merge:** take the template's structural changes (new sections, reworded rules), keep the user-filled ones — **User Info**, **System Info**, custom **RULES**, **Available masters**.
- New skill *folders* are usually discovered only next session — say so rather than claiming they're live.

## 2. Collect system info → global CLAUDE.md

Detect OS + version, CPU/RAM/GPU, and the default dev environment (shells, installed languages/runtimes, editors). Write a short summary into **System Info** in `~/.claude/CLAUDE.md`. (Repair: only if stale.)

## 3. Interview the user → global CLAUDE.md

Ask in plain chat — **not** the question tool — so the user can answer freely or skip. One short message covering: preferred spoken language · role / job · experience level and strong areas · favourite languages, frameworks, tools.

**"No answer" is always fine.** Pause for the reply, then write what they gave into **User Info**, skipping anything declined. (Repair: skip if already filled.)

## 4. Install the mandatory plugins

Only the ones not already working, each via its own installer / README:

- **codegraph** — https://github.com/colbymchenry/codegraph
- **superpowers**, **context7**, **plugin-dev** — from the `claude-plugins-official` marketplace (add via `/plugin`).

Long-term memory is **native** (no plugin) — step 1 already seeded `~/.claude/memory/MEMORY.md` and its `@import` in `CLAUDE.md`. See `maintain-memory`.

## 4a. GitHub access *(optional — ask, don't assume)*

Needed by `release` and any project on the PR flow; skip for a user working purely locally. Both halves or neither:

- **`gh` CLI** — `winget install --id GitHub.cli` / `brew install gh` / per distro. Then **the user runs `gh auth login`**: interactive and browser-based, so pause here.
- **`github` plugin** — same marketplace, but only a wrapper around a remote MCP server authenticating via `GITHUB_PERSONAL_ACCESS_TOKEN`. **Without that variable it silently exposes zero tools.** Cheapest source is the login just done: `setx GITHUB_PERSONAL_ACCESS_TOKEN "$(gh auth token)"` / shell-profile equivalent. Say plainly it lands in the environment in clear text; offer a scoped PAT instead.

## 5. Restart — pause

**Only if step 4 or 4a changed anything.** Ask the user to restart Claude Code (and the terminal), then resume; **stop here** until they confirm. A new environment variable needs the restart too, or the MCP server starts unauthenticated. Nothing changed → say so and skip.

## 6. Verify the plugins

Confirm each mandatory plugin is actually **working**, not merely present: its skills/commands/MCP tools are discoverable this session, and its entry point runs (no failing hook, no error on invoke).

Report what passed and failed. For any failure propose a brief troubleshooting plan and **get the user's OK before any tool calls**. Two traps behind a plugin that is "installed" yet silently exposes nothing: an orphaned or dependency-incomplete cache directory shadowing the working one, and a remote MCP server whose credential is missing — `enabledPlugins: true` in `settings.json` says nothing about either. For **github** specifically, `get_me` returning your account is the proof; `gh auth status` is the separate one.

## 7. Offer to capture working rules → global CLAUDE.md (optional)

The install is essentially complete. **Ask whether the user wants further rules** on language, version control (commit/branch/PR style), and code style. Yes → collect them in plain chat and fill the matching **RULES** subsections. No → keep the template defaults.

## 8. Done — pause for review

Summarise what was installed/changed and what was written into `CLAUDE.md`. Ask the user to review and adjust before first use.
