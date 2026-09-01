# T-003 — Local model dispatch tool

- **Summary:** `localagent-dispatch` — a standalone MCP server + CLI that runs a single-purpose agent prompt against a local llama.cpp server with file access scoped to its declared inputs
- **Category:** feature
- **Importance:** medium
- **Effort:** M
- **Depends on:** none

> Seeds its own repo — so this ticket carries the design shape, one altitude above a normal
> ticket. Copy it into the new repo as the initial brief; it is not a spec, the interface below
> is binding but the implementation is open.

## Why

Two gaps, one tool closes both.

**The visibility wall is currently only a promise.** `localagent-workflow` forces TDD by having the
`test-author` and the `implementer` derive independently from a shared contract, with the
implementer never seeing the test code. Nothing enforces that: a subagent in a normal harness can
open any file it likes, and the workflow's own SKILL.md has to say so. The wall becomes real the
moment the agent's file access is scoped to its declared inputs — then the implementer *cannot*
reach the tests, and code that passes provably satisfies the contract rather than the test text.

**A local model cannot be reached from an agent harness.** `localagent-workflow` exists to run on a
weak (~30B) local model, but a harness like Claude Code can only dispatch its own vendor's models.
Without a bridge the workflow either runs on the wrong model class — defeating its purpose — or only
outside the harness. llama.cpp's own MCP support points the other way: `llama-server` is an MCP
*client* in its web UI, which does not help.

The existing "delegate to a local LLM" MCP servers (`hessenpepper/mcp-delegate`,
`houtini-ai/houtini-lm`, `HenryLinyy/local-llm-mcp`) are all young and none implements per-dispatch
path scoping — the one property that matters here. Wrapping one costs about as much as owning it.

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

**Also serves `dynamic-workflow`.** Its delegated mode dispatches one implementer subagent per work
package from a self-contained handoff — the same shape. Keep the interface free of any
localagent-specific concept (no units, no `U<N>`, no protocol vocabulary) and both workflows can
dispatch through it, with the path scoping as an optional tightening rather than a requirement.

**Non-goals:** not a model server, not an orchestrator (it never decides what runs next), and not a
sandbox — path scoping is a correctness boundary against a cooperative agent, not a security
boundary against a hostile one. Say so in the README.

## Open questions

- Does the local model need the whitelist restated in its prompt, or is a denied tool call enough
  feedback for a ~30B model to correct course?
- Transcript retention: keep every dispatch, or only failed ones?
- Does the harness's own permission layer double-prompt on each dispatch, and does that make the MCP
  path noisier in practice than the CLI one?
