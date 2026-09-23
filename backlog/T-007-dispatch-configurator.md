# T-007 — dispatch-configurator

- **Summary:** A skill that probes one machine for dispatch-capable harnesses, providers and models and writes `~/.agents/DISPATCH-GUIDE.md` from what it finds
- **Category:** feature
- **Importance:** medium
- **Effort:** M
- **Depends on:** none — the guide template and the workflow hooks are in place

## Why

**The dispatch configuration is machine-local and the repo must not carry it.** Which harnesses are
installed, which providers hold credit, which models fit the GPU: all of it differs per machine and
none of it survives a copy. The workspace therefore ships `workspace_TEMPLATE/DISPATCH-GUIDE_TEMPLATE.md`
and nothing else; the filled guide is user-owned, `workspace-sync` never writes or overwrites it.

**Written by hand it goes stale silently.** A model dropped from the local server, a credential that
expired, an agent that fell back to `mode: subagent` after a sync — each one turns into a dispatch
that looks like it worked. The probe is cheap and the failure is not, so the skill exists to make
re-running it cheaper than auditing the file.

**It keeps the launch command out of the adapters.** The guide carries it per machine, so no
harness overlay needs a dispatch recipe and [`T-006`](T-006-generated-adapters.md) has one surface
less to generate.

## What

**One skill, `dispatch-configurator`, user-invoked, writing exactly one file.** Fresh and repair are
the same run: re-probe, show the diff against the existing guide, write on approval.

**Probe, never assume** — every row must come from a command that answered:

| Question | Lever |
| --- | --- |
| Which harness can be dispatched to? | its binary on PATH, and the platform's launcher name |
| Which providers resolve? | the target's own auth/config listing |
| Which model keys are valid? | the target's model listing, filtered to what the user actually wants |
| Which agents are dispatchable? | the agent directory **plus its `mode`** — subagent-only is the silent-failure case |

**The wall check is part of configuring, not of using.** Path denies are last-match-wins, so a
`"*": allow` after the deny globs voids them with no warning and the run still looks green. The
skill offers one probe dispatch — an allowed read, a denied read, a status line — and records the
result. That probe is also the only honest way to fill a model's note.

**Fill rules the template already states, and the skill must hold to:** no version in a heading
(it drifts at the next update, so `verified on v{x}` goes in the notes) · notes are one clause that
changes a choice, never benchmark figures or dates · a model nobody tested is marked untested rather
than described · secrets stay out — reference the config path, since the guide is read into context
on every dispatching run.

**Interview, don't guess, for the one thing no probe answers:** the workflow defaults. Which model
each role gets and what its fallback is, is the user's call — the probe supplies the candidates.

**Non-goals:** not an installer — it configures no provider, installs no model server, logs into no
vendor; it reports what is missing. Not a benchmark harness. Not a dispatcher.

## Open questions

- Where does the guide belong when several machines share one `~/.agents/` (sync, dotfiles)? A
  per-host filename, or is one machine per install the honest assumption?
- Should the skill verify the guide against reality on demand (a `--check` shape) or only rewrite it?
- Is the probe dispatch worth its cost on every run, or only when the agent set or a provider changed?

## Links

- `workspace_TEMPLATE/DISPATCH-GUIDE_TEMPLATE.md` — the file this skill fills; its CONTRACT block
  holds the fill rules
- [`dispatch-bench.md`](../docs/dispatch-bench.md) — the measurements the mechanism rests on: one unchanged
  command across nine models and four connector types, and the wall enforced at the tool layer
