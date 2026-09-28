Template: `.agents/skills/harness-onboard/templates/MANIFEST.md`

# Codex — adapter manifest

Binary: not on `PATH` — `%LOCALAPPDATA%\OpenAI\Codex\bin\<hash>\codex.exe` · 0.153.0, 0.155.0-alpha where marked

## Install

| Fact | Value | Verified |
| --- | --- | --- |
| Home | `~/.codex` | 0.153.0 · 2026-09-04 · `debug prompt-input` |
| Global instructions | `AGENTS.md` · imports: no · `project_doc_fallback_filenames` exists | 0.153.0 · 2026-09-04 · `debug prompt-input`, string dump |
| Instruction budget | `project_doc_max_bytes`, default 32768, truncation silent · global and project `AGENTS.md` share it — raise the key in `config.toml` rather than lose the tail | 0.153.0 · 2026-09-04 · string dump |
| Skills | reads `~/.agents/skills` natively · `skills.config` exists (a `path` **or** `name` selector, never both), not needed | 0.153.0 · 2026-09-04 · `debug prompt-input` root map, string dump |
| Agents | `~/.codex/agents/{name}.toml` · keys `name`, `description`, `developer_instructions` · ID from `name` · convert: frontmatter to keys, Markdown body into a `'''` literal string, read and write UTF-8 explicitly, parse-check with `tomllib` | 0.155.0-alpha · 2026-09-27 · capture server shows every file as a `spawn_agent` role |
| Dispatch | `spawn_agent`, `agent_type` · proof: the child's `~/.codex/sessions/**/rollout-*.jsonl` carries `agent_role` and the injected instructions | 0.155.0-alpha · 2026-09-27 · run |
| Memory lever | none — global memory is read on demand | 0.153.0 · 2026-09-04 · `debug prompt-input` |
| Project memory | gap | 2026-09-27 · run |
| MCP | `[mcp_servers.{name}]` in `config.toml` | 0.153.0 · 2026-09-04 · run |
| Plugins | `[marketplaces.{x}] source_type = "git"` + `[plugins."{name}@{x}"] enabled = true` · `claude-plugins-official` works | 0.153.0 · 2026-09-04 · run |
| Config reload | unknown | — |
| Custom provider | `[model_providers.{x}]` with `base_url`, `env_key`, `wire_api = "responses"` (`"chat"` is rejected), per run via `-c model_providers.*` · a non-OpenAI model also needs a `model_catalog_json` entry (schema: `~/.codex/models_cache.json`) with `multi_agent_version`, else no agent tools | 0.155.0-alpha · 2026-09-27 · run |

## Project

| Fact | Value | Verified |
| --- | --- | --- |
| Project config | `.codex/config.toml` · trust: loads only with `[projects.'<path>'] trust_level = "trusted"`, so a moved repo silently loses it | 0.153.0 · 2026-09-04 · run |
| Skill roots | `.agents/skills/`, `.codex/skills/`, cwd up to the repo root · junctions resolve | 2026-09-17 · `debug prompt-input` |
| Agents | `.codex/agents/*.toml` | 2026-09-17 · string dump |
| MCP | `.codex/config.toml`, trusted only | 2026-09-17 · docs |
| Instruction shim | none — reads `AGENTS.md` itself; no imports, so `CHECKPOINT.local.md` and domain memory are read on demand | 2026-09-04 · `debug prompt-input` |

## Verification levers

- `codex debug prompt-input` — the model-visible prompt as JSON: instruction files, memory, the skill catalog with its `r0…rN` root map; not tool schemas
- `codex doctor` — `config.toml` parses
- `-c model_providers.{x}.base_url` at a local server logging the POST body — `spawn_agent`'s `agent_type` roles, no model call
- `~/.codex/sessions/**/rollout-*.jsonl` — one per thread; a child's names its `agent_role`

## Gotchas

- Agent tools ship as one `type: namespace` tool (`multi_agent_v1`, or `collaboration` under `multi_agent_v2`); `features.multi_agent_v2.tool_namespace` renames it, never removes it. An endpoint without namespace support (Unsloth Studio) hides every agent tool — a proxy flattening them upward and tagging returned `function_call`s with their `namespace` restores dispatch.
- The `github` plugin authenticates against `api.githubcopilot.com` and emits `AuthRequired` — separate from `gh`.
- Double quotes inside a prompt argument break parsing; pass the prompt after `--`.

## Proven

2026-09-27 · local Qwen3.6-35B-A3B · `e2e-runner` dispatched, the child rollout carries the converted `developer_instructions` · every agent registered · no domain skill in the catalog · a full workflow run waits for an OpenAI-class model: Qwen3.6 stalls on the PowerShell tooling and ends turns early
