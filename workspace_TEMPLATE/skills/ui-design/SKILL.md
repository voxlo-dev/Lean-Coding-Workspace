---
name: ui-design
description: "Use when designing the look and feel of a UI — colors, themes, typography, layout, mockups, or a design system."
---

# UI Design

Design a UI's look and feel, capture it as a durable **design system** (the styleguide) and durable **mockups**, one per product area, that each feature extends. Invoked from `shape` (styleguide only, new UI project), `spec-design` (one feature's changes to the mockups) or `sprint-autopilot` (every UI ticket of a sprint in one pass). This skill is for designing; a one-off styling tweak is just done.

> **UX first — even for a small feature.** Each time, before wiring anything in: does this hurt the UX, should the layout or grouping be reworked, is every element unambiguous and placed by its relevance, can something be simplified? Prefer the layout change that keeps the experience clean over the minimal one.

**Design only as precisely as the stage needs.** At project/design level settle the styleguide foundations — brand & tone of voice, palette, type, spacing, theming (light/dark), components split into **atoms** (buttons, inputs, chips) and composed **patterns** (cards, list rows, form fields) — then stop. Concrete screens come later, per feature.

This skill ships two **HTML templates**: `styleguide.html` (the design-system sheet) and `layout.html` (phone + desktop mockup frames). Seed them into the project on demand — nothing empty is pre-scaffolded — and keep them **functional, not pretty**: style only enough to communicate a token or a screen's structure.

## 1. Frame the stage & branch

- **Stage:** project/design level → styleguide only · feature/spec level → *this* feature worked into the existing mockups of the areas it touches, grounded in the styleguide — read them first.
- **Branch:** a **web frontend** → build in-repo in the real stack (3a); desktop, game, mobile-native or CLI/TUI → mockups directly (3b).

## 2. Brainstorm UI/UX

Dialogue the look and feel into shape, covering what the stage needs — at styleguide level the system, at feature level this one feature's layout:

- Ask **one question at a time**, multiple-choice where possible: brand/tone, palette, theme(s), typography, spacing/density, key layout patterns, component style, references the user likes.
- Apply **YAGNI**.
- For genuinely **visual** questions (layout options, style directions, side-by-side comparisons) show the options as throwaway HTML so the user *sees* the choice — in the harness's built-in browser where it has one (desktop apps do; screenshot it yourself before asking), else a file the user opens. Conceptual questions stay in the chat.

## 3a. Web frontend → the real stack

When the frontend is written in the project's real stack (Svelte, React, plain HTML/CSS/JS), build it there — through `frontend-design` where installed, which carries the aesthetic craft (distinctive typography, cohesive palette, motion, spatial composition) the mockup-only path does not; else directly, holding that same bar.

- **Ground it in the styleguide** — pass it those tokens so the output stays on-system, not a one-off aesthetic.
- **No styleguide yet?** Design the system first (steps 2 + 4), *then* execute against it, so the system defines the look rather than one component.
- **Still persist (step 4):** the in-repo code is the product, but anything this established or extended in the design system folds back into the styleguide.

## 3b. Everything else → build mockups directly

Once step 2's option screens have settled a direction, change the durable mockups here — the options explore, the mockups are what the spec links.

- **One file per product area** — `docs/design/mockups/{area}.html`, named like the e2e drivers, never for a feature. An area that has one → edit it in place; a fresh copy or a per-feature file is the drift this prevents. A genuinely new area → seed from `templates/layout.html`: self-contained HTML, no build step, phone and desktop frames (delete the one you don't need), viewable in any browser regardless of the real stack.
- Never inline in the spec — it is thrown away at sprint close; the spec links the files and names what this feature changes on them.
- Keep them faithful to the styleguide — paste its tokens into the mockup's `:root`.

## 4. Persist & integrate

- **Styleguide** — `docs/design/Styleguide.html` is the durable, project-wide system; seed it from `templates/styleguide.html` the first time, then edit in place. Built-in light/dark toggle — fill the dark tokens or drop them. Every feature designs against it.
- **Mockups** — `docs/design/mockups/` is the current UI per area, built up feature by feature; the implement packages build against it, the styleguide stays free of concrete layouts.
- **Both are durable docs:** this skill designs into them, `maintain-docs` reconciles them with the shipped UI at sprint close.
- **Pause for user review**, then **commit** the styleguide and any mockups.
