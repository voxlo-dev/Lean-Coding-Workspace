---
name: harness-onboard
description: "Use to add a harness the workspace has no adapter for, or to re-verify an adapter after a harness release moved something: probe the harness, fill adapters/{target}/MANIFEST.md with verified facts, derive the overlay from it, prove it. Once per harness, not per machine — installing is workspace-sync. Explicit-invoke."
disable-model-invocation: true
---

# Harness Onboard

Writes `adapters/{target}/MANIFEST.md` — the facts — and `adapters/{target}/overlay/`, derived from them. Both are tracked: the overlay is a cache of the manifest, so nobody pays the probe cost twice and a diff shows when a release moved a path. `{target}` = a lowercase slug, **one per install, not per binary** — a wrapper pointing the binary at its own config directory is its own target. A harness used only as a dispatch target needs no adapter; `dispatch-configurator` probes it per machine.

**Evidence over documentation.** Every row comes from something that answered on this machine; nothing answered → `unknown`, the harness lacks it → `gap`. Both stay visible; neither gets guessed — a wrong seed multiplies into every install.

## 1. Frame

- Existing manifest → **re-verify**: probe every row verified on an older version than the installed one, plus whatever the user names; keep the rest.
- New target → fill `templates/MANIFEST.md`. Read one existing manifest for the kind of answer a row wants, never for its value.
- Resolve the binary and its version first — not on `PATH` is itself a fact for the **Binary** line.

## 2. Levers first

Before any row, find what makes rows checkable without a model call: `--help` of every subcommand, recursively — `debug`, `doctor`, config print or validate, prompt rendering, session export, the transcript store, a published config schema. Each goes into **Verification levers** with what it shows. They turn an unfalsifiable install into a checkable one; a harness with none gets its checks from scratch runs, and the manifest says so.

## 3. Probe each row

Strongest evidence first; stop at the first that answers:

1. **A lever's output.**
2. **A throwaway probe**, removed afterwards — a stub skill, agent or import in a scratch repo or a junctioned folder, then the lever or one run to see it picked up. A file existing proves nothing; only the harness reporting it does.
3. **A binary string dump** (`grep -a`, `strings`) — paths, config keys and defaults the docs omit.
4. **Official docs or schema, fetched** — never recalled.
5. **Upstream issues** — a lead, never a verdict.

- Check the check: a comparison that filters lines (say, those containing `{`) can skip exactly the wrong one.
- A row that needs a model run: point the harness at a local endpoint through its custom-provider config instead of a subscription, and fill **Custom provider** on the way. Scope tools per run rather than bypassing permissions. A small model proves wiring, not workflow quality.
- **Dispatch is proven by the child's own record** — transcript, export or rollout naming the agent and carrying its instructions — never by the parent's report.

## 4. Derive the overlay

Files sit at the exact paths they land on in `{home}`; `overlay/adapter/` holds the three folders `workspace-sync` step 3 defines. From the manifest:

- **Instruction shim** `overlay/{global instruction file}` — only where that file isn't `AGENTS.md`: it imports `AGENTS.md` and, given an import lever, `~/.agents/memory/MEMORY.md`.
- **`adapter/merge/`** — the home config's instructions list where that is the memory lever, and capability entries (MCP, plugins) in the harness's own shape.
- **`adapter/project-merge/`** — the project config's MCP block and pre-approval, plus the domain-memory entry where the lever is a list.
- **`adapter/project/`** — the project shim, where **Project → Instruction shim** says needed.
- `gap` → no file; nothing invented to paper over it.
- The workspace invariants are inputs, not probe results: shared content lives once in `~/.agents/`, harnesses are pointed at it, a non-native skills root is linked per folder, agents install flat in the manifest's format with frontmatter per the repo's `AGENTS.md`.

## 5. Join the enumerations

Grep the repo for an existing target's home path: every hit outside `adapters/` — `workspace_TEMPLATE/AGENTS.md`, `INSTALL.md`, `README.md` — is a list the new target joins.

## 6. Prove it

The install is the user's: they run `workspace-sync` for this target, whose step 7 uses the new levers. Then one end-to-end run in a throwaway repo — a `minimal-workflow` change with docs and commit, one agent dispatch, a memory read — its outcome into **Proven**. Commit `feat: {target} adapter`, or `docs: {target} manifest re-verified`.
