---
name: compress
description: "Use when writing or editing any Markdown in this repo (CLAUDE.md, a SKILL.md, AGENTS.md, project_TEMPLATE, README), and again when a finished section reads fat or the user asks for token compression. Two modes: compress-on-write lands the edit in final form, compress-after-write re-passes existing text."
---

# Compress

Two modes, best used together: write compressed, then re-pass the finished section. **On-write** gets the shape right — a fat draft trimmed later keeps its seams. **After-write** catches what only becomes visible once the whole thing exists: duplication across sections, a rule that outgrew its host, scaffolding nobody reads.

## The five tests

Both modes run these. Every line here is read by a model mid-task.

- **Behaviour** — what does the model do differently because of this line? Nothing → it doesn't exist.
- **Host** — does a list, table row or sentence already cover the topic? Then the rule belongs *inside* it; a fresh section is the last resort.
- **Home** — is the fact already stated elsewhere in the repo? Reference it, never restate. Grep before assuming it isn't.
- **Seam** — can a reader tell which lines are new or touched? Visible seams mean an explaining voice or a block where an integration belonged.
- **Deletion** — remove the clause; does any agent behave differently? No → it stays removed.

## Compress on write

1. Answer **behaviour**, **host** and **home** before typing — the host decides where the edit goes, so find it first.
2. **Rules, not reasons.** State the imperative; add the *why* only where an agent would otherwise break the rule, as one clause in the same sentence.
3. **Clause > sentence > paragraph.** Merge related rules with `·` or `—` rather than listing them.
4. **Match the surrounding density** — the voice of the lines above and below, not your natural one.
5. Cut on sight: preamble, "note that", restated rationale, examples where the rule is already unambiguous, hedges, any recap of what the text just said.
6. **Seam** and **deletion** test on the new lines, then save.

## Compress after write

On a finished file or section, never mid-draft.

1. **Read it whole first.** Local edits without the full picture create the duplication you came to remove.
2. **Map what it asserts** — one line per rule. Two lines saying the same thing → merge into the stronger host. A rule with a home elsewhere → replace with a reference.
3. **Deletion test every clause**, top to bottom. Delete first, restore only what fails the test.
4. **Rewrite the survivors into the fewest sentences that keep every rule.** Restructure the host — shaving words while the structure stands is what leaves seams, and buys little.
5. **Verify against the map**: every rule still present, and report the size delta.
