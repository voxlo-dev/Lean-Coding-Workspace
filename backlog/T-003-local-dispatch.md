# T-003 — Harness dispatch

- **Summary:** Delegate a step or a phase to another harness — Claude Code shells out to `opencode run`, which brings the connector, the path scoping and its own subagents
- **Category:** feature
- **Importance:** high
- **Effort:** M
- **Depends on:** none

> Carries the design shape, one altitude above a normal ticket: the interface below is binding,
> the implementation is open. Workspace-internal work — it touches `skills/`, `agents/` and
> `adapters/`, and ships no new repo.

## Why

**A harness cannot reach a model its vendor does not sell.** `localagent-workflow` exists to run on a
weak (~30B) local model and `dynamic-workflow`'s delegated mode wants cheap work packages one class
down, but Claude Code dispatches Anthropic models and nothing else.

**OpenCode already solves this, and solves more than the model problem.** It resolves any
OpenAI-compatible endpoint through its `provider` block, it runs headless with an agent and a model
chosen per invocation, and it enforces **per-agent path globs** — the one restriction
`localagent-workflow` depends on, which Claude Code has no mechanism for at all (it restricts
subagents by tool name only). So the bridge is not a model bridge: **dispatch the harness that
already has one.** No MCP server, no inner tool sandbox, no transcript plumbing to own — the three
expensive parts of the previous shape.

Verified on 2026-09-05 against OpenCode 1.18.23–1.18.29 (it self-updated mid-session) and Unsloth
Studio 2026.8.22, by dispatching a purpose-built probe agent at a local Qwen3.5-9B GGUF:

| Claim | Status |
| --- | --- |
| `opencode run --agent X -m provider/model --dir <abs> --auto "<brief>"` is the dispatch | **Confirmed on a live run** — agent and model both resolved, tool calls executed, final line returned |
| The wall holds under `--auto`: a `permission.read` deny refuses the tool call and the agent sees the refusal | **Confirmed** — the probe was refused and reported `WALL-HELD`; the model did not route around it |
| **Rule precedence is last-match-wins, not deny-wins** | **Confirmed by counter-example** — with `"*": allow` listed *after* the denies the probe read the forbidden file (`WALL-BREACHED`); moving the allow first restored the deny |
| Exit code + stdout carry the report | **Confirmed** — the status line is stdout's last line; a dead endpoint gave `Error: Cannot connect to API` and exit 1 |
| `run --agent` accepts **primary agents only** — a `mode: subagent` name warns and **silently falls back to the default agent** | Confirmed, and the reason `mode: all` is required below |
| `mode` accepts `subagent \| primary \| all` | Confirmed from `opencode.ai/config.json` |
| OpenCode's own system prompt costs **~16k tokens** before the brief | **Confirmed** — a model loaded at 8192 context aborts with `context_length_exceeded`; 32k is the floor |
| One endpoint can serve many models, loaded on demand | **Confirmed** — Unsloth Studio's `openai_auto_switch` setting (off by default) makes a request for an unloaded model load it, so a per-role table needs no per-role server |

**Alternatives fail on the two properties this rests on.** Hermes' `delegate_task` delegates only
inside its own session and exposes no path scoping; Pi ships no subagent tool in core at all. Neither
offers a headless run-one-agent CLI. OpenCode is the backend; the adapter layer is what keeps it
replaceable.

**Relation to [`T-001`](T-001-sprint-orchestrator.md):** thick dispatch is the delegation *primitive*
a sprint conductor would use — a phase costs the conductor a brief and a status line, because the
work happens in another process. It does not settle T-001's actual questions (where the inline
planning phases hand off, how a question relays back), so it enables that ticket rather than
replacing it.

## What

**Two shapes, one mechanism** — same command, a different agent name:

- **Thin (default)** — one dispatch per step. The calling harness keeps the plan, the gate and the
  check after every `DONE`; OpenCode does the bounded content work. This is the point: a strong
  orchestrator, a weak worker.
- **Thick** — one dispatch per phase, to a primary agent that drives its own subagents natively.
  Nested delegation across the process boundary, at the cost of running the gate on the weak model.

**The routing table** — `~/.agents/dispatch.json`, harness-neutral, user-owned, written by
`workspace-install` and edited by hand. Keyed by **role**, never by agent name, so both workflows and
both shapes read the same file: the localagent workers, the localagent orchestrator, a
`dynamic-workflow` package. A role's value names a `provider/model` that the target harness resolves.

**Connectors** are provider entries in the target harness's own config, and reduce to two recipes:
llama.cpp, unsloth and runpod are one OpenAI-compatible block differing only in `baseURL` and key;
openrouter is a built-in provider reached through `auth login`. The workspace ships both recipes; the
table references the resulting IDs and knows nothing about how a provider was defined.

**The table carries model IDs and nothing else.** Load-time parameters — context length, quant,
sampling, GPU placement — belong to the connector, keyed per model on its own side: Unsloth Studio
stores them as per-model overrides and applies them when a request loads that model — keyed by model
*and quant*, so an override written without the quant suffix silently becomes a catch-all across every
quant of that model, and one written for a quant that is not the one being loaded silently does
nothing. So a role switching models does not mean a table entry carrying launch flags, and it does not
mean one server per role. **Its one hard requirement is context:** OpenCode's system prompt alone is
~16k tokens, so any model in the table must be loaded at 32k or more or the dispatch dies before
reading its brief.

**Per-request parameters are a third home, and the harness owns it.** Load-time settings belong to
the connector, but what the harness puts in each request body beats every server-side default,
because an explicit field always wins. In OpenCode that is the provider's per-model entry: `limit.output`
becomes the request's `max_tokens`, and reasoning effort reaches the model **only** as
`options.reasoningEffort` — camelCase, which OpenCode translates to `reasoning_effort` in the body.
A model-level `effort` key, `options.reasoning_effort` in snake_case, and the `--variant` flag are all
dropped in silence; the model-entry schema is `additionalProperties: false`, yet an unknown key
loads without a warning. So the three homes are: **the table** names the model, **the connector**
holds how it loads, **the harness's model entry** holds what every request carries. A dispatch that
behaves differently than expected is almost always the third one, and only a proxy capture of the
real request body settles it.

**The report contract.** The agent prompts already end in a status line (`DONE <path>` · `RED <path>`
· `ESCALATE <reason>` · `BLOCKED <reason>`). A dispatch writes the full transcript to a file and
returns **only that line plus the transcript path**, so a failed dispatch is inspectable without
loading it into the orchestrator's context.

**Watching a run is half-solved.** Every dispatch lands in OpenCode's shared session store — agent,
model, tool calls, tokens, cost — and a detached `opencode run` appears there within seconds, while
it is still going, with no `--attach` and no change to the dispatch command. `/api/session` serves
all of it. What cannot read it is the shipped web UI: in 1.18.29 its bundle requests `/api/project`,
a route the server does not implement, so the project list stays empty and the page reports no
sessions; deep-linking around it fails too, because the server's `/session/{id}` route shadows the
client route of the same name. The TUI (`opencode attach <url>`) works. The orchestrator is
unaffected either way — it gets only the status line; a live view is for the human.

**A dead connector is `BLOCKED`, always** — in both workflows, with no fallback to the calling
harness's own model. Falling back would put the strongest context in the run on unwalled, untracked
work, which is the exact failure `localagent-workflow` exists to prevent; and a `dynamic-workflow`
package that silently changed model class is a result no one can reason about afterwards.

**Harness separation is an adapter concern.** The rule is *a harness never dispatches itself*:
OpenCode runs its agents natively through its own Task tool, Claude Code and Codex shell out. The
dispatch skill therefore states the rule and reads the recipe from `{home}/adapter/dispatch/`; each
adapter supplies its own, and the recipe must name the platform's launcher (on Windows the npm shim
is `opencode.cmd` / `opencode.ps1` — a bare `opencode` is not executable from a POSIX shell).

**Required change to the agents:** the six `localagent-*` workers move from `mode: subagent` to
`mode: all`, or an external dispatch runs them as the default agent with the wall gone. `mode` is an
OpenCode-only key that Claude Code ignores, so the files stay valid in both dialects.

**Non-goals:** not a sandbox — path scoping is a correctness boundary against a cooperative agent,
not a security boundary against a hostile one. Not an orchestrator — the dispatcher never decides
what runs next. Not a model server.

## Open questions

- Transcript retention: keep every dispatch, or only failed ones?
- Does Claude Code's permission layer prompt per dispatch, making the shell-out noisier in practice
  than running the whole workflow in OpenCode?
- Does `--auto` interact badly with MCP servers a project's `opencode.json` registers — extra
  startup cost, or prompts that `--auto` does not cover?
- What role granularity does `dynamic-workflow` need — one `package` role, or per package type?
- Does `mode: all` make the six workers appear in OpenCode's primary-agent picker, and is that
  noise worth suppressing?
- Is a small dispatch monitor over `/api/session` worth owning, or is waiting for the upstream
  web-UI fix cheaper? The TUI covers the need today.

## Links

- [`T-003-bench.md`](T-003-bench.md) — the measurements: nine models, four connector types, all
  passing on one unchanged command; local Qwen3.5-9B ties the fastest hosted route
- [`T-001`](T-001-sprint-orchestrator.md) — consumes thick dispatch as its delegation primitive
- [`T-005`](T-005-verify-harness-adapters.md) — the same "prove a dispatch actually runs" gap, for Codex
- [`T-006`](T-006-generated-adapters.md) — would generate the per-harness dispatch recipe rather than ship three
