# T-005 — Verify the Codex and OpenCode adapters on live installs

- **Summary:** run `/workspace-install` against Codex and OpenCode and confirm each adapter's claims by dispatching, not by looking
- **Category:** feature
- **Importance:** medium
- **Effort:** S
- **Depends on:** none

## Why

Every path in `adapters/codex/` and `adapters/opencode/` comes from the harnesses' own documentation
and from a live `~/.codex` — real sources, but none of it has been exercised end to end. The two
claims that matter most are also the two least certain: that a converted Codex agent is reachable
from a normal session, and that OpenCode's `skills.paths` picks up the workspace skills.

## What

Per target, install and then prove each row of `workspace-install`'s table:

- **Codex** — `~/.codex/AGENTS.md` loads · the inlined memory block survives `project_doc_max_bytes`
  (raise it if the file is near 32 KiB; truncation is silent) · `[[skills.config]] path` registers a
  skill outside `~/.codex/skills/` · **dispatch one converted `agents/*.toml` agent and check the
  answer comes from its prompt** — upstream reports custom subagents not reaching tool-backed
  sessions ([#15250](https://github.com/openai/codex/issues/15250)) · a domain activates from
  `.codex/config.toml` in a trusted project.
- **OpenCode** — `instructions` pulls in `AGENTS.md` and the memory index · `skills.paths` finds the
  skills, ideally pointed at `~/.claude/skills/` rather than a second copy · a project `opencode.json`
  scopes a domain's skills and MCP · agents dispatch by name.

Then flip whatever fails into a fix, and record what holds where it is claimed.

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
