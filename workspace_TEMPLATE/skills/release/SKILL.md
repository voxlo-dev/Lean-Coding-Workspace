---
name: release
description: "Use to publish a version: compose the changelog from every sprint since the last release tag, bump the version, pass the security & data gate (audit, secrets, backup, migration test), open the release PR, run CI, audit it, ship (GitHub release · CI deploy · domain store; production only after the user accepts the risks) and tag. Spans one or more closed sprints and runs after close-sprint, never inside one. Solo projects: on request only. Invokable directly or via /release."
---

# Release

One run = one **published version**: everything on `main` since the last release tag, normally several closed sprints.

**Off unless the project publishes** — `docs/release.md` exists. Solo default: on request only.

**Leave product code alone.** The version bump is the only write outside `artefacts/release-{version}/`; whatever a gate turns up leaves this skill as a recommendation.

**At the user's own risk, production last.** The workspace carries no warranty; a release touching a production environment is refused at first (step 7) until the user has seen its risks and accepted them.

**Irreversible from the tag onward.** Tick each step in the release artefact and re-read it on re-entry — a resumed run never repeats a push, a tag or a submission.

**Tooling split, neither covers the other:** PRs, reviews, tag reads and `run_secret_scanning` → the **github** MCP · CI runs, release creation, tagging, auth → **`gh`**.

## 0. Preflight — all of it, before anything is written

- **Runbook** `docs/release.md` — the authority, whatever domains are installed; theirs only seeded it at init. Missing → **STOP**, offer to write it, pulling the stack's defaults from each recipe's engineering section.
- **Auth** — `gh auth status` *and* one MCP call (`get_me`). Here, not at step 4.
- **Tree** — on `main`, clean, level with the remote, no unmerged sprint branch.
- **Scope** — last tag (`get_latest_release`, else `list_tags`) → `git log <tag>..main`. No tag → first release, whole history.
- **Sprints in scope** — every `artefacts/{sprint}/` closed since that tag, each board all-`done`. Anything still open → a sprint never closed → **STOP**.
- **State & environments** — from the runbook: does the release touch persistent state (database schema, stored data, container volumes, running services), and which target is **production** (live users or real data)? The runbook silent on either → ask, then record the answer there.
- **Resume** — an `artefacts/release-{version}/` with unticked steps → continue there.

## 1. Compose the changelog

From the **sprint archives**, not `git log`: the done ticket lines on each board in scope, their `sprint-decisions.md`, the `spec_*` behaviour deltas. Keep a Changelog groups (Added/Changed/Fixed/Removed), the user's view of each change, breaking changes called out.

Lives in the release artefact, the PR body and the GitHub release — **never as a repo doc**.

## 2. Version & release artefact

**Source:** `AGENTS.md` → Build/test/run names every file and field; `docs/release.md` covers the stack. Semver `major.minor.patch`, no padding or leading zeros (`1.02.3` is invalid).

Propose the bump from step 1 — breaking → major · new capability → minor · fixes only → patch — and **get the user's confirmation**. **Bump every carrier in one commit** (`chore: release {version}` on `main`); where the platform has a separate build number it must increase strictly, and a rejected upload still burns it.

Then seed `artefacts/release-{version}/release.md` from `templates/RELEASE_TEMPLATE.md`.

## 3. Security & data gate

- **Dependencies** — the audit command from `docs/release.md`. **Only `low` passes.** On GitHub read the Dependabot alerts too, via `gh api` — the MCP exposes none.
- **Secrets** — `run_secret_scanning` over the diff hunks of `<tag>..main`. It takes raw content, not paths: batch the hunks, skip gitignored files.
- **Backup** — persistent state in scope → the runbook's backup command (database dump, volume or container snapshot) run against every environment the release changes, stored off that environment, and **a restore proven** on a scratch copy. An unrestored backup does not count.
- **Migrations** — schema or data migrations in scope → the runbook's migration test: forward on a production-like copy, then rollback where the stack supports one, then the suite against the migrated copy. Red, or no rollback and no restore path → blocking.
- **Waivers** — an advisory with no upstream fix passes only via a dated, reasoned entry in `docs/release.md`, re-checked each release. Never for a secret finding: rotate the credential first.

Anything blocking → **STOP**, report, hand the fix to `minimal-`/`dynamic-workflow`, return here.

## 4. Release PR

`create_pull_request`, `main` → `release` (no `release` branch yet → `create_branch` from `main`). Title `Release {version}`, body = the changelog. Honour `.github/PULL_REQUEST_TEMPLATE` if present.

## 5. CI — green before anyone reads it

`gh run watch` / `gh run list --branch main`; the MCP has no Actions surface. Red → **STOP**, fix on `main`, the PR updates itself.

## 6. Release audit

**Not a code review** — every sprint diff was reviewed at its close. This judges the *release*:

- version consistent in every file carrying it, tag name matches
- changelog complete, user-facing, breaking changes flagged
- no secrets, debug flags, temporary logging or commented-out blocks in the diff
- dependency and licence changes intentional
- migration notes where the release needs them
- the runbook's preconditions met (signing keys, store metadata, CI secrets)

Record on the PR: `pull_request_review_write` (create pending) → `add_comment_to_pending_review` per finding → submit. Blocking finding → fix on `main`, re-run 3 and 5.

## 7. Ship

Targets from `docs/release.md`, more than one may apply; a gap there is a runbook bug — fix it in this run.

**Production — refuse first.** Before the first action against a production target, stop and decline to proceed. Lay out for this release: what can be lost (data, uptime, users mid-session), what cannot be undone (migrations without rollback, a published version, a store submission), which safeguards are missing (backup without restore proof, untested migration, no rollback plan, no staging run), and the blast radius if it fails. Continue only on the user's explicit statement that they accept those risks at their own responsibility — recorded verbatim, dated, in the release artefact. A staging or preview target needs none of this.

- **CI pipeline** — trigger and watch only. `gh workflow run <file>` for manual dispatch, else the tag or merge fires it; `gh run watch`, then `gh run view --log-failed`. Suspect an unconfigured secret or environment before the code.
- **Build & GitHub release** — build and verify the artifacts exist, then `gh release create v{version} --notes-file <changelog> <artifacts...>`, which **creates the tag too** — never tag separately. `--prerelease` for rc/beta, `--draft` where the artifacts want eyeballing. The MCP can only *read* releases.
- **Stack-specific** (store, registry) — signing, metadata and tracks per the runbook. **Staged by default**, never straight to production. Record the submission ID so a resumed run checks its status instead of re-submitting. A rejection is not retryable: read it, fix, bump the build number, resubmit.

## 8. Land

**The merge is the point of no return, so its position follows the target:**

- **Re-runnable publication** (CI deploy, GitHub release) — merge into `release`, publish from the tag.
- **Externally judged publication** (app store, registry review) — submit from the PR head, merge only on **acceptance**; a rejection must not leave `release` pointing at a version that never shipped.

Tag `v{version}` on `release` — via `gh release create` where a GitHub release exists, otherwise `git tag` + push. One source, never both.

**Failed release** — say so and stop: close the PR, revert the bump on `main`, capture what broke as a ticket, leave the artefact with its state ticked. A published version is never unpublished; the next one fixes it forward.

## Handoff & boundaries

- Produces: the version bump on `main`, `artefacts/release-{version}/release.md` (scope, changelog, gate results, state, links), the PR, the audit, the tag, whatever the target published.
- Consumes closed sprints; neither opens nor closes one. Touches no `backlog/`, board or `docs/` — except a waiver line in `docs/release.md`.
- Then → `open-sprint`, if it isn't already running.
