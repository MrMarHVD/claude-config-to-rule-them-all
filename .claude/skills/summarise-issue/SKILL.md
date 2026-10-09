---
name: summarise-issue
description: >-
  Return a hyperconcise, structured summary of one or more issues or bugs: Location, Essence, Summary. Use when the user asks for a concise/clear summary of an issue, bug, problem or error ("summarise the issue", "what's the problem, concisely", "give me a short summary of these bugs"). NOT for investigating or fixing the issue, and NOT for long write-ups or reports.
---

# summarise-issue

Summarise each issue in exactly three fields. Nothing else: no preamble, headings beyond the issue title, file tours, tables, counts, status notes, open questions or closing remarks.

## Before writing

Only summarise what is established. If a fact is unconfirmed, verify it first, or mark it "(unconfirmed)" inside the field. Never pad a field to look complete.

Pick the mental model of the problem space first: the process or structure the issue lives in (e.g. "ontology → smell DL → LLM query generation → SPARQL → GraphLake results"). Every field is written against that model.

## Format

One block per issue:

```
**<short issue name>**
- **Location:** <the exact unit where the issue is>
- **Essence:** <the minimal artefact that causes it>
- **Summary:** <2–3 sentences max>
```

### Location
The exact point or unit that captures *where* the issue is: a named function, file:line, a named definition (e.g. a smell class), or a step in a larger process that can be logically separated. Name it concretely (`FlaggedOwnerOrgNotConsumer` in `coverage-gaps_application.dle`, `normalizeTimestampValue` in `util.go:334`, "the seed's post-copy step"). One line.

### Essence
The problem expressed in the domain's own formal terms, not prose. Give the smallest artefact that makes the problem visible:
- the causing logical statement (e.g. the DL snippet `ownedBy ∘ belongsTo`),
- the causing code line, or simplified pseudocode of the faulty logic,
- the variable and its wrong value (e.g. `time.precision.mode` unset → timestamps sent as µs integers),
- a minimal input → wrong output pair.

Put code/logic in a code block. Keep it to the few tokens that carry the fault; cut everything around it.

### Summary
At most 2–3 short sentences. State what goes wrong in terms of the mental model: which step is at fault and what it causes downstream. Include one concrete example (entity, value, row) if one exists. No hedging, no background, no fix proposals unless asked.

## Example

**OU owners**
- **Location:** smells `FlaggedOwnerOrgNotConsumer`, `FlaggedCriticalOwnerNoLocalExpert` (DL definition step)
- **Essence:**
  ```
  ownedBy ∘ belongsTo
  ```
- **Summary:** The smell definition reaches an owner's org via Belongs To, which fits Person owners only; for an OU owner it reaches the OU's parent instead of the OU. The generated SPARQL inherits this. Example: Marketing owns and consumes App X, yet App X is flagged.

## Final check

Before sending, delete any word, clause or line whose removal loses nothing. If a field could be shorter and still exact, shorten it.
