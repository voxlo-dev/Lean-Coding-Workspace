Template: `.agents/skills/harness-onboard/templates/MANIFEST.md`

# OpenCode — adapter manifest

Binary: `opencode` · 1.18.23

## Install

| Fact | Value | Verified |
| --- | --- | --- |
| Home | `~/.config/opencode` | 1.18.23 · 2026-09-04 · `debug config` |
| Global instructions | `AGENTS.md`, listed in `opencode.json` `instructions` (array of paths, expands `~`) · imports: unknown, the list makes them unnecessary | 1.18.23 · 2026-09-04 · `debug config` |
| Instruction budget | unknown | — |
| Skills | reads `~/.agents/skills` natively · `skills.paths` exists, redundant | 1.18.23 · 2026-09-04 · `debug skill` |
| Agents | `~/.config/opencode/agent/*.md` — singular, flat: a subfolder folds into the ID · ID from path · `mode: subagent` · convert: none | 1.18.23 · 2026-09-04 · `debug agent` |
| Dispatch | `task` tool · proof: `opencode export <session>` names the child's `agent` | 1.18.23 · 2026-09-27 · run |
| Memory lever | instructions list | 1.18.23 · 2026-09-27 · global memory in context |
| Project memory | gap | 2026-09-27 · run |
| MCP | `opencode.json` `mcp` — `{type: "local", command: [...]}` or `{type: "remote", url}` | 1.18.23 · 2026-09-04 · `mcp list` |
| Plugins | `plugin` takes JS modules only — capabilities arrive through `mcp` | 1.18.23 · 2026-09-04 · schema |
| Config reload | restart — `/new` is not enough | 1.18.23 · 2026-09-04 · run |
| Custom provider | an `OPENCODE_CONFIG` overlay file | 1.18.23 · 2026-09-27 · run |

## Project

| Fact | Value | Verified |
| --- | --- | --- |
| Project config | `opencode.json` in the repo root · trust: unknown | — |
| Skill roots | `.agents/skills/`, `.claude/skills/`, `.opencode/skills/`, cwd upwards · junctions resolve, listed under the link path · `skills.paths` adds a root without a link | 2026-09-17 · `debug skill`, string dump |
| Agents | `.opencode/agent/*.md` | 2026-09-17 · string dump |
| MCP | `opencode.json` `mcp` | 2026-09-17 · docs |
| Instruction shim | none — domain memory joins `instructions` via `adapter/project-merge/` | 2026-09-17 · docs |

## Verification levers

- `$defs.Config.properties` in <https://opencode.ai/config.json> — the authoritative key list; fetch it, invalid config hard-fails the harness
- `opencode debug skill` — every skill with its resolved path
- `opencode debug agent <name>` — one agent's resolved config
- `opencode debug config` — the merged config
- `opencode mcp list` — which servers actually connect
- `opencode export <session>` — a session's record, the child's `agent` included

## Gotchas

- A dispatch answered by the built-in `general` subagent instead of the named one is indistinguishable to the caller: restart fully first; if it persists, suspect a per-agent `permission` block (removing one fixed it once; [#29616](https://github.com/anomalyco/opencode/issues/29616) blames the `task` enum).
- In `opencode run`, a permission resolving to `ask` is auto-rejected and **ends the run**; reads outside the project are the `external_directory` permission.
- On PowerShell the banner goes to stderr and surfaces as `NativeCommandError` around successful runs.

## Proven

2026-09-27 · local Qwen3.6-35B-A3B · `minimal-workflow` change with docs and commit · `e2e-runner` dispatched, the child session exports as `"agent": "e2e-runner"` · global memory in context · no domain skill in the catalog
