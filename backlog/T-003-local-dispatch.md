# T-003 — Local model dispatch tool

- **Summary:** `localagent-dispatch` — a standalone MCP server + CLI that runs an agent prompt against a local llama.cpp server with file access scoped to its declared inputs
- **Category:** feature
- **Importance:** low
- **Effort:** M
- **Depends on:** none

> Seeds its own repo — so this ticket carries the design shape, one altitude above a normal
> ticket. Copy it into the new repo as the initial brief; it is not a spec, the interface below
> is binding but the implementation is open.

## Why

**A harness cannot reach a local model.** `localagent-workflow` exists to run on a weak (~30B) local
model, but Claude Code only dispatches its own vendor's models. Without a bridge the workflow either
runs on the wrong model class — defeating its purpose — or it leaves the harness entirely.

The obvious alternative is to leave: OpenCode launches against `llama-server` and enforces the whole
workflow natively, including the visibility wall via per-agent `permission.read` globs. **That path
needs no tooling at all**, which is why this ticket is `low` — it buys one specific thing OpenCode
does not: keeping a *strong* orchestrator and giving the *weak* model only the bounded content work.
Plan and gate on the capable model, dispatch `test-author` and `implementer` locally. `dynamic-workflow`'s
delegated mode wants the same thing for its work packages, one model class up.

Scoping matters here in a way it does not in OpenCode: Claude Code restricts subagents by tool name
only, with no path or glob mechanism, so an agent dispatched *through* this tool is scoped by the
tool or not at all. The wall is a property of the dispatcher, not of the harness around it.

The existing "delegate to a local LLM" MCP servers (`hessenpepper/mcp-delegate`,
`houtini-ai/houtini-lm`, `HenryLinyy/local-llm-mcp`) are all young and none implements per-dispatch
path scoping. Wrapping one costs about as much as owning it.

## What

A standalone tool, in its own repo, that takes an agent prompt plus a set of declared file paths,
runs it as an agentic loop against an OpenAI-compatible endpoint, and returns the agent's status
line. It knows nothing about units, sprints, workflows or any harness.

**Two entry points, one implementation:**

- **MCP server** (stdio) — a harness registers it and gets the dispatch tool.
- **CLI** — the same dispatch, for an external runner or a shell script driving the protocol directly.

**The dispatch interface** (binding):

| Parameter | Meaning |
| --- | --- |
| `prompt` | path to the agent prompt file, or the prompt text |
| `brief` | the 2–3 line task brief |
| `readable` | paths the agent may read — **the whitelist is the whole contract** |
| `writable` | paths/dirs the agent may write |
| `commands` | commands it may run, or none |
| `model` | backend override; otherwise the configured default |

An agent prompt file may carry frontmatter — the localagent agents do, for the harnesses. Ignore
every key except a `permission` block, whose globs are a usable default for `readable`/`writable`
when the caller passes none.

Returns the agent's final status line (`DONE <path>` · `RED <path>` · `ESCALATE <reason>` ·
`BLOCKED <reason>`) plus the path to the full transcript, so a failed dispatch is inspectable
without loading it into the orchestrator's context.

**Inner tools given to the agent:** `read_file`, `write_file`, `list_dir`, `run_cmd` — every one of
them **deny-by-default**, resolving the real path and refusing anything outside the whitelist. A
denied call returns an error the agent can see and act on, it does not kill the dispatch.

`run_cmd` is in scope: the `test-author` must confirm its tests are red, the `verifier` must run
them, and the `implementer` must typecheck. It takes a per-dispatch allow-list rather than a shell —
an agent that may run the test runner must not be able to run anything else.

**Backend:** `llama-server`'s OpenAI-compatible `/v1/chat/completions`, base URL and model
configurable, so LM Studio, vLLM or any other compatible endpoint works unchanged. Turn cap and
timeout per dispatch; hitting either returns `BLOCKED`, never a partial success.

**Non-goals:** not a model server, not an orchestrator (it never decides what runs next), and not a
sandbox — path scoping is a correctness boundary against a cooperative agent, not a security
boundary against a hostile one. Say so in the README.

## Open questions

- Does the local model need the whitelist restated in its prompt, or is a denied tool call enough
  feedback for a ~30B model to correct course?
- Transcript retention: keep every dispatch, or only failed ones?
- Does Claude Code's permission layer prompt on every dispatch, making the MCP path noisier in
  practice than just running the whole workflow in OpenCode?
