---
name: maintain-memory
description: "Use at the memory step of the minimal and dynamic workflows, or whenever a durable learning, decision, or gotcha emerged that a future session should know. Curates the layered MEMORY.md memory and prunes stale entries."
---

# Maintain Memory

The workspace uses Claude Code's **native file memory** — plain Markdown, no plugin. A
`MEMORY.md` index per scope is loaded each session; detailed topic files load on demand.
This skill is the deliberate curation pass: write the right fact to the right scope,
keep the index lean, and **remove what's no longer true**.

## The three scopes — pick the narrowest that fits

| Scope | Lives in | Loaded | Use for |
| --- | --- | --- | --- |
| **Project** | `~/.claude/projects/<repo>/memory/` | native, auto, every session | facts true only for this repo |
| **Domain** | `~/.claude/domains/{x}-domain/DOMAIN-MEMORY.md` | `@import` in domain projects | facts true for every project of this domain |
| **Global** | `~/.claude/memory/MEMORY.md` | `@import` from `~/.claude/CLAUDE.md` | facts true everywhere, across all projects |

Default to **project**. Only promote to domain/global once a learning is clearly that
broad. When scope becomes clearer later, **move** the entry (don't copy it).

## What to save

- Decisions + their rationale, gotchas, non-obvious constraints, build/debug insights.
- One fact per entry, concrete and self-contained. Link related entries by name.
- **Machine-bound facts belong here, not in docs:** absolute paths, local installations,
  personal tool setup, this-machine-only quirks. The mirror rule: anything
  system-independent and generally true → `docs/dev.md` via `maintain-docs`. **One home,
  never both** — a fact duplicated across memory and docs guarantees one stale copy.
- **Skip what's already recorded elsewhere:** code structure, git history, and anything
  in `CLAUDE.md` / `AGENTS.md`. Memory is for what those *don't* capture.

## How to write

1. Pick the scope (above). Open that scope's `MEMORY.md`.
2. Add the fact — short ones inline in a topic file; keep `MEMORY.md` itself a one-line
   index (`- [Title](file.md) — hook`). The project `MEMORY.md` only auto-loads its
   first ~200 lines, and domain/global load **in full**, so keep all three lean.
3. Convert relative dates to absolute.

## Prune — every pass

- Delete entries that are **wrong, contradicted, or no longer relevant**. Stale memory
  is worse than none.
- Before relying on an entry that names a file, function, or flag, **verify it still
  exists** — fix or drop it if not.
- Merge duplicates; demote an over-broad entry to a narrower scope.

## Spin off a skill — if a reusable procedure emerged

When the work produced a reusable procedure worth keeping, create a skill with
superpowers' **skill-creator** and place it by scope:

- global → `~/.claude/skills/{skill-name}/` (must be a **direct** child of `skills/`; grouping subfolders aren't discovered)
- domain → the domain master under `~/.claude/domains/{x}-domain/`
- project → the repo's `.claude/skills/`

A brand-new skill *folder* is usually discovered only on the next session — flag this to the user.

## Note

A brand-new domain/global memory file only enters context once its `@import` is wired
(`domain-initialiser` / `workspace-install` do this) and the session restarts. Project
memory needs no wiring — it's native.
