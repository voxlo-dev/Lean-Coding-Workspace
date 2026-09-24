# T-006 — Generate adapters per harness instead of shipping three

- **Summary:** replace the three hand-authored `adapters/` with a probe-and-generate flow, so onboarding any harness is a documented procedure rather than an author's favour, and split `workspace-sync` along the line where that procedure actually divides
- **Category:** feature
- **Importance:** high
- **Effort:** L
- **Depends on:** a clean run of the verification below

## Why

The repo calls itself harness-agnostic and then ships exactly three harnesses. A fourth — Gemini CLI,
Cursor, whatever comes next — means hand-authoring an overlay from scratch, which nobody does, so
"agnostic" quietly means "these three". The knowledge that *makes* an adapter correct is also
invisible: `adapters/opencode/` is four short files, and nothing in them says that the agents
directory is singular, that `skills.paths` would be redundant, or how either was established.

`workspace-sync` has meanwhile grown two jobs with different lifecycles. Learning what a harness
needs is rare, expensive and needs the user. Applying what is already known is frequent, mechanical
and should be boring. One skill doing both is why the current one is ten steps long and why a repair
run re-reads research it does not need.

## What

Three outcomes, in this order.

### 1. An adapter is generated from a capability manifest, not written by hand

Each target gets a manifest of **verified facts**, and the overlay files fall out of it. The
questions below are the ones this repo has actually had to answer per harness; a manifest that
cannot answer one names the gap instead of guessing:

| Fact | Why it decides something |
| --- | --- |
| home directory, project config file, trust model | where everything lands; Codex reads a project config only in a trusted project |
| global instruction filename, **does it resolve imports** | decides the memory lever and whether a project shim is needed at all |
| instruction size budget + truncation behaviour | Codex silently truncates at `project_doc_max_bytes` (32768) |
| reads `~/.agents/skills` natively? else the link mechanism | the one-home rule stands or falls here |
| agents: directory name, file format, how the ID is derived, how dispatch works | the format differs per harness and the directory name is not guessable |
| memory lever: import · instructions list · none | "none" is a legitimate answer that must stay visible |
| MCP config shape, plugin system (if any) | capabilities install differently or not at all |
| **verification levers** | the whole install is unfalsifiable without them |

### 2. A procedure for probing an unknown harness

The manifest above is the output; this is the input. It must work for a harness nobody here has
seen, and it must prefer evidence over documentation — see the record below for why.

### 3. The split

Recommended cut is **by the lifecycle of a harness**, not by first-run vs re-run:

- **onboard a harness** — probe, fill the manifest, generate the overlay, verify. Once per harness,
  ever. A second machine running the same harnesses never touches it.
- **sync the workspace** — copy the shared home, apply each known overlay, merge config, install
  capabilities, verify. Every install and every repair, identical either way. **This half exists:**
  `workspace-sync` in `.agents/skills/`, with the bootstrap and the once-only personalisation split
  off into `INSTALL.md` — prose rather than a skill, so a fresh machine can follow it before
  anything is cloned. What is left for this ticket is the **onboard** half.

An init/update split cuts the wrong way for the *skills*: a fresh install on a machine whose
harnesses are already known needs no research, while adding a fourth harness to a long-running
workspace does. The expensive part follows the harness, not the calendar. It does hold for the
**entry point**, which is why `INSTALL.md` is a document and not a third skill.

## The open decision: are generated adapters tracked?

Settle this first — it changes what the generator is for.

- **Tracked (recommended)** — the overlay is a cache of verified results. Regenerating is possible,
  but nobody pays the probe cost twice, and a diff shows when a harness release moves a path.
  Costs: a generated artifact in git, and the repo still names three harnesses in `adapters/`.
- **Gitignored** — the repo names no harness anywhere, which is the cleanest reading of "agnostic".
  Costs: every install re-derives everything below, including the parts that took a binary string
  dump to establish. Weigh honestly against how often the facts actually change.
- A middle option exists: track the **manifests** (the facts, which are the expensive part) and
  gitignore the **generated overlay files** (the cheap mechanical part).

## Blocked on the current setup being proven

Do not start while the install is unverified — a generator seeded from facts that were never
confirmed multiplies the error across every future harness. Required first:

- A converted Codex agent dispatches, or `workspace-sync`'s Codex row says it cannot: dispatch
  `e2e-runner` from a trusted project and check the answer comes from *its* prompt, not a generic
  sub-agent playing it; failing that, try declaring the agents in `config.toml`.
- Practical use, not a checkup: run a real workflow end to end in **each** harness — a
  `minimal-workflow` change with a memory write and a docs step is enough — and confirm skills load,
  agents dispatch, memory resolves, and a domain stays inert until a project installs one.

## What this chat established — the seed for the manifests

Verified on 2026-09-04 unless marked. Treat every version-bound line as a fact with an expiry date.

### Method — the part worth keeping

- **Levers beat runs.** `codex debug prompt-input` renders the model-visible prompt as JSON with no
  model call, so instruction loading, memory presence and the skill catalog (with its `r0…rN` root
  map) are all readable at once. `opencode debug skill` / `debug agent <name>` / `debug config` /
  `mcp list` do the same for OpenCode. Claude Code re-lists skills mid-session, so a description
  flipping to the template's wording proves a link resolved. Finding these levers should be an
  explicit probe step — they turned an unverifiable install into a checkable one.
- **A file existing proves nothing.** Every real defect this chat found — two mangled skill copies,
  three divergent memory stores, a dead `@~/.claude/domains/` import, an `AGENTS.md` still carrying
  a superseded rule — passed an existence check and failed a lever.
- **Beware checks that skip what they should test.** A template-vs-install comparison that skipped
  lines containing `{` reported "0 missing" while the one wrong line was a `{x}` placeholder line.
- **Binary string dumps answered what docs did not** — `.agents/skills` as a native path,
  `skills.config` accepting a `path` or `name` selector, the `project_doc_max_bytes` default.
- **Upstream issues are a lead, not a verdict.** They pointed at the right area twice and were the
  wrong explanation once (OpenCode dispatch was the `permission` block, not the `subagent_type` enum).

### Codex — CLI 0.153.0, `gpt-5.6-terra`

- Not on `PATH`; binary at `%LOCALAPPDATA%\OpenAI\Codex\bin\<hash>\codex.exe`.
- `~/.codex/AGENTS.md`, no imports. `project_doc_max_bytes` default 32768, truncation silent; the
  global and a project `AGENTS.md` share the budget. `project_doc_fallback_filenames` exists.
- Reads `~/.agents/skills` **natively** as a skill root — no `skills.config` entry needed. That key
  does exist, taking a `path` **or** `name` selector, never both; `SkillsConfig` also carries
  `bundled`, `include_instructions`, `max_context_tokens`.
- **No memory lever.** Global memory is readable by path but never in context. Inlining it was tried
  and removed: a cache the next memory write strands.
- Agents: `~/.codex/agents/{name}.toml` with `name`, `description`, `developer_instructions`. A
  multi-line **literal** string (`'''`) carries the Markdown body without escaping. Converted, then
  parse-checked with `tomllib`. **Reachability unproven** — the prompt describes a generic
  `spawn_agent` / `followup_task` / `send_message` model and names no custom agent, matching
  upstream [#15250](https://github.com/openai/codex/issues/15250). Not conclusive: `prompt-input`
  renders input items, not tool schemas.
- Plugins: the same `claude-plugins-official` marketplace, `[marketplaces.X] source_type = "git"`
  plus `[plugins."name@X"] enabled = true`. codegraph goes in as `[mcp_servers.*]`, not a plugin.
- Its `github` plugin emits `AuthRequired` against `api.githubcopilot.com` — separate from `gh`.
- Project config `.codex/config.toml` loads **only in a trusted project**
  (`[projects.'<path>'] trust_level = "trusted"`), so a moved repo silently loses it.
- CLI gotcha: double quotes inside a prompt argument break parsing; pass it after `--`.

### OpenCode — 1.18.23

- Authoritative key list is `$defs.Config.properties` in <https://opencode.ai/config.json>. Fetch it
  rather than recalling — the harness hard-fails on invalid config.
- `instructions` is an array of paths and expands `~`. This is the memory lever.
- Reads `~/.agents/skills` **natively**; `skills.paths` exists but is redundant, so the adapter
  correctly carries none.
- Agents live in `agent/` — **singular** — flat, ID from path, `mode: subagent`. A per-agent
  `permission` block interfered with dispatch by name: a built-in `general` subagent played the
  agent instead, indistinguishable to the caller, until the block was removed. Upstream blames the
  `task` enum ([#29616](https://github.com/anomalyco/opencode/issues/29616)); if it recurs, restart
  fully before suspecting it.
- Plugins are JS modules in `plugin`; everything else arrives through `mcp`
  (`{type: "local", command: [...]}` or `{type: "remote", url}`).
- Config is not hot-reloaded — restart, `/new` is not enough.
- On PowerShell the binary writes its banner to stderr, which surfaces as `NativeCommandError`
  around perfectly successful runs.

### Pi — 0.85.1, verified 2026-09-24 as a dispatch target only

- Two distinct harnesses on this machine: **bonsai-pi** (pinned by `bonsai-local` in WSL, own agent
  dir via `PI_CODING_AGENT_DIR`) and plain Pi (`~/.pi/agent`, no binary on PATH). A manifest per
  install, not per binary.
- No agent registry: a dispatch hands one session the whole brief. `-p` is headless;
  `--` must separate flags from the prompt.
- Dispatched from Windows through `wsl.exe -e bash -lc`, repo under `/mnt/c/…`; the launcher's
  status lines go to stderr, so stdout's last line stays the verdict.
- A model reads its own role from the paths it is given: a brief under `.temp/dispatch/` made the
  `--localagent` orchestrator act as a dispatched worker and skip its pipeline.

### Claude Code

- Reads only `~/.claude/skills/`. A **per-folder** junction into `~/.agents/skills/` is discovered,
  and a description edit shows up mid-session. Linking `skills/` itself fails — the harness writes
  its own internals there. `mklink /J` needs no elevation on Windows 11.
- `CLAUDE.md` is a shim resolving `@` imports, and `@~/…` absolute home paths resolve.
- Agents flat in `~/.claude/agents/`, ID from `name:`. A dropped agent keeps loading until its file
  is deleted explicitly.

### Projection targets — verified 2026-09-17, the domain system's inputs

Docs plus string dumps of the installed builds; the junction probe was run in this repo and removed.
A manifest has to answer these per harness, because `project-initialiser` writes to every one of them.

| Harness | Skill roots (project) | Agents | MCP |
| --- | --- | --- | --- |
| Codex | `.agents/skills/`, `.codex/skills/`, cwd → repo root | `.codex/agents/*.toml` | `.codex/config.toml`, trusted only |
| OpenCode | `.agents/skills/`, `.claude/skills/`, `.opencode/skills/`, cwd upwards | `.opencode/agent/*.md` | `opencode.json` `mcp` |
| Claude Code | `.claude/skills/` only | `.claude/agents/*.md`, recursive | `.mcp.json` + `enableAllProjectMcpServers` |

- `grep -a '\.agents' claude.exe` returns nothing: Claude Code reads no `.agents` path at any scope,
  so the per-skill link is the only bridge — and the reason a manifest needs a *link mechanism* row.
- **Junctions resolve in all three.** A throwaway skill junctioned into this repo's `.agents/skills/`:
  `opencode debug skill` lists it under the *link* path, `codex debug prompt-input` carries it under
  skill root `r8`. This is what lets a domain be projected rather than copied.
- OpenCode's `skills.paths` would add a root without any link; Codex and Claude Code have no
  equivalent. One of three is a hole — the manifest records it as such rather than as a strategy.

### Cross-harness

- Agent frontmatter safely shared by Claude Code and OpenCode: `name`, `description`,
  `disallowedTools`, `skills`, `hooks`, `mode`, `permission`. **Never `model` or `tools`** — each
  takes a different type per harness and invalidates the file in the other.
- Agents must install **flat** whatever the source layout: OpenCode folds a subfolder into the ID.
- The one-home rule and its project-scope form are settled and should be inputs to the generator,
  not rediscovered: shared content lives once in `~/.agents/` (repo: `.agents/`), harnesses are
  **pointed** at it, and a harness that cannot be pointed reads it on demand.
