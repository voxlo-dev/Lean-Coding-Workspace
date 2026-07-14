---
name: ui-design
description: "Use when designing the look and feel of a UI — colors, themes, typography, layout, mockups, design system. Invokable from plan (styleguide level, new UI project) and spec-design (concrete layouts for a feature). Routes web frontends to Claude Design; builds HTML mockups otherwise."
---

# UI Design

Design a UI's look and feel, capture it as a durable **design system** (the styleguide)
and — when a feature is being built — concrete **mockups**. Invoked from
`plan` (set the styleguide only, new UI project) or `spec-design` (lay out a specific
feature). For a one-off styling tweak, just do it — this skill is for designing.

> **UX first — even for a small feature.** Never integrate a feature into the UI by the path of least effort. Each time, ask: does this hurt the UX? Should the layout be reworked or elements regrouped? Is every element unambiguous and positioned by its relevance — can something be simplified? Prefer the layout change that keeps the experience clean over the minimal one that just squeezes the feature in.

**Design only as precisely as the stage needs.** At project/design level, settle the
styleguide foundations — brand & tone of voice, palette, type, spacing, theming
(light/dark), and components split into **atoms** (buttons, inputs, chips) and composed
**patterns** (cards, list rows, form fields) — then stop. No concrete screens; layouts and
mockups come later, per feature, when that feature is implemented.

This skill ships **HTML templates** in `templates/` next to it — `styleguide.html` (the
design-system sheet) and `layout.html` (phone + desktop mockup frames). Seed them into the
project on demand; nothing empty is pre-scaffolded into `docs/`. Keep them **functional, not
pretty** — style only enough to communicate a token or a screen's structure.

## 1. Frame the stage & branch

- **Stage:** project/design level → styleguide only · feature/spec level → concrete layout for *this* feature, grounded in the existing styleguide.
- **Branch:** a **web frontend** (→ Claude Design handoff, step 3a) or anything else — desktop, game, mobile-native, CLI/TUI (→ build mockups directly, step 3b)?

## 2. Brainstorm UI/UX

Dialogue the look and feel into shape — cover only what the stage needs and scale
ruthlessly (at styleguide level don't design screens; at feature level don't re-litigate
the brand):

- Ask **one question at a time**, multiple-choice when you can: brand/tone, palette, theme(s) (light/dark), typography, spacing/density, key layout patterns, component style, references the user likes.
- Apply **YAGNI** — only what the stage needs.
- For genuinely **visual** questions — layout options, style directions, side-by-side comparisons — reach for the **Visual Companion** (below) so the user *sees* the choice instead of reading it. For conceptual/text questions ("what does *playful* mean here?", which features are in scope) stay in the terminal.

## 3a. Web frontend → Claude Design handoff

Claude Design (claude.ai/design) is a separate Anthropic Labs tool; the link is one-way
**Design → Code** with no MCP — see [[claude-design]].

- Write a **handoff brief**: design intent, brand/tone, the palette and type from step 2, hard constraints, and exactly which screens/components are needed (none at styleguide level — just the system).
- Hand the brief to the user to run in Claude Design. They export the result (handoff bundle / standalone HTML / PDF) into the repo.
- **Ingest** the export: distil the design system into `docs/design/Styleguide.html` (seed it from `templates/styleguide.html`, then fill the tokens); place any screen mockups with the spec (step 4).

## 3b. Everything else → build mockups directly

Explore layout directions in the **Visual Companion** first (below) if the choice is still
open; once it's settled, build the durable mockup here — the companion is for exploring,
`layout.html` is the artifact that lands in the spec.

- Seed mockups from `templates/layout.html` — self-contained HTML (no build step), with phone and desktop frames; delete the frame you don't need. Viewable in any browser regardless of the real tech stack.
- Simple UI → embed the snippet directly in the spec's UI section. Sophisticated UI → put the files under `docs/design/mockups/` and link them from the spec.
- Keep them faithful to `docs/design/Styleguide.html` — paste its tokens into the mockup's `:root`.

## 4. Persist & integrate

- **Styleguide** — `docs/design/Styleguide.html` is the durable, project-wide system; seed it from `templates/styleguide.html` the first time, then edit in place. Built-in light/dark toggle — fill the dark tokens or drop them. The single source every feature designs against; extended later via `maintain-docs`.
- **Mockups** are per-feature, not global: they live in the spec (or `docs/design/mockups/`) so the spec's implement package builds against them. Never pour concrete layouts into the styleguide.
- **Pause for user review** before committing anything.
- **Commit** the styleguide and any mockups.

## Visual Companion

An interactive browser tool for the **visual** parts of the brainstorm (step 2) and a live
pre-step to the finished mockup (step 3b): you write HTML wireframes / option screens, the
user sees them in a browser and clicks to choose, you read the selection and iterate. It
reuses superpowers' companion server — no separate install:

- **Scripts:** newest version dir under `~/.claude/plugins/cache/claude-plugins-official/superpowers/*/skills/brainstorming/scripts/`. Start with `start-server.sh --project-dir <repo>`; on Windows set `run_in_background: true` and read `$STATE_DIR/server-info` next turn for the URL.
- **Full loop & CSS classes:** read `visual-companion.md` next to those scripts before driving it.
- **Consent:** offer it once before first use (opens a local URL, token-intensive), then decide per question — browser for visual choices, terminal for text.
- **Converge:** the companion is for exploring; the moment a direction is picked, build the durable mockup from `templates/layout.html` (step 3b). That mockup — not the companion screens — is what lands in the spec.
