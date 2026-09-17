# T-005 — Prove a converted Codex agent dispatches

- **Summary:** the one adapter claim still unproven — that `~/.codex/agents/*.toml` reaches a normal Codex session — needs a real dispatch, and the evidence so far says it does not
- **Category:** feature
- **Importance:** medium
- **Effort:** S
- **Depends on:** none

## Why

Everything else in `adapters/` was exercised on a live install (see below). This one could not be:
the Codex account was at its usage limit, and a dispatch is the only thing no offline lever answers.
`workspace-sync` currently tells the user to convert the agents and verify one dispatch before
calling `localagent-workflow` usable on Codex — a claim the install cannot yet stand behind.

## What

On a Codex account with quota, from a trusted project:

1. Dispatch `localagent-spec-architect` and check the answer comes from *its* prompt, not a generic
   sub-agent role-playing it — the failure mode already observed on OpenCode, where an orchestrator
   cannot tell the two apart.
2. If it does not reach: try declaring the agents in `config.toml` rather than as files, then decide
   between fixing `adapters/codex/` and giving the target a row that says it lacks named agents.
   Either way `workspace-sync`'s Codex bullet and its table's **Agents** column change.

## Evidence against, from `codex debug prompt-input` (0.153.0, `gpt-5.6-terra`)

Codex's subagent model is `spawn_agent` — the prompt describes creating generic sub-agents "equally
intelligent and capable, [with] the same set of tools", and names no agent from `~/.codex/agents/`.
Consistent with upstream reporting custom subagents not reaching tool-backed sessions
([#15250](https://github.com/openai/codex/issues/15250)). Not conclusive: `prompt-input` renders
input items, not tool schemas, so a named-agent parameter could still exist at runtime.

## Dispatching custom agents in OpenCode — observed

A primary agent asking for `localagent-spec-architect` first got a built-in `general` subagent that
role-played the part, which the orchestrator could not tell from a real result. Upstream this is
described as the `task` tool exposing only built-in `subagent_type` values to the model
([#29616](https://github.com/anomalyco/opencode/issues/29616), open, seen on 1.18.21;
[#20059](https://github.com/anomalyco/opencode/issues/20059) closed as fixed in 1.14.39 for agents
declared in `opencode.json`).

Here it resolved once the agents' `permission` blocks were removed: the correct subagent then ran,
on the second attempt. So the restricted `task` permission, not the enum, is the prime suspect — a
`task: { "localagent-*": allow, "*": ask }` map appears to interfere with dispatching by name. If it
recurs: restart OpenCode fully (agents load at startup, `/new` is not enough) before suspecting the
enum, and only then try declaring the agents in `opencode.json`.

## Settled on the 2026-09-04 install

- **The shared skills home holds.** Codex resolves `~/.agents/skills` as a native skill root (`r0` in
  `prompt-input`'s root map) with all 16 skills catalogued and no `skills.config` entry; OpenCode
  lists all 16 from the same path with no `skills.paths` entry, so both adapters are right to carry
  none. Claude Code discovers a skill through a per-folder `mklink /J` junction and re-lists it
  mid-session — the layout's one untested link works.
- **Codex instructions and memory load.** `~/.codex/AGENTS.md` reaches the prompt whole, inlined
  memory block included; at ~12.5 KiB it clears `project_doc_max_bytes` without help, but the install
  now raises the key anyway because a project `AGENTS.md` shares that budget.
- **OpenCode instructions, agents and MCP load.** `instructions` resolves both paths, a flat
  `agent/*.md` (singular directory) resolves by bare name at `mode: subagent`, and both MCP servers
  connect.
- **Codex's `github` plugin is unauthenticated** — a session emits `AuthRequired` against
  `api.githubcopilot.com`. Separate from `gh` and from Claude Code's github MCP, both of which work.
