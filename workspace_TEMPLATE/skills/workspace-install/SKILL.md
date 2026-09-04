---
name: workspace-install
description: "Use to bootstrap or repair the global workspace in one or more harnesses: copy the shared template, apply the target's adapter, collect system + user info into AGENTS.md, install the required capabilities, verify them. Explicit-invoke."
disable-model-invocation: true
---

# Workspace Install

Install or repair **only the targets the user selects**. Copy what's missing, merge instructions, never touch user-owned state. Pause where the user must act (steps 6, 8).

**Bootstrap note:** on a fresh machine this skill isn't installed yet, so the first run is Read from the workspace and followed by hand. That's expected.

**`{workspace}`** = the repo holding `workspace_TEMPLATE/` and `adapters/` (usually the cwd). Substitute the real path.

**Two copies, that's the whole install.** `workspace_TEMPLATE/` is harness-neutral and goes in wholesale; `adapters/{target}/` is a plain overlay whose files already sit at the paths they must land on. Everything a target needs later — the project shim, the domain manifests — lands under `{home}/adapter/`, so no other skill ever names a harness.

**Fresh vs repair:** everything below is idempotent — **skip any step whose result already holds** (file correct, capability working) and act on what's missing or broken. Tell the user which steps you skip and why.

## 1. Select targets — ask

One multi-select question: **Claude Code · Codex · OpenCode**. An unselected target is not touched. Skip any whose home doesn't exist unless the user wants it created — installing a harness the user doesn't have is noise, not service.

| Target | Home | Global instructions | Skills | Agents | Project dir | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Claude Code | `~/.claude` | `CLAUDE.md` shim → `AGENTS.md` | `skills/` | `agents/`, ID from `name:` | `.claude/` | verified |
| Codex | `~/.codex` | `AGENTS.md` (native, no imports) | `skills/` | none found | `.codex/` | partial |
| OpenCode | `~/.config/opencode` | unconfirmed | reads `~/.claude/skills/` | `agents/`, ID from path | `.opencode/` | unverified |

**Never guess a path.** A row marked *partial* or *unverified* gets asked, not assumed, and an existing `adapters/{target}/` folder is the proof a row is real — **no folder, no install**. Two consequences to state out loud rather than work around:

- **Codex has no agent directory**, so `localagent-workflow` cannot run there — there is nothing to dispatch to, and one context doing every step is the failure that workflow exists to prevent. It also resolves no `@` imports, which is why its global memory is inlined (step 3) and its per-project domain memory and checkpoint stay manual.
- **OpenCode is unverified and ships no adapter.** On a machine that also has Claude Code it already reads `~/.claude/skills/` directly, so the skills work with no OpenCode-side copy at all. Offer the target only to ask the user for its paths, record what they confirm in the **workspace repo**, and skip it this run.

Below, `{home}` and `{project-agent-dir}` mean the selected row's values.

## 2. Inventory & copy the shared template

- `diff -r --strip-trailing-cr` `{workspace}/workspace_TEMPLATE` against `{home}` (live copies may carry different line endings). Missing → a fresh copy, differing → a merge candidate; the inventory tells you whether this is a bootstrap or a repair. Report it before changing anything.
- Copy everything missing, leaving existing files untouched (`-n` = no-clobber; run from `{workspace}` or use absolute paths):

  ```bash
  cp -rn {workspace}/workspace_TEMPLATE/. {home}/
  ```

- **On a repair, `-n` is not enough** — a skill whose template version changed keeps the old installed copy. Two kinds of file:
  - **workspace-owned** — `skills/`, `agents/`, `project_TEMPLATE/`, `adapter/`: overwrite from the template or the overlay (`cp -r`, no `-n`). A user edit inside the installed copy is lost **by design**; real customisations belong in the workspace repo. Deletions need doing explicitly — a skill or agent renamed, moved or dropped in the template leaves its old copy behind and keeps loading; check for a stale *home* too, not just a stale file.
  - **user-owned** — `memory/`, `projects/`, `domains/`, and the target's own configuration (`settings.json`, `config.toml`, `opencode.jsonc`, `.mcp.json`): leave them alone. A domain master is generated, not templated (`domain-initialiser` rebuilds one on request).
- **`AGENTS.md` is always a manual merge:** take the template's structural changes (new sections, reworded rules), keep the user-filled ones — **User Info**, **System Info**, custom **RULES**, **Available masters**.
- **Agents install flat**, whatever the source layout: OpenCode folds a subfolder into the agent's ID while Claude Code keys off `name:`, so a nested copy answers to a different name in each. Nothing else may live in `agents/` — a stray file is scanned as an agent.
- New skill *folders* are usually discovered only next session — say so rather than claiming they're live.

## 3. Overlay the target's adapter

Its files already carry the paths they must land on, so this is a copy, not a transform:

```bash
cp -r {workspace}/adapters/{target}/. {home}/
```

That leaves `{home}/adapter/` holding whatever the target needs later — `project/` for `project-initialiser`, `domain/` for `domain-initialiser` — which is why neither of them names a harness. A target needing nothing there simply has no such folder, and both skills treat that as "no shim required".

Two things the overlay cannot do on its own:

- **Claude Code** — `{home}/CLAUDE.md` may already exist. It is a shim, so a conflict means the user put rules in the wrong file: move them into `AGENTS.md` rather than keeping two homes.
- **Codex** — merge `{home}/adapter/config.toml.fragment` into `{home}/config.toml`, keeping existing keys. Then **inline** the global memory index into `{home}/AGENTS.md` between `<!-- workspace:memory:begin -->` and `<!-- workspace:memory:end -->`, since Codex resolves no imports, and re-sync that block on every repair.

## 4. Collect system info → `AGENTS.md`

Detect OS + version, CPU/RAM/GPU, and the default dev environment (shells, installed languages/runtimes, editors). Write a short summary into **System Info**. (Repair: only if stale.)

## 5. Interview the user → `AGENTS.md`

Ask in plain chat — **not** the question tool — so the user can answer freely or skip. One short message covering: preferred spoken language · role / job · experience level and strong areas · favourite languages, frameworks, tools.

**"No answer" is always fine.** Pause for the reply, then write what they gave into **User Info**, skipping anything declined. (Repair: skip if already filled.)

## 6. Install the required capabilities

Only the ones not already working, and only where the target can host them. Memory is **native Markdown, no plugin** — step 2 seeded `{home}/memory/MEMORY.md` and step 3 wired it. See `maintain-memory`.

- **Claude Code** — **codegraph** from https://github.com/colbymchenry/codegraph; **superpowers**, **context7**, **plugin-dev** from the `claude-plugins-official` marketplace (add via `/plugin`).
- **Codex** — the same marketplace works: add it, then the three plugins, via `~/.codex/config.toml`. codegraph goes in as an `mcp_servers` entry, not as a plugin.
- **OpenCode** — unconfirmed; report as unavailable rather than inventing an install path.

### GitHub access *(optional — ask, don't assume)*

Needed by `release` and any project on the PR flow; skip for a user working purely locally. Both halves or neither:

- **`gh` CLI** — `winget install --id GitHub.cli` / `brew install gh` / per distro. Then **the user runs `gh auth login`**: interactive and browser-based, so pause here.
- **`github` plugin** — a wrapper around a remote MCP server authenticating via `GITHUB_PERSONAL_ACCESS_TOKEN`. **Without that variable it silently exposes zero tools.** Cheapest source is the login just done: `setx GITHUB_PERSONAL_ACCESS_TOKEN "$(gh auth token)"` / shell-profile equivalent. Say plainly it lands in the environment in clear text; offer a scoped PAT instead.

## 7. Restart — pause

**Only if step 6 changed anything.** Ask the user to restart each affected harness (and the terminal), then resume; **stop here** until they confirm. A new environment variable needs the restart too, or the MCP server starts unauthenticated. Nothing changed → say so and skip.

## 8. Verify each target independently

Per selected target, confirm each capability is actually **working**, not merely present: global instructions load, skills are discoverable this session, agents register where the target supports them, MCP/plugin entry points run (no failing hook, no error on invoke), memory references resolve, and domains stay inert until `project-initialiser` installs one.

Report each target as **passed · skipped · failed**. For any failure propose a brief troubleshooting plan and **get the user's OK before any tool calls**. Two traps behind a capability that is "installed" yet silently exposes nothing: an orphaned or dependency-incomplete cache directory shadowing the working one, and a remote MCP server whose credential is missing — `enabledPlugins: true` in `settings.json` says nothing about either. For **github** specifically, `get_me` returning your account is the proof; `gh auth status` is the separate one.

**Never report a scope as loaded because its file exists.** Where a target cannot load memory automatically, say the file is stored and manual.

## 9. Offer to capture working rules → `AGENTS.md` (optional)

The install is essentially complete. **Ask whether the user wants further rules** on language, version control (commit/branch/PR style), and code style. Yes → collect them in plain chat and fill the matching **RULES** subsections. No → keep the template defaults.

## 10. Done — pause for review

Summarise per target what was installed/changed and what was written into `AGENTS.md`. Ask the user to review and adjust before first use.
