---
name: maintain-memory
description: "Use at the memory step of the minimal and dynamic workflows, or whenever a durable learning, decision, or gotcha emerged that a future session should know. Curates the layered MEMORY.md memory and prunes stale entries."
---

# Maintain Memory

The workspace uses Markdown memory, not a required plugin. `workspace-install` enables each scope only where the selected harness can load it. Curate the right fact, keep the index lean and **remove what's no longer true**.

## The three scopes — pick the narrowest that fits

| Scope | Lives in | Loaded | Use for |
| --- | --- | --- | --- |
| **Project** | `{home}/projects/<repo>/memory/` | native, auto, every session where the target has one | facts true only for this repo |
| **Domain** | `{home}/domains/{x}-domain/DOMAIN-MEMORY.md` | imported per project by `project-initialiser` | facts true for every project of this domain |
| **Global** | `{home}/memory/MEMORY.md` | wired once by `workspace-install` | facts true everywhere |

The files stay shared; only the wiring differs, and a harness offers exactly one of three levers — an **import** in the instruction file (Claude Code's `@`), an **instructions list** in the config (OpenCode's), or **inlining** where there is neither (Codex). `workspace-install` picks one per target, `project-initialiser` repeats it per repo. Inlining competes for an instruction-size budget that truncates silently, so keep it lean.

Default to **project**; promote only once a learning is clearly that broad. When scope becomes clearer later, **move** the entry.

## What to save

- Decisions + their rationale, gotchas, non-obvious constraints, build/debug insights.
- One fact per entry, concrete and self-contained. Link related entries by name.
- **Machine-bound facts belong here:** absolute paths, local installations, personal tool setup, this-machine-only quirks. The mirror rule: anything system-independent and generally true → `docs/dev.md` via `maintain-docs`. **One home, never both** — a duplicated fact guarantees one stale copy. Close calls go to `docs/dev.md`: a **delegated subagent reads the repo, not this memory**, so what one needs to work (build invocations, driver choices, framework quirks) is reliable only there or in its handoff.
- **Memory is for what other records miss** — code structure, git history and anything in `AGENTS.md` or the harness shim is already captured there.
- **Memory holds what is *true*, the board what is *to do*** — a bug, a todo, a question to settle becomes a ticket in `backlog/`.

## How to write

1. Pick the scope, open its `MEMORY.md`.
2. Add the fact — short ones inline in a topic file; keep `MEMORY.md` itself a one-line index (`- [Title](file.md) — hook`). The project `MEMORY.md` only auto-loads its first ~200 lines, and domain/global load **in full**, so keep all three lean.
3. Convert relative dates to absolute.

## Prune — every pass

- Delete entries that are **wrong, contradicted, or no longer relevant**. Stale memory is worse than none.
- Before relying on an entry that names a file, function or flag, **verify it still exists** — fix or drop it if not.
- Merge duplicates; demote an over-broad entry to a narrower scope.

## Spin off a skill — if a reusable procedure emerged

Create it with superpowers' **skill-creator** and place it by scope: global → `~/.agents/skills/{skill-name}/` · domain → the domain master under `{home}/domains/{x}-domain/` · project → the repo's `.agents/skills/`. It must be a **direct** child of `skills/` — grouping subfolders aren't discovered.

A brand-new skill *folder* is usually discovered only on the next session — flag this to the user, as with a brand-new domain/global memory file: that only enters context once the target's adapter wires it (`domain-initialiser` / `workspace-install` do this) and the session restarts. Project memory needs no wiring where the target has it natively.
