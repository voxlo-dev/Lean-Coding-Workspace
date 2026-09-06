# Dispatch recipe — OpenCode dispatches natively

OpenCode **is** the dispatch target. Shelling out to `opencode run` from inside OpenCode buys a
second process and a second ~16k system prompt for nothing, so: **call the agent by name through the
task tool**, exactly as any native subagent. Everything the shell-out exists for — the connector,
the path globs, the isolated context — you already have.

## What that changes for the caller

- **The brief and the report contract are unchanged.** Four parts in, one status line out.
- **No transcript file.** The sub-session is in the session store and readable there, so report the
  status line and the session, not a path.
- **A blocked dispatch is still `BLOCKED`** — an unregistered agent name or a dead provider never
  falls back to the calling model.

## The routing table is advisory here

`~/.agents/dispatch.json` is read per-invocation by a shelling harness. A native call takes the
model from the **agent's own config**, so a role that needs a different model is set in
`~/.config/opencode/opencode.json` under that agent, matching the table by hand:

```json
{ "agent": { "localagent-implementer": { "model": "unsloth/qwen3.5-9b-mtp" } } }
```

Keep the two in step, and say which one you acted on when a run's model is in question.

## Agent visibility

`mode: all` agents appear in the primary-agent picker as well as being dispatchable. That is what
makes them reachable from another harness; the picker noise is the price.
