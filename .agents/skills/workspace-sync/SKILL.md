---
name: workspace-sync
description: "Use to sync or repair the global workspace in one or more harnesses: copy the shared template, apply the target's adapter, install the required capabilities and the picked skill bundles, verify them. Runs from the workspace repo; the first install on a machine comes through INSTALL.md. Explicit-invoke."
disable-model-invocation: true
---

# Workspace Sync

Sync **only the targets the user selects**. Copy what's missing, merge instructions, never touch user-owned state. Pause where the user must act (steps 4–6).

Its inputs are `workspace_TEMPLATE/` and `adapters/`, so it runs from the workspace repo and nowhere else. **`{workspace}`** = that clone (usually the cwd). Substitute the real path.

**Fresh vs repair is not a mode** — everything below is idempotent: **skip any step whose result already holds** (file correct, capability working), act on what's missing or broken, and say which steps you skipped. The once-only half of a first install — system info, the user interview, the optional rules — belongs to `{workspace}/INSTALL.md`: a target with unfilled **User Info** or **System Info** gets pointed back there, never interviewed here.

## 1. Select targets — ask

One multi-select question: **Claude Code · Codex · OpenCode**. An unselected target is not touched. Skip any whose home doesn't exist unless the user wants it created — installing a harness the user doesn't have is noise, not service.

| Target | Home | Project config | Global instructions pulled in by | Reaches `~/.agents/skills/` | Agents |
| --- | --- | --- | --- | --- | --- |
| Claude Code | `~/.claude` | `.claude/settings.json` | `CLAUDE.md` shim, `@` imports | **no** — needs per-skill links | `agents/*.md`, ID from `name:` |
| Codex | `~/.codex` | `.codex/config.toml` *(trusted projects only)* | `AGENTS.md`, no imports | yes, natively | `agents/*.toml`, needs a transform |
| OpenCode | `~/.config/opencode` | `opencode.json` | `instructions` array in config | yes, natively | `agent/*.md` — singular — ID from path |

Every row is implemented in `adapters/{target}/` — **no folder, no install**, and a claim not backed by a file there gets asked rather than assumed. Three consequences to state out loud rather than work around:

- **Codex agents are TOML, not Markdown.** `~/.codex/agents/{name}.toml` (or `.codex/agents/` per repo) with `name`, `description`, `developer_instructions` carrying the prompt body in a `'''` literal string — so the shared `.md` definitions need converting, not copying, and it is the one place the install is not a plain overlay. Convert on install and say you did; **read and write UTF-8 explicitly** — a default-codepage read turns every `—` into `â€”`. Codex lists each converted agent as an `agent_type` role on `spawn_agent`, but only a real dispatch proves one runs.
- **Codex silently truncates instructions at `project_doc_max_bytes` (32 KiB default).** Inlined memory eats that budget with no warning. Check the size after inlining, and raise the key in `config.toml` rather than letting the tail of `AGENTS.md` vanish.
- **`~/.agents/skills/` is every target's skills home** (repo scope: `.agents/skills/`) — the Agent Skills standard, read natively by Codex, OpenCode, Gemini CLI and Cursor. Claude Code is the holdout, reading only `~/.claude/skills/`, and gets **links, never a second copy**: a copy is what let the installed skills drift into two mangled versions.

Below, `{home}` and `{project-agent-dir}` mean the selected row's values.

## 2. Inventory & copy the shared template

- `diff -r --strip-trailing-cr` `{workspace}/workspace_TEMPLATE` against `{home}` (live copies may carry different line endings). Missing → a fresh copy, differing → a merge candidate; the inventory tells you whether this is a bootstrap or a repair. Report it before changing anything.
- Copy everything missing, leaving existing files untouched (`-n` = no-clobber; run from `{workspace}` or use absolute paths). **Almost everything goes to the shared home once, whatever targets were picked; only the instruction file and the agents are per target:**

  ```bash
  cp -rn {workspace}/workspace_TEMPLATE/{skills,memory,domains,project_TEMPLATE} ~/.agents/
  cp -rn {workspace}/workspace_TEMPLATE/{AGENTS.md,agents} {home}/
  ```

  `~/.agents/DISPATCH-GUIDE.md` is never written here: it describes one machine, so the user runs
  `/dispatch-configurator` for it. Absent = no dispatch configured, a valid state the workflows
  handle.

- Then link each skill folder into the directory a non-standard target does read — `ln -s ~/.agents/skills/{name} {home}/skills/{name}`, `mklink /J` on Windows. **Per folder, never the `skills/` directory itself**, which the harness writes its own internals into. A real directory where a link belongs is the old duplicated install: diff it against the template, salvage what only it has, replace it.
- **On a repair, `-n` is not enough** — a skill whose template version changed keeps the old installed copy. Two kinds of file:
  - **workspace-owned** — `~/.agents/{skills,domains/domain_TEMPLATE,project_TEMPLATE}/`, plus `{home}`'s `agents/` and `adapter/`: overwrite from the template or the overlay (`cp -r`, no `-n`). A user edit inside the installed copy is lost **by design**; real customisations belong in the workspace repo. Deletions need doing explicitly — a skill or agent renamed, moved or dropped in the template leaves its old copy behind and keeps loading; check for a stale *home* too, not just a stale file. A name `BUNDLES.md` records is step 5's, never stale here.
  - **user-owned** — `~/.agents/memory/`, `~/.agents/DISPATCH-GUIDE.md`, `~/.agents/BUNDLES.md`, the domain *masters* beside their template, `{home}/projects/`, and the target's own configuration (`settings.json`, `config.toml`, `opencode.jsonc`, `.mcp.json`): leave them alone. A master is generated, not templated (`domain-init` rebuilds one on request).
- **`AGENTS.md` is always a manual merge:** take the template's structural changes (new sections, reworded rules), keep the user-filled ones — **User Info**, **System Info**, custom **RULES**, **Available masters** — the bundle rows are step 5's.
- **Agents install flat**, whatever the source layout: OpenCode folds a subfolder into the agent's ID while Claude Code keys off `name:`, so a nested copy answers to a different name in each. Convert rather than skip where the format differs, one file per agent ID. Nothing else may live in `agents/` — a stray file is scanned as an agent.
- New skill *folders* are usually discovered only next session — say so rather than claiming they're live.

## 3. Overlay the target's adapter

Its files already carry the paths they must land on, so this is a copy, not a transform:

```bash
cp -r {workspace}/adapters/{target}/. {home}/
```

That leaves `{home}/adapter/` holding whatever the target needs later, in three folders named by what happens to them:

| Folder | Fate | Used by |
| --- | --- | --- |
| `project/` | copied into a repo root as-is | `project-init` |
| `project-merge/` | merged into the repo's **Project config** from the table above | `project-init` |
| `merge/` | merged into `{home}`'s own config | this skill, below |

A folder absent from a target's adapter means that target lacks that capability; report it as
missing rather than working around it. A merge keeps keys already present and substitutes any `{x}`. Then finish the two things a copy cannot do:

- **`merge/*`** into `{home}`'s own config — capability entries and the instruction paths it loads.
- **Point the target at `~/.agents/memory/MEMORY.md`** — an import in its instruction file, or an entry in its config's instructions list. **Never inline a copy**: it is a cache the next memory write strands, so a target with neither lever gets global memory reported as **read-on-demand, not in context** instead (`maintain-memory` owns the rule).

For Claude Code specifically, `{home}/CLAUDE.md` may already exist. It is a shim, so a conflict means the user put rules in the wrong file: move them into `AGENTS.md` rather than keeping two homes.

## 4. Install the required capabilities

Only the ones not already working, and only where the target can host them. Memory is **native Markdown, no plugin** — step 2 seeded `~/.agents/memory/MEMORY.md` and step 3 pointed the target at it or declared it missing. See `maintain-memory`.

- **Claude Code** — **codegraph** from https://github.com/colbymchenry/codegraph; **context7** from the `claude-plugins-official` marketplace (add via `/plugin`).
- **Codex** — the same marketplace works, as `[marketplaces.claude-plugins-official]` with `source_type = "git"`, then one `[plugins."{name}@claude-plugins-official"]` block each. codegraph goes in as an `mcp_servers` entry, not as a plugin.
- **OpenCode** — everything through `mcp`; its `plugin` list is JS modules, a different thing entirely.

### GitHub access *(optional — ask, don't assume)*

Needed by `release` and any project on the PR flow; skip for a user working purely locally. Both halves or neither:

- **`gh` CLI** — `winget install --id GitHub.cli` / `brew install gh` / per distro. Then **the user runs `gh auth login`**: interactive and browser-based, so pause here.
- **`github` plugin** — a wrapper around a remote MCP server authenticating via `GITHUB_PERSONAL_ACCESS_TOKEN`. **Without that variable it silently exposes zero tools.** Cheapest source is the login just done: `setx GITHUB_PERSONAL_ACCESS_TOKEN "$(gh auth token)"` / shell-profile equivalent. Say plainly it lands in the environment in clear text; offer a scoped PAT instead.

## 5. Skill bundles *(optional)*

Third-party skill sets, peers of the workspace: **no workspace skill ever invokes one**, so any can be dropped. Catalog: `references/bundles.md` beside this skill. The user's pick: `~/.agents/BUNDLES.md`, user-owned — `| id | source | kind | ref | installed |`, no rows = declined.

- **Pick** — manifest missing, or the user asks to change it → one multi-select question from the catalog rows (`id` — *pick when*), "Other" taking any repo URL; write the manifest. Otherwise take it as it stands, and skip an entry whose `ref` equals the source's current HEAD.
- **Fetch** the source shallow into scratch and install **only the row's *take***: skill folders flat into `~/.agents/skills/`, linked like step 2's; agents flat per step 2's conversion rules; a `plugin` row through the target's plugin mechanism, reported missing on a target without one. **Never** hooks, commands, rules, plugin manifests, or a skill whose description loads it every session — always-loaded context overrides the workflow gate. An "Other" source gets the same filter, its *take* agreed with the user first.
- **Collisions** — a name a workspace skill or agent, another entry or a harness built-in command already holds → ask: skip, or install as `{id}-{name}`. Never overwrite.
- **Needs** → install with the user's OK; declined → report the entry as installed but inert. Per-project setup stays with the bundle's entry skill, never run here.
- **Record** the fetched `ref` and every installed name. An entry dropped from the manifest → delete exactly its recorded names, links included.
- **Gate rows** — each `workflow` entry gets `| {entry} | {pick when} ({id} bundle) |` at the `{bundle workflows}` row of each target's `AGENTS.md`; none → drop that row.

## 6. Restart — pause

**Only if step 4 or 5 changed anything.** Ask the user to restart each affected harness (and the terminal), then resume; **stop here** until they confirm. A new environment variable needs the restart too, or the MCP server starts unauthenticated. Nothing changed → say so and skip.

## 7. Verify each target independently

Per selected target, confirm each capability is actually **working**, not merely present — **never that a scope is loaded because its file exists**: global instructions load, skills are discoverable this session, agents dispatch where the target supports them, MCP/plugin entry points run (no failing hook, no error on invoke), memory resolves or is reported as stored and manual, and domains stay inert until `project-init` projects one.

Each target has levers that answer this without a model call, and they cost nothing — reach for them before spending a run: **Codex** `codex debug prompt-input` renders the model-visible prompt, so the instruction file, the inlined memory block and the skill catalog with its `r0…rN` root map are all readable in one dump, and `codex doctor` confirms `config.toml` parses, while agent registration shows only in the request: point `-c model_providers.X.base_url` at a local server that logs the POST body and read `spawn_agent`'s `agent_type` roles; **OpenCode** `opencode debug skill` lists every skill with its resolved path, `opencode debug agent <name>` one agent's resolved config, `opencode debug config` the merged config, `opencode mcp list` which servers actually connect; **Claude Code** re-lists its skills mid-session, so a description that flips to the template's wording is the proof a link resolved. Only **agent dispatch** still needs a real run.

Report each target as **passed · skipped · failed**. For any failure propose a brief troubleshooting plan and **get the user's OK before any tool calls**. Two traps behind a capability that is "installed" yet silently exposes nothing: an orphaned or dependency-incomplete cache directory shadowing the working one, and a remote MCP server whose credential is missing — `enabledPlugins: true` in `settings.json` says nothing about either. For **github** specifically, `get_me` returning your account is the proof; `gh auth status` is the separate one.

## 8. Done — pause for review

Summarise per target what was synced or changed. A first install continues at `INSTALL.md`'s personalisation step; otherwise ask the user to review before further use.
