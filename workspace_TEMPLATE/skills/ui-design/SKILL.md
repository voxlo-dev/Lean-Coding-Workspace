---
name: ui-design
description: "Use when designing the look and feel of a UI — colors, themes, typography, layout, mockups, or a design system."
---

# UI Design

Design a UI's look and feel, capture it as a durable **design system** (the styleguide) and — when a feature is being built — concrete **mockups**. Invoked from `plan` (styleguide only, new UI project) or `spec-design` (one feature's layout). This skill is for designing; a one-off styling tweak is just done.

> **UX first — even for a small feature.** Each time, before wiring anything in: does this hurt the UX, should the layout or grouping be reworked, is every element unambiguous and placed by its relevance, can something be simplified? Prefer the layout change that keeps the experience clean over the minimal one.

**Design only as precisely as the stage needs.** At project/design level settle the styleguide foundations — brand & tone of voice, palette, type, spacing, theming (light/dark), components split into **atoms** (buttons, inputs, chips) and composed **patterns** (cards, list rows, form fields) — then stop. Concrete screens come later, per feature.

This skill ships two **HTML templates**: `styleguide.html` (the design-system sheet) and `layout.html` (phone + desktop mockup frames). Seed them into the project on demand — nothing empty is pre-scaffolded — and keep them **functional, not pretty**: style only enough to communicate a token or a screen's structure.

## 1. Frame the stage & branch

- **Stage:** project/design level → styleguide only · feature/spec level → a concrete layout for *this* feature, grounded in the existing styleguide.
- **Branch:** a **web frontend** → build in-repo through `frontend-design` (3a); desktop, game, mobile-native or CLI/TUI → mockups directly (3b).

## 2. Brainstorm UI/UX

Dialogue the look and feel into shape, covering what the stage needs — at styleguide level the system, at feature level this one feature's layout:

- Ask **one question at a time**, multiple-choice where possible: brand/tone, palette, theme(s), typography, spacing/density, key layout patterns, component style, references the user likes.
- Apply **YAGNI**.
- For genuinely **visual** questions (layout options, style directions, side-by-side comparisons) reach for the **Visual Companion** below so the user *sees* the choice; conceptual questions stay in the terminal.

## 3b. Everything else → build mockups directly

Explore directions in the **Visual Companion** while the choice is open; once it's settled build the durable mockup here — the companion explores, `layout.html` is the artifact that lands in the spec.

- Seed from `templates/layout.html` — self-contained HTML, no build step, phone and desktop frames (delete the one you don't need), viewable in any browser regardless of the real stack.
- Simple UI → embed the snippet in the spec's UI section. Sophisticated UI → files under `docs/design/mockups/`, linked from the spec.
- Keep them faithful to the styleguide — paste its tokens into the mockup's `:root`.

## 3a. Web frontend → frontend-design

When the frontend is written in the project's real stack (Svelte, React, plain HTML/CSS/JS), invoke `frontend-design` for the code craft. It owns the aesthetic execution — distinctive typography, cohesive palette, motion and spatial composition — that the mockup-only path does not produce.

- **Ground it in the styleguide** — pass it those tokens so the output stays on-system, not a one-off aesthetic.
- **No styleguide yet?** Design the system first (steps 2 + 4), *then* execute against it, so the system defines the look rather than one component.
- **Still persist (step 4):** the in-repo code is the product, but anything this established or extended in the design system folds back into the styleguide.

## 4. Persist & integrate

- **Styleguide** — `docs/design/Styleguide.html` is the durable, project-wide system; seed it from `templates/styleguide.html` the first time, then edit in place. Built-in light/dark toggle — fill the dark tokens or drop them. Every feature designs against it; `maintain-docs` extends it later.
- **Mockups** are per-feature: they live in the spec (or `docs/design/mockups/`) so the implement package builds against them, while the styleguide stays free of concrete layouts.
- **Pause for user review**, then **commit** the styleguide and any mockups.

## Visual Companion

An interactive browser tool for the **visual** parts of the brainstorm and a live pre-step to the finished mockup: you write HTML wireframes / option screens, the user sees them in a browser and clicks to choose, you read the selection and iterate. It reuses superpowers' companion server — no separate install:

- **Scripts:** the `superpowers` companion server, under its plugin cache — Claude Code: newest version dir under `{home}/plugins/cache/claude-plugins-official/superpowers/*/skills/brainstorming/scripts/`; elsewhere, the same path under that target's plugin cache. Start with `start-server.sh --project-dir <repo>`; on Windows set `run_in_background: true` and read `$STATE_DIR/server-info` next turn for the URL.
- **Full loop & CSS classes:** read `visual-companion.md` next to those scripts before driving it.
- **Consent:** offer it once before first use (opens a local URL, token-intensive), then decide per question.
- **Converge:** the moment a direction is picked, build the durable mockup from `templates/layout.html`. That mockup — not the companion screens — is what lands in the spec.
