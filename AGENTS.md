# AGENTS.md

Agent-agnostic guide for **this repo**. `README.md` is for people installing the workspace; this
file is for whoever (human or agent) works *on* it.

## Domain

Meta: authoring an agent-agnostic workspace. The product is **plain Markdown** — instruction files,
skills, templates. No build, no runtime, no tests, no dependencies. Editing prose *is* the work.

## The one rule that shapes everything else

**This repo is not governed by its own content.** Everything under `workspace_TEMPLATE/` is the
artifact being authored — it describes how *other* projects are run, and it does not apply here.
Concretely, in this repo there is:

- no workflow gate, no preflight, no autonomy question — no `/plan`, `/open-sprint`,
  `/dynamic-workflow`, `spec-design`, `maintain-docs`, …
- no sprint, no `artefacts/`, no `docs/` tier system — `backlog/` is the one borrowed convention,
  a plain ticket index for work not being done now, with no board and no sprint above it; `docs/`
  is flat research the template's decisions rest on — read it before re-deciding what it settles
- no `project-initialiser` run, no domain, no scaffolded instruction file — the root `AGENTS.md`
  here is hand-written, not an installed copy

**Only this root `AGENTS.md` applies**, plus the user's selected global workspace rules on language,
MD syntax and commits. Work directly: read the file, discuss, edit, commit.

## Never touch the live workspace

The shared home `~/.agents/` and every harness home (`~/.claude`, `~/.codex`, `~/.config/opencode`)
are an **installed** copy and off limits to any work done here. Never `cp`, never edit a file there to "try something", never repair
it by hand.

- Source of truth is `workspace_TEMPLATE/` in this repo. Change it here.
- Syncing into a selected harness happens **only** when the user runs `workspace-sync`, and only the
  user starts it (`disable-model-invocation`). It is also the repair path, and it lives in this
  repo's `.agents/skills/` because its inputs are `workspace_TEMPLATE/` and `adapters/`. A fresh
  machine comes through `INSTALL.md`, which owns the clone and the once-only personalisation.
- Consequence: a change made here is not live until the user syncs and restarts that harness.
  Say that when handing work over — don't imply an edit took effect.
- An install may legitimately differ in `~/.agents/memory/`, the domain masters, `{home}/projects/`
  and configuration; those are user-owned. A diff there is not automatically a bug.

The live workspace is readable — comparing against it to answer "what would sync change?" is fine.
Writing to it is not.

## Repo layout

```
workspace_TEMPLATE/            ← harness-neutral; installs to ~/.agents/ except where noted
├── AGENTS.md                  ←   the always-loaded global instruction file — per {home}; budget ~2.3k
├── agents/{group}/*.md        ←   agent definitions — per {home}, harness-registered, so not a skill
├── skills/{name}/SKILL.md     ←   one folder per skill; templates/ and references/ beside it
├── project_TEMPLATE/          ←   scaffold copied into each initialised project
├── domains/domain_TEMPLATE/   ←   domain master scaffold
└── memory/MEMORY.md           ←   global memory seed
adapters/{target}/             ← install overlay, one per harness; the ONLY place a harness is named
└── {paths as they land in {home}}
.agents/skills/                ← this repo's own skills, incl. `workspace-sync` — it reads the two
                                 directories above, so it ships with them, not with the template
INSTALL.md                     ← the install guide: prose, no skill, fetchable before anything exists
backlog/                       ← tickets for this repo's own work (`T-NNN-{slug}.md` + `backlog.md`)
docs/                          ← research behind template decisions, dated
├── repo-survey.md             ←   41 coding-agent repos judged; source of the bundle catalog
├── token-savers.md            ←   token savers in depth; settles headroom
└── dispatch-bench.md          ←   nine models, four connectors, one unchanged dispatch command
assets/*.svg                   ← README diagrams (session flow, skill/doc map)
README.md                      ← end-user facing: what this is, install, how it fits together
AGENTS.md                      ← this file
```

`.agents/skills/` holds this repo's **own** skills (`compress`, `workspace-sync`), tracked, with `.claude/skills/{name}`
junctioned to each — the project-scope form of the same one-home rule. `.claude/settings.json` is
tracked as the one exception: it enables the `plugin-dev` plugin **for this repo only**, since
authoring skills, agents and hooks is this repo's domain and nowhere else's. Not tracked (see
`.gitignore`): the rest of the harness dirs `.claude/`, `.codex/`, `.opencode/`, `.serena/`,
`.tokensave`.

## Authoring conventions

- **Skills are discovered only at `skills/<name>/SKILL.md`** — direct children of `skills/`; grouping
  subfolders silently break discovery. Installed, their one home is `~/.agents/skills/` (repo:
  `.agents/skills/`), the Agent Skills standard; the one harness that reads elsewhere is **linked**
  there, never given a copy — a copy is what let the installed skills drift into two mangled versions.
- **Every skill needs frontmatter** `name` + `description`; the description is the *only* thing an
  agent picks by, so it must say when to reach for the skill, not what it contains.
  `disable-model-invocation: true` marks user-only skills (currently `workspace-sync`, `dispatch-configurator`).
- **A skill's helper files** (`templates/`, `references/`) live inside its own folder and are
  referenced from `SKILL.md` — they load on demand, which is the whole point. **Agent definitions are
  the exception:** a harness registers them from its own agents directory and scans that directory
  recursively, so they live in `workspace_TEMPLATE/agents/{group}/` and must install flat. Their
  frontmatter serves both harnesses at once, which holds only because each dialect ignores the
  other's keys — safe: `name` `disallowedTools` `skills` `hooks` (Claude Code), `mode` `permission`
  (OpenCode), `description` (both). **Never a key both define differently** — `model` and `tools`
  each take a different type per harness and make the file invalid in one of them.
- **Only `adapters/` may name a harness**, and only `workspace-sync` reads it. Each is an **install
  overlay** — files already at the paths they land on in `{home}`, whatever a *later* skill needs
  under `adapter/`, so every other skill says `{home}/adapter/…` and an absent folder means that
  target needs none. Hence: **an abstraction with no adapter behind it is a hole, not a design.**
  Prose like "the target adapter loads it" is allowed once a file does it, and a capability no
  adapter implements gets a row saying the target lacks it.
- **`AGENTS.md` is always loaded, in every session, forever.** It says *when* something applies and
  *where* the rest lives — never what a skill does (that duplicates the skill's description and
  goes stale). Adding a paragraph there is a permanent cost; default to putting it in a skill.
- **Placeholders** are `{...}` — substituted by a skill or by the user at install time.
- **Write instructions, not explanations.** Skills and `CONTRACT` blocks are read by a model and
  never shipped to a person, so optimise for tokens, not readability: imperative steps, decisions
  with a stated default, no preamble, no restated rationale, no "this is deliberate because…".
  A clause beats a sentence, a sentence beats a paragraph. Justify a rule only where an agent
  would otherwise break it.
- **Compress on write, not after.** Every edit lands in final compressed form and *integrated* —
  a new rule joins the existing list or sentence in the existing voice, never as its own section.
  A reader who can tell which lines are new, because they explain more or sit in a fresh block,
  is looking at a bad edit.
- **One home per fact.** The same rule stated in `AGENTS.md` and a skill will drift. Reference it.
- **MD syntax** per the global rules: `-` bullets, Unicode trees with aligned `←` comments,
  unpadded pipe tables.
- **README and template stay in sync.** Renaming a skill, changing the kanban semantics or the doc
  tiers means editing `README.md` (and possibly `assets/*.svg`) in the same commit.

## Changing the template

Changes here are cheap to write and expensive to get wrong — they propagate into every project the
user runs. Before editing:

- Ask which of the four layers it belongs to: always-loaded (`AGENTS.md`), on-demand (`skills/`),
  per-project (`project_TEMPLATE/`), or per-harness (`adapters/`, outside the template). Pushing a
  rule down a layer is almost always right; a rule that names a harness has only one legal layer.
- Grep the template for the concept being changed — the workflow skills cross-reference each other
  heavily, and a renamed status token or file path usually has 5–10 call sites.
- Structural changes to work items, doc tiers or sprint semantics touch `AGENTS.md`,
  `project_TEMPLATE/AGENTS.md`, the workflow skills *and* `README.md`. Treat that set as one edit.

The `plugin-dev` skills are the reference for skill mechanics —
`plugin-dev` is project-scoped here, so it is available in this repo and in no other.

## Version control

Compact commits `<type>: <subject & scope>` in very few words — `feat` `fix` `docs` `refactor`
`chore`. Most work here is `docs:` or `refactor:`. Commit on `main`; the user handles anything else.
No sprint branches in this repo.

## Verification

There is nothing to run. "Done" means: the prose says what it means, cross-references resolve,
frontmatter is well-formed, and `README.md` still matches the template. Check by reading, and by
grepping for the terms touched.
