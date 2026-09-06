---
name: dispatch
description: "Use to run one agent step or phase on a model the current harness cannot reach — a local GGUF, a free vendor tier, an aggregator — or to get per-agent path scoping the current harness lacks. Shells out to another harness with a role-chosen model and returns one status line. Reached for by localagent-workflow's steps and by dynamic-workflow's delegated packages."
---

# Dispatch

Run **one** agent, in another harness, in its own process, against a model chosen by **role**. You get
back a status line and a transcript path — nothing else enters your context.

Two things make this worth a process boundary, and both are unavailable inside a single harness:
any OpenAI-compatible endpoint as a target model, and **per-agent path globs** (`read`/`glob`/`grep`
denies), which is the only enforced restriction `localagent-workflow` rests on.

**A harness never dispatches itself.** Where the target harness *is* the one you are running in, use
its native agent call — a shell-out to your own binary is a second process, a second system prompt
and no added capability. `{home}/adapter/dispatch/` holds this harness's recipe and decides which of
the two you are in; **no `dispatch/` folder in the adapter means this harness has no dispatch** — say
so and fall back to the calling workflow's own escalation, never improvise a command.

## 1. Resolve the model — `~/.agents/dispatch.json`

User-owned, hand-edited, seeded by `workspace-install` from `templates/dispatch.json`. Keyed by
**role**, so both workflows and both shapes read one file. Resolution is **most specific first**:

1. `agents.<agent-name>` — one agent pinned to its own model.
2. `roles.<role>` — the role the caller names (`localagent-worker`, `localagent-orchestrator`,
   `package`).
3. `default`.

A value is a `provider/model` string the *target* harness resolves, and **nothing else**: how a model
loads belongs to the connector, what each request carries belongs to the target harness's model entry
(`references/connectors.md` — read it before touching either, and before diagnosing a dispatch that
ran but behaved wrong).

**One hard requirement on any model in the table: ≥32k context.** The target harness's own system
prompt costs ~16k tokens before your brief, so a model loaded at less dies before reading it.

Missing file, unresolvable role or empty value → `BLOCKED`, and say which.

## 2. Build the brief

Same four parts a native dispatch takes, because the agent sees nothing else: the **absolute working
directory** · the **standing constraints** from the user's prompt and the project's rules · the
**task**, one or two lines · the **paths** to its declared inputs. Pass paths, never inlined artifact
content. One brief, one agent, one status line — a dispatch that needs two agents is two dispatches.

**Two shapes, one mechanism** — the same command, a different agent name:

- **Thin (default)** — one dispatch per step. You keep the plan, the gate and the check after every
  `DONE`; the target does bounded content work. A strong orchestrator driving a weak worker.
- **Thick** — one dispatch per phase, to a **primary** agent that drives its own subagents natively.
  Nested delegation across the boundary, paid for by running the gate on the weaker model. Reach for
  it only where the phase is self-contained and its result is checkable from outside.

Target agents must be **primary-capable** (`mode: all` or `mode: primary`). A subagent-only name is
accepted, warned about, and **silently replaced by the default agent** — which runs your brief with
no wall at all. Treat an unexpected agent in the transcript header as a failed dispatch.

## 3. Run it

The command and the platform's launcher come from `{home}/adapter/dispatch/` — read it, substitute,
run. Never write the command from memory: the launcher name is platform-specific (a Windows npm shim
is not executable from a POSIX shell) and getting it wrong looks like a dead connector.

- **Sequential, one at a time.** A local endpoint serves one inference at a time, and a free tier
  measures its queue rather than the model. Two dispatches at once measures neither.
- **Redirect the full output to a transcript** under `~/.agents/dispatch-transcripts/<run>/`, one
  folder per calling run. Keep every transcript; the folder is disposable and the user prunes it.
- Expect minutes, not seconds — a warm local worker is ~30-60 s for a small step; set a timeout that
  reflects the role's model rather than the default.

## 4. Report

Return **only the last status line plus the transcript path**. The agent prompts already end in one
(`DONE <path>` · `RED <path>` · `ESCALATE <reason>` · `BLOCKED <reason>`), and a failed dispatch stays
inspectable without loading it into your context. No status line, or a non-zero exit → `BLOCKED`
with the transcript path; read the transcript only when you are about to act on it.

**A dead connector is `BLOCKED`, always — never fall back to your own model.** Falling back puts the
strongest context in the run on unwalled, untracked work, which is the exact failure
`localagent-workflow` exists to prevent, and it turns a `dynamic-workflow` package into a result
nobody can reason about afterwards. Report the blocker; let the caller decide.

## Watching a run

Every dispatch lands in the target harness's own session store while it is still running — agent,
model, tool calls, tokens, cost — with no flag and no change to the command. `{home}/adapter/dispatch/`
names the viewer where its target has one. This is for the human; you get the status line either way,
so never wait on a viewer.

## Non-goals

Not a sandbox — path scoping is a correctness boundary against a cooperative agent, not a security
one against a hostile one. Not an orchestrator — dispatch never decides what runs next. Not a model
server.
