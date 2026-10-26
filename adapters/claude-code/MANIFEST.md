Template: `.agents/skills/harness-onboard/templates/MANIFEST.md`

# Claude Code — adapter manifest

Binary: `claude` · 2.1.286

## Install

| Fact | Value | Verified |
| --- | --- | --- |
| Home | `~/.claude` | 2026-09-04 · run |
| Global instructions | `CLAUDE.md` · imports: yes, `@` — `@~/…` absolute home paths resolve | 2026-09-04 · run |
| Instruction budget | unknown | — |
| Skills | `~/.claude/skills/` only · link each folder into `~/.agents/skills/{name}` (`mklink /J`, no elevation on Windows 11) — never `skills/` itself, the harness writes its internals there | 2026-09-17 · `grep -a '\.agents'` on the binary finds nothing, junction probe |
| Agents | `~/.claude/agents/*.md`, scanned recursively · ID from `name:` · convert: none | 2026-09-04 · run |
| Dispatch | `Agent` tool, `subagent_type` · proof: unknown | 2026-09-27 · run |
| Headless run | `claude -p "<pointer>" --permission-mode auto --permission-prompts none < /dev/null`, every `CLAUDE*`/`ANTHROPIC*` variable unset · session id: `--session-id <uuid>` · resume: `claude -p --resume <uuid> "<pointer>"`, same transcript, context intact | 2.1.294 · 2026-10-08 · probe run + resume |
| Desktop session run | desktop app only: `start_session` (`initiation: user_asked` · `use_worktree: false` · `wake_when_done: true`), the pointer as `prompt` — inherits model, effort, permission mode, cwd and branch; no prompt seen in `auto` · its own `Agent` subagents work · verdict: first word of the child's wrap-up recap, the note quotes 400 chars, details in `report.md` · resume: `SendMessage` to its `local_…` id, context intact · the next run starts from the turn a note woke, when the user asked for the chain; how many events in a row the app allows is untested | claude-desktop 3.6556.2 · 2.1.299 · 2026-10-26 · probe: 2 runs, subagent, resume, chain |
| Memory lever | import, in the `CLAUDE.md` shim | 2026-09-27 · global memory in context |
| Project memory | `~/.claude/projects/<slug>/memory/` | 2026-09-27 · run |
| MCP | user scope in `~/.claude.json` `mcpServers` · `claude mcp add -s user {name} -- {command}` | 2026-10-05 · run, codegraph connected |
| Plugins | marketplace `claude-plugins-official`, not registered on a fresh install: `claude plugin marketplace add anthropics/claude-plugins-official`, then `claude plugin install {name}@claude-plugins-official` (or `/plugin`) · lands in `settings.json` `enabledPlugins` + `extraKnownMarketplaces` | 2026-10-05 · run |
| Config reload | skills re-listed mid-session, a description edit shows up · new skill folders next session | 2026-09-04 · run |
| Custom provider | `ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN` + the `ANTHROPIC_*_MODEL` family against an Anthropic-compatible `/v1/messages` endpoint | 2026-09-27 · run |

## Project

| Fact | Value | Verified |
| --- | --- | --- |
| Project config | `.claude/settings.json` · trust: workspace trust dialog — until accepted, MCP approvals committed to the project config are ignored | 2026-09-30 · docs |
| Skill roots | `.claude/skills/` only | 2026-09-17 · string dump |
| Agents | `.claude/agents/*.md`, recursive | 2026-09-17 · docs |
| MCP | `.mcp.json` + `enabledMcpjsonServers` naming each server in the project config — never `enableAllProjectMcpServers`, which approves whatever a later commit adds | 2026-09-30 · docs |
| Instruction shim | needed — `CLAUDE.md` imports `AGENTS.md`, `CHECKPOINT.local.md`, each installed domain's `DOMAIN-MEMORY.md` | 2026-09-27 · run |

## Verification levers

- the session's own skill listing — re-listed mid-session, so a description flipping to the template's wording proves a link resolved
- `claude mcp list` — health per server, plugin servers included: `Connected` · `Needs authentication` (OAuth, via `/mcp` in a session) · `Failed to connect` with the HTTP error
- `claude plugin list` — installed plugins, scope and enabled state

## Gotchas

- A dropped agent keeps loading until its file is deleted explicitly.
- A headless child launched from inside a Claude Code session inherits its `CLAUDE*` variables — session id, messaging socket — and hangs after the prompt, no error: unset them in the launch. From the Bash sandbox it also cannot start its own (`EPERM` on its socket) → `--settings '{"sandbox":{"enabled":false}}'`, the caller's sandbox still walls it; and `~/.claude/projects` is read-only there, so no session persists — no resume, transcript only via `--output-format stream-json` — 2.1.299 · 2026-10-25 · run.
- A desktop session run ends its turn while its background subagent still runs and its note arrives then, verdict-less; it resumes on the hand-back by itself → act only on a note whose recap starts with a verdict — claude-desktop 3.6556.2 · 2026-10-26 · probe.
- Under the Bash sandbox the OS keyring is unreachable: `gh` reports its keyring token invalid → `gh auth login --insecure-storage` — 2026-10-20 · run.
- Linux Bash sandbox: no `/dev/nvidia*` · own loopback — a host port only through the proxy, `NO_PROXY` emptied and `ip:port` in `allowedDomains` (a bare IP opens every port) · `allowUnixSockets` ignored, macOS only; `allowAllUnixSockets` exposes the user D-Bus, an escape via `systemd-run --user` · `excludedCommands` match a whole Bash call, never part of a chain — 2.1.299 · 2026-10-25 · run.
- Node's `fetch` ignores the sandbox proxy (`npx ctx7 …` → `fetch failed`) → `"NODE_USE_ENV_PROXY": "1"` in `settings.json` `env` — 2026-10-20 · run.
- `skills/synced/` (claude.ai-synced skills) and `skills/.trash/` are harness internals — never foreign files, never touched.

## Proven

2026-09-27 · local Qwen3.6-35B-A3B · `minimal-workflow` change with docs and commit · `e2e-runner` dispatched and returned its own verdict · global memory in context · no domain skill in the catalog
