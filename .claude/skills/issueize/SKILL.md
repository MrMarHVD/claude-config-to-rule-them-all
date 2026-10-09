---
name: issueize
version: 1.0.0
description: Transform an existing spec into a set of cross-sectional, independently-testable issues arranged as a dependency DAG that is maximally parallelised. Trigger when the user asks to "issueize", "break this spec into issues", "turn spec-NNN into issues", "decompose the spec", "create the issue graph", or wants a spec split into parallelizable work items for augmentors. Each issue is a complete vertical slice across all affected code layers; issues are tiered (A, B, C…) so every issue in a tier can be built in parallel with no overlap. Writes one markdown file per issue into the spec's issues/ folder plus an index with the dependency graph.
argument-hint: [spec number or label, e.g. 001 or spec-001-... ; blank = ask / most recent]
allowed-tools: Read, Write, Bash, Glob, Grep, AskUserQuestion
---

# Issueize

Take one existing spec and decompose it into a set of **issues** that form a directed acyclic dependency graph, tiered so that maximum work happens in parallel. Each issue is a self-contained brief an **augmentor** can execute end-to-end without further context.

This is decomposition and scoping only — you do **not** implement the issues here.

This skill slices **vertically**: one issue = one complete feature element across every layer, owned end-to-end by one agent. If the work divides cleanly along layer boundaries and the user wants backend and frontend built concurrently against a pre-frozen interface, use the `issueize-horizontal` skill instead (it writes `components/` rather than `issues/`).

## Core concepts (get these exactly right)

**1. An issue is a cross-sectional vertical slice.**
Each issue delivers one complete, independent feature element that spans *every* code layer it needs — API/handler, service, repo, DB migration, frontend, tests, config — across *every* repo it touches. After the issue is done it must be **testable in isolation** and require **no further wiring**: it stands on its own. An issue is never "the backend part" of something whose frontend is a separate issue — that would leave dangling wiring. Slice by feature element, not by code layer.

> Note the word "layer" is overloaded. **Code layers** = api/service/repo/frontend/DB (an issue spans all it needs). **Tier** = the A/B/C dependency level in the graph. Never conflate them.

**2. Issues form a dependency DAG, tiered for parallelism.**
- An issue with no dependencies is a **root**, tier **A**.
- Otherwise its tier is one letter after the highest tier among its dependencies: `tier_index = 1 + max(tier_index of each dependency)` (A=1, B=2, C=3…).
- This guarantees two things simultaneously: every dependency of a tier-N issue sits in a strictly lower tier (already complete), and **no two issues in the same tier depend on each other**. Therefore an entire tier can be executed in parallel, then the next tier, and so on.
- Example: `A1, A2, A3` start immediately. `B1` depends on `A1`; `B2` depends on `A1` and `A2`. Once all A issues are done, every B issue can run at once. `C1` depends on at least one B (and possibly some A).

**3. Same-tier issues must have zero problematic overlap.**
Since a whole tier runs concurrently (potentially by separate augmentors in separate worktrees), two issues in the same tier must not edit the same file/function/endpoint/migration in conflicting ways, and must not both need to define the same shared thing. If they would, you have three fixes — pick one:
   - **Merge** them into one issue (they weren't truly independent), or
   - **Introduce a dependency** so one precedes the other (moving one to a later tier), or — best when several issues need the same foundation —
   - **Extract the shared surface** (schema, shared namespace, base component, migration) into its own earlier-tier "foundation" issue that the others depend on.

**4. Maximal parallelism ≠ maximal splitting.**
Introduce a dependency *only* when the dependent genuinely cannot be built or tested without the other's output. Do not invent false dependencies that serialize work needlessly. But also don't split so finely that an "issue" is no longer an independently-testable slice. Aim for the widest tiers the true dependencies and non-overlap constraint allow.

## Workflow

### 1. Locate and read the spec

Resolve the target spec from the argument (a number like `001` or a full label). If none given, list `.aispace/specs/spec-*/` and either use the obvious most-recent one or ask the user which. Read the full spec markdown — its Goals, Non-goals, functional requirements (`FR-N`), and acceptance criteria are your raw material. Every issue must trace back to one or more FRs, and every in-scope FR must be covered by some issue.

If the spec is too vague to decompose cleanly (unclear boundaries, missing acceptance criteria), stop and ask the user rather than guessing — a bad decomposition is worse than none.

### 2. Identify affected code layers and surfaces

Before slicing, understand what the change actually touches. Grep/Glob the relevant repos (ardoq-api, ardoq-packages/frontend, etc.) to ground the decomposition in real files, namespaces, endpoints, tables, and components. Concrete touch-points are what let you reason about overlap in step 4.

### 3. Slice into vertical issues

Break the spec into the smallest set of complete, independently-testable vertical slices that together satisfy all in-scope FRs. For each candidate issue, sanity-check: *Can this be built and tested on its own? Does it leave any dangling wiring?* If yes to dangling wiring, it's not a valid slice — widen it or fold the wiring in.

### 4. Build the dependency graph and assign tiers

- For each issue, list only its **true** dependencies (what must exist and be complete before it can be built/tested).
- Assign tiers by the leveling rule in Core Concept 2.
- Run the **overlap check** across every set of same-tier issues (Core Concept 3) and restructure until each tier is conflict-free.
- Verify the graph is **acyclic** (a cycle means your slices are entangled — re-slice).
- Number issues within each tier: `A1, A2, …`, `B1, B2, …`.

### 5. Write the issues and the index

Create one markdown file per issue in the spec's `issues/` folder, plus an `index.md`. See conventions and templates below.

### 6. Validate and report

Run the validation checklist. Then report: the tier structure (how many issues per tier), the critical path, and any decomposition decisions the user should sanity-check (especially merges/foundation-extractions you introduced).

## File & ID conventions

Issues live in the spec's own folder:

```
.aispace/specs/spec-NNN-descriptive-title/
  spec-NNN-descriptive-title.md
  issues/
    index.md                       ← dependency graph + tier overview
    NNN-A1-brief-description.md
    NNN-A2-brief-description.md
    NNN-B1-brief-description.md
    ...
```

**Issue ID format: `NNN-Xk-brief-description`**
- `NNN` — the spec number, three digits, matching the spec (e.g. `001`).
- `X` — the tier letter (`A`, `B`, `C`, …). All issues sharing a letter are parallelizable.
- `k` — the issue's number within that tier (`1`, `2`, …), unique per tier.
- `brief-description` — short kebab-case slug identifying the slice.

The markdown filename equals the issue ID. Example: `001-B2-workspace-picker-endpoint.md`.

## Issue file template

Each issue file is a **standalone brief** — an augmentor should be able to execute it with only this file plus the repos in front of it. Fill every section; use `_None._` where truly empty.

```markdown
# NNN-Xk: <Issue Title>

| | |
|---|---|
| **Issue ID** | NNN-Xk-brief-description |
| **Spec** | spec-NNN-descriptive-title |
| **Tier** | X  (parallel with all other tier-X issues) |
| **Depends on** | <comma-separated issue IDs, or `None (root)`> |
| **Enables** | <issue IDs that depend on this, if any> |
| **Satisfies** | FR-<n>, FR-<m> |
| **Repos / code layers touched** | <e.g. ardoq-api (service, repo, migration, api), ardoq-front (component, api-client)> |

## Objective

One or two sentences: the complete vertical slice this issue delivers.

## Scope — in

The concrete deliverable, as a checklist an augmentor can work through. Cover every code layer this slice needs so nothing is left unwired.
- [ ] <e.g. Add `foo` column via org migration>
- [ ] <e.g. `foo-service/create!` + spec>
- [ ] <e.g. `POST /api/foo` handler with `:allowed?`>
- [ ] <e.g. frontend `FooForm` + API client call>
- [ ] <tests proving the slice works end-to-end>

## Scope — out (do NOT touch)

Explicit exclusions that keep this issue from overlapping with its siblings. Name the files/areas that belong to *other* issues so parallel work doesn't collide.
- <e.g. Do not modify `bar_service.clj` — owned by NNN-B3>

## Interface / contract

What this issue must expose for its dependents, and/or what it consumes from its dependencies. This is the boundary that lets independent slices compose.
- **Produces:** <e.g. `GET /api/foo` returning `{:id :name}`; `foo-service/query!` signature>
- **Consumes:** <e.g. the `foo` table created by NNN-A1>

## Context & pointers

Grounding an augmentor needs but shouldn't have to rediscover: relevant existing files, analogous implementations to mirror, conventions, gotchas.
- Analogue to follow: <path to a similar existing feature>
- <constraints, prior art, links to spec sections>

## Acceptance criteria (testable in isolation)

Observable conditions that confirm this slice is done and works standalone — no dependent issue required to verify it.
- [ ] <verifiable outcome, ideally traceable to an FR>

## Definition of done

- [ ] All in-scope items implemented across every listed layer
- [ ] Tests written and passing
- [ ] No dangling wiring — the slice functions on its own
- [ ] Conforms to repo conventions (for ardoq-api, run the `ardoq-backend-enforcer` review)
```

## Index template (`issues/index.md`)

```markdown
# spec-NNN — Issue graph

Generated from spec-NNN-descriptive-title. Tiers execute in order; all issues within a tier run in parallel.

## Dependency graph

​```mermaid
graph TD
  A1[A1 short-name]
  A2[A2 short-name]
  B1[B1 short-name]
  A1 --> B1
  A2 --> B1
​```

## Tiers

### Tier A — no dependencies (start here, all in parallel)
| ID | Title | Satisfies | Repos touched |
|----|-------|-----------|---------------|
| NNN-A1-… | … | FR-1 | … |

### Tier B — depends on A
| ID | Title | Depends on | Satisfies |
|----|-------|-----------|-----------|
| NNN-B1-… | … | A1, A2 | FR-3 |

## Coverage
Every in-scope FR maps to at least one issue: FR-1 → A1, FR-2 → A2, …

## Critical path
A1 → B1 → C2  (longest dependency chain — the minimum number of sequential waves)
```

## Validation checklist (run before reporting)

- **Acyclic**: no cycles in the dependency graph.
- **Tier correctness**: for every issue, `tier = 1 + max(dependency tiers)`; roots are tier A.
- **No intra-tier dependencies**: no issue depends on another in the same tier.
- **No intra-tier overlap**: no two same-tier issues touch the same file/endpoint/migration/shared definition in conflicting ways.
- **Vertical completeness**: each issue is testable in isolation with no dangling wiring.
- **FR coverage**: every in-scope FR is satisfied by ≥1 issue; every issue traces to ≥1 FR.
- **Contracts closed**: every dependency's needs are met by something a lower-tier issue "Produces".

## Rules

- Slice by **feature element (vertical)**, never by code layer. No "backend-only" / "frontend-only" issues.
- A dependency exists **only** when the dependent genuinely cannot be built or tested first. No false dependencies.
- When several issues need the same shared surface, extract it into an earlier-tier foundation issue rather than duplicating or overlapping.
- Do not reuse issue IDs. Number within each tier from 1.
- Do not implement anything — this skill produces the issue set only.
- If the spec can't be decomposed cleanly, say so and ask, rather than forcing a fragile graph.
```
