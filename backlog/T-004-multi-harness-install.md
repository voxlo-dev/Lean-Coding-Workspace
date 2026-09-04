# T-004 — Multi-harness workspace install

- **Summary:** verify the OpenCode adapter on a live install and fill `adapters/opencode/`, the one target still shipped empty
- **Category:** feature
- **Importance:** medium
- **Effort:** S
- **Depends on:** none

## Why

The template is neutral and `adapters/{target}/` carries the wiring; Claude Code is verified and
Codex is verified except for agent registration. **OpenCode is unverified**, so `workspace-install`
currently offers it only to ask the user for its paths and then skips it.

`localagent-workflow` is the skill that needs this most: its agents must be *registered* with the
harness or there is nothing to dispatch and one context does every step — the failure the workflow
exists to prevent. Copying those files by hand per machine is also the kind of step that silently
rots: an agent gets edited in the template and the OpenCode copy keeps running the old prompt, with
no signal that the two diverged.

## What

On a machine with OpenCode installed, confirm and then record in `adapters/opencode/`:

- Global instruction file — path, and whether it resolves imports (Claude Code does, Codex does not;
  this decides how global memory is wired).
- Skills — whether it has a home of its own, or only reads `~/.claude/skills/`. If the latter, the
  adapter installs no skills at all and says so.
- Agents — path confirmed, installed flat. The ID comes from the path below `agents/`, unlike Claude
  Code which keys off `name:`, which is why flat is the only layout that serves both.
- Domain manifests — whether an equivalent of `plugin.json` / `.mcp.json` exists. Without one,
  `domain-initialiser` reports that OpenCode cannot host domains.
- Plugins/MCP — whether the `claude-plugins-official` marketplace is reachable, as it is in Codex.

Then flip its row in `workspace-install` step 1 from **unverified** to **verified**.

**Codex agents are the second open question:** there is no `~/.codex/agents/`. Either find the
registration mechanism or keep `localagent-workflow` marked unavailable there.

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
