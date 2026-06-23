# BUILD_SPEC Blueprint

## Purpose

Template for `BUILD_SPEC_<ProjectName>.md`. Worker 2 creates this in Phase 2.

The BUILD_SPEC is the **single authoritative architecture document**. It drives the WP breakdown, the acceptance criteria, and the W4 validation gate. Update it whenever architecture, scope, or interfaces change.

User stories and their acceptance criteria live in `USER_STORIES.md` (separate file). The BUILD_SPEC references them; it does not repeat them.

Use codegraph (when the repo is indexed) as the structural sidecar for discovery — record decisions, constraints, interfaces, and WP specs here, not the file-level relationships the graph already holds.

## 1. Project Overview

- **Project name:**
- **Target vision:** (one sentence: what this system does for whom)
- **Primary user group / consumers:**
- **Non-goals:** (explicitly what this project does NOT do)
- **UI language / locale:**

## 2. Scope and Deliverables

- **User stories:** see `USER_STORIES.md`
- **Must-have requirements:** (P0 — project fails without these)
- **Should-have requirements:** (P1 — important but not blocking)
- **Nice-to-have requirements:** (P2 — only if time allows)
- **Explicitly out of scope:**

## 3. System Architecture

- **Graph basis:** (codegraph used for grounding + index date, or "none — greenfield plan only")
- **Frontend stack:** (framework, language, build tool)
- **Backend stack:** (language, framework, runtime)
- **Data storage:** (DB type, ORM, file storage)
- **External integrations:** (APIs, services, message queues)
- **Runtime environment:** (local, container, cloud, serverless)
- **Key subsystems:** (concise summary — detail lives in the codegraph index)
- **Key architecture decisions:** (choices + rationale — e.g. "REST over GraphQL because...")

## 4. Data Architecture

- **Primary data sources:**
- **Core data models / schemas:** (field names, types, relationships)
- **Normalisation rules:**
- **Consistency and integrity rules:**
- **Data flow:** (how data moves between components)

## 5. Component Map

For each component in scope:

```
### <Component Name>
- Change type: create | modify | delete
- Responsibility: [one sentence]
- Interfaces:
  - Input: [type/shape]
  - Output: [type/shape]
- User stories: US<N>, US<M>
- Assigned to WP: WP<N>
```

## 6. API and Interfaces

- **Endpoints / tool surfaces:** (path, method, purpose)
- **Request / response structure:**
- **Authentication / authorization:**
- **Error cases and expected responses:**
- **Persistence behaviour:**

## 7. Quality Gates

- **Lint / typecheck / test commands:** (exact commands W3 must run)
- **Execution order:**
- **Abort criteria:** (what stops the pipeline)
- **Definition of Done (project-level):**
- **Test framework and runner:**
- **Active test levels:** (passed in by the dispatcher — record current state here)
  - Smoke tests: enabled | disabled
  - Integration tests: enabled | disabled
  - Full E2E: enabled | disabled

## 8. Validation and Test Strategy

- **Test levels active:** (cross-reference Section 7)
- **Test data sources:** (fixtures, seeds, deterministic generators — no ad-hoc LLM data)
- **Known flaky areas:** (patterns to avoid in test design)

## 9. Work Package Breakdown

One block per WP. This section replaces separate task files — each WP must have enough detail for W3 to implement without asking W2 again.

### WP<N> — <Title>
- **Status:** planned
- **Depends on:** WP<X> | none
- **Scope:** (explicit list of what this WP changes or creates)
- **Out of scope:** (what this WP does NOT do)
- **User stories covered:** US<N>, US<M>
- **Acceptance Criteria:**
  1. (observable, testable — reference US ACs by number where applicable)
  2. ...
- **Definition of Done:** (observable outcome confirming all ACs are met)
- **Key files:** (files to modify or create — leave empty if unknown, never invent paths)
- **Architecture notes:** (task-local constraints, interfaces, invariants)
- **Handover summary:** *(filled by W3 on completion)*

## 10. Operational Rules

- **Logging:**
- **Monitoring:**
- **Recovery / backups:**
- **Security and access rules:**

## Worker 2 Checklist

- [ ] Project overview and non-goals aligned with PLAN.md?
- [ ] USER_STORIES.md written with all stories expanded from PLAN.md?
- [ ] All stories have numbered, observable ACs and a definition of done?
- [ ] All components in scope defined with interfaces and US/WP references?
- [ ] Every WP in Section 9 has scope, out-of-scope, ACs, DoD, and US references?
- [ ] Every AC observable and testable (no interpretation gaps)?
- [ ] Architecture decisions documented with rationale?
- [ ] Data models and flows complete?
- [ ] API surfaces fully specified?
- [ ] Quality gates and active test levels documented in Section 7?
- [ ] Graph basis noted (or "none — greenfield")?
- [ ] BUILD_SPEC saved as `<repo>/workflowArtifacts/BUILD_SPEC_<ProjectName>.md`?
- [ ] Both BUILD_SPEC and USER_STORIES.md paths returned to the dispatcher?
