Template: `.agents/skills/harness-onboard/templates/MANIFEST.md`

# Claude Code — adapter manifest

Binary: `claude` · version unrecorded

## Install

| Fact | Value | Verified |
| --- | --- | --- |
| Home | `~/.claude` | 2026-09-04 · run |
| Global instructions | `CLAUDE.md` · imports: yes, `@` — `@~/…` absolute home paths resolve | 2026-09-04 · run |
| Instruction budget | unknown | — |
| Skills | `~/.claude/skills/` only · link each folder into `~/.agents/skills/{name}` (`mklink /J`, no elevation on Windows 11) — never `skills/` itself, the harness writes its internals there | 2026-09-17 · `grep -a '\.agents'` on the binary finds nothing, junction probe |
| Agents | `~/.claude/agents/*.md`, scanned recursively · ID from `name:` · convert: none | 2026-09-04 · run |
| Dispatch | `Agent` tool, `subagent_type` · proof: unknown | 2026-09-27 · run |
| Memory lever | import, in the `CLAUDE.md` shim | 2026-09-27 · global memory in context |
| Project memory | `~/.claude/projects/<slug>/memory/` | 2026-09-27 · run |
| MCP | unknown at home scope — capabilities arrive as plugins | — |
| Plugins | `/plugin`, marketplace `claude-plugins-official` · enabled in `settings.json` `enabledPlugins` | 2026-09-04 · run |
| Config reload | skills re-listed mid-session, a description edit shows up · new skill folders next session | 2026-09-04 · run |
| Custom provider | `ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN` + the `ANTHROPIC_*_MODEL` family against an Anthropic-compatible `/v1/messages` endpoint | 2026-09-27 · run |

## Project

| Fact | Value | Verified |
| --- | --- | --- |
| Project config | `.claude/settings.json` · trust: unknown | — |
| Skill roots | `.claude/skills/` only | 2026-09-17 · string dump |
| Agents | `.claude/agents/*.md`, recursive | 2026-09-17 · docs |
| MCP | `.mcp.json` + `enableAllProjectMcpServers` in the project config | 2026-09-17 · docs |
| Instruction shim | needed — `CLAUDE.md` imports `AGENTS.md`, `CHECKPOINT.local.md`, each installed domain's `DOMAIN-MEMORY.md` | 2026-09-27 · run |

## Verification levers

- the session's own skill listing — re-listed mid-session, so a description flipping to the template's wording proves a link resolved

## Gotchas

- A dropped agent keeps loading until its file is deleted explicitly.

## Proven

2026-09-27 · local Qwen3.6-35B-A3B · `minimal-workflow` change with docs and commit · `e2e-runner` dispatched and returned its own verdict · global memory in context · no domain skill in the catalog
