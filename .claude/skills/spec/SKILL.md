---
name: spec
version: 1.0.0
description: Create a clear, scoped specification for a feature or change from user requirements or a conversation with the user. Trigger when the user asks to "write a spec", "create a specification", "spec this out", "spec-out this feature", "turn this into a spec", or wants the requirements of a feature/change captured as a durable document. Produces a numbered markdown spec under .aispace/specs/ following the `spec-NNN-descriptive-title` convention.
argument-hint: [feature/change to spec, or leave blank to use the conversation]
allowed-tools: Read, Write, Bash, Glob, Grep, AskUserQuestion
---

# Spec

Turn a feature or change — described in the invocation or established over the conversation — into a single, clearly-scoped specification document, saved in the right place with the right label.

A spec captures **what** is being built and **why**, and the boundaries around it. It is not a design doc (no implementation strategy, no file lists — that's `implementation-plan`) and not a task tracker.

## Workflow

### 1. Gather and scope the requirements

Assemble the requirements from the invocation argument and the preceding conversation.

**Do not invent requirements.** If anything material is ambiguous, missing, or under-scoped — the core user problem, the boundaries of what's in vs out, a success condition, or a constraint — ask the user before writing. Use `AskUserQuestion` for a few targeted choices, or ask directly in prose. Prefer one focused round of questions over guessing. If the user has said "just capture what we discussed", proceed with what you have and record any gaps under **Open questions**.

Scope tightly. A good spec is small and self-contained. If the request is really several features, say so and propose splitting into multiple specs (one folder each) rather than one sprawling document.

### 2. Determine the label

Specs are numbered sequentially and globally unique. Find the next number:

```bash
ls -d .aispace/specs/spec-*/ 2>/dev/null | sed -E 's#.*/spec-0*([0-9]+)-.*#\1#' | sort -n | tail -1
```

The next spec number is that value + 1, zero-padded to three digits. Note `spec-000-format-example` is a reserved placeholder — real specs start at `spec-001`. If no numbered specs exist yet, use `001`.

Build the label as `spec-NNN-descriptive-title` where `descriptive-title` is a short, kebab-case, human-readable slug (3–6 words) that uniquely and transparently identifies the feature — e.g. `spec-001-saml-cert-expiry-notifications`. Avoid vague slugs like `spec-001-improvements`.

### 3. Create the folder structure

Each spec gets its own subfolder inside `.aispace/specs/`, matching the existing convention (see `.aispace/specs/spec-000-format-example/`):

```
.aispace/specs/
  spec-NNN-descriptive-title/
    spec-NNN-descriptive-title.md   ← the spec (same name as the folder)
    issues/                         ← subfolder for breaking the spec into discrete work items (create it empty)
```

```bash
mkdir -p .aispace/specs/spec-NNN-descriptive-title/issues
```

The markdown file's name must exactly match the folder name. Leave `issues/` empty unless the user has asked you to break the spec down into issues in the same pass.

### 4. Write the spec

Use the template below. Fill every section; if a section genuinely doesn't apply, keep the heading and write `_None._` rather than deleting it — a reader should be able to tell it was considered. Write requirements so they are **testable**: each should be something you could later confirm as done or not done. Give functional requirements stable IDs (`FR-1`, `FR-2`, …) so issues and PRs can reference them.

Keep it terse and concrete. This is a requirements document, not prose — favor lists over paragraphs.

### 5. Confirm

After writing, report the created path and give the user a 2–3 line summary of the scope, and explicitly surface any **Open questions** that still need their input.

## Spec template

```markdown
# spec-NNN: <Human Readable Title>

| | |
|---|---|
| **Label** | spec-NNN-descriptive-title |
| **Status** | Draft |
| **Created** | <YYYY-MM-DD> |
| **Author** | <user / requester> |
| **Affected area(s)** | <repos / domains / services, if known> |

## Summary

One or two sentences: what this feature/change is, in plain language.

## Background & motivation

Why this is needed — the problem, the current behavior, the trigger. 1–4 sentences.

## Goals

- What this spec sets out to achieve (the outcomes, not the implementation).

## Non-goals / Out of scope

- Explicitly what this spec does **not** cover. This is where scope is defended.

## Requirements

### Functional

- **FR-1** — <a single, testable requirement>
- **FR-2** — …

### Non-functional / Constraints

- Performance, security, compatibility, i18n, data-migration, or other constraints the solution must respect. Write `_None._` if there are none.

## Acceptance criteria

- Observable conditions that must all hold for this to be considered done. Phrase so each is verifiable (ideally maps back to an FR).

## Dependencies & assumptions

- Anything this depends on, or is assumed true. `_None._` if not applicable.

## Open questions

- Unresolved decisions needing the user or a stakeholder. `_None._` if fully specified.
```

## Rules

- **Never fabricate requirements or acceptance criteria** to fill the template — an honest `Open questions` entry is better than an invented requirement.
- The folder name and the markdown filename must be identical.
- Numbers are never reused. Always re-check the highest existing number at creation time (step 2) rather than assuming.
- One feature per spec. Propose splitting when the request is broader than one cohesive change.
- Keep implementation out of the spec — how it gets built belongs in an implementation plan or the `issues/` breakdown, not here.
