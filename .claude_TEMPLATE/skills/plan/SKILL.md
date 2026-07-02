---
name: plan
description: "Use to turn an idea, a rough draft, or a brainstorming transcript into a clear end-user requirements plan — plan.md, the *Lastenheft*. Categorises bugs/features/UX/refactors, defines user stories and UI flows, phrased unambiguously. Non-technical: no code exploration, no architecture. Recommended input for spec-design but optional; hand the plan to any workflow."
---

# Plan

Turn a fuzzy idea into a crisp, **end-user-level** requirements plan — the *Lastenheft*
(what the user wants and why). Stay at product/UX level: **no code exploration, no
architecture, no implementation decisions**; `spec-design` later turns the plan into the
technical *Pflichtenheft*. Output: `docs/plans/{topic}-plan.md`.

**Sensible context — only what already exists, never the codebase:** existing UI mockups
(`docs/design/`) and optionally the wiki (`docs/wiki/`). Pull these in when present; don't go hunting
through source.

## 1. Pick the starting point

Match the situation:

- **User has a draft `plan.md`** → read it, then sharpen and challenge it into the template.
- **A brainstorming transcript** (e.g. audio → text) → **extract the signal**: mine requirements, ideas and pain points out of the messy transcript, discard the filler.
- **Nothing yet** → run a **plan meeting** with the user — an equal-footing dialogue where you contribute ideas as much as you ask questions, building the plan up together. Not one-question-at-a-time interviewing; genuine co-planning.

## 2. Categorise everything

Sort every item into **bugs · features · UX · refactors** and keep them distinct. One item, one category.

## 3. Define user stories & UI flows

- A **user story** for each meaningful capability — *as a {role}, I want {capability}, so that {value}*.
- A **UI flow** wherever it's a screen journey — the steps the user walks through. Ground flows in existing mockups (`docs/design/`) and the wiki when they exist.

## 4. Challenge as you go

This is the value over a raw transcript. For each item ask:

- **Unclear / ambiguous?** — pin it down; the plan must be unmistakable where the input was fuzzy.
- **Sensible?** — is the user's idea actually worth doing, or is there a better framing?
- **Good UX?** — does the flow serve the user, or add friction?

Surface concerns to the user and resolve them before writing.

## 5. Write the plan

Copy this skill's `templates/PLAN_TEMPLATE.md` to `docs/plans/{topic}-plan.md`, fill it,
and get the user's approval. The plan is deliberately **unambiguous** — the opposite of the
input it came from.

## Handoff

The plan is an **input artifact**, not a workflow step. `plan` is **not** wired into any workflow — it runs standalone and produces the doc. Hand `docs/plans/{topic}-plan.md` to `spec-design` (recommended) or any workflow.

