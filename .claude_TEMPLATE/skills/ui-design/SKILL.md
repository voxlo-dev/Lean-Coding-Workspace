---
name: ui-design
description: "Use when designing the look and feel of a UI — colors, themes, typography, layout, mockups, design system. Invokable from project-designer (styleguide level) and spec-design (concrete layouts for a feature). Routes web frontends to Claude Design; builds HTML mockups otherwise."
---

# UI Design

Design a UI's look and feel, capture it as a durable **design system** (the styleguide)
and — when a feature is being built — concrete **mockups**. Invoked from
`project-designer` (set the styleguide only) or `spec-design` (lay out a specific
feature). For a one-off styling tweak, just do it — this skill is for designing.

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

Invoke superpowers' **brainstorming** skill for the dialog; don't reinvent it. Cover only
what the stage needs: brand/tone, color palette, theme(s) (light/dark), typography,
spacing/density, key layout patterns, component style, references the user likes. Scale
ruthlessly — at styleguide level don't design screens; at feature level don't re-litigate
the brand.

## 3a. Web frontend → Claude Design handoff

Claude Design (claude.ai/design) is a separate Anthropic Labs tool; the link is one-way
**Design → Code** with no MCP — see [[claude-design]].

- Write a **handoff brief**: design intent, brand/tone, the palette and type from step 2, hard constraints, and exactly which screens/components are needed (none at styleguide level — just the system).
- Hand the brief to the user to run in Claude Design. They export the result (handoff bundle / standalone HTML / PDF) into the repo.
- **Ingest** the export: distil the design system into `docs/design/Styleguide.html` (seed it from `templates/styleguide.html`, then fill the tokens); place any screen mockups with the spec (step 4).

## 3b. Everything else → build mockups directly

- Seed mockups from `templates/layout.html` — self-contained HTML (no build step), with phone and desktop frames; delete the frame you don't need. Viewable in any browser regardless of the real tech stack.
- Simple UI → embed the snippet directly in the spec's UI section. Sophisticated UI → put the files under `docs/design/mockups/` and link them from the spec.
- Keep them faithful to `docs/design/Styleguide.html` — paste its tokens into the mockup's `:root`.

## 4. Persist & integrate

- **Styleguide** — `docs/design/Styleguide.html` is the durable, project-wide system (brand & tone of voice, palette, type, spacing, theming, components as atoms + patterns); seed it from `templates/styleguide.html` the first time, then edit in place. It has a built-in light/dark toggle — fill the dark tokens or drop them. It's the single source every feature designs against. Extended later via `maintain-docs`.
- **Mockups** are per-feature, not global: they live in the spec (or `docs/design/mockups/`) so the spec's implement package builds against them. Never pour concrete layouts into the styleguide.
- **Pause for user review** before committing anything.
- **Commit** the styleguide and any mockups.

When **invoked from `project-designer`**, run steps 1–2 then set the styleguide only
(stop before per-feature mockups) and return. When **invoked from `spec-design`**, the
styleguide already exists — design this feature's layout against it.
