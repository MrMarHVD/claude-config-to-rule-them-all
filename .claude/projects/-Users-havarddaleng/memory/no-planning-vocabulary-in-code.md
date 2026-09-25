---
name: no-planning-vocabulary-in-code
description: "Never reference spec/issue identifiers (FR-3, spec-004, 004-A1, _workflow paths) in code, comments, docstrings or test names — they're unresolvable to other readers"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 61f76fcb-9517-46f8-a8a8-daadd248102a
  modified: 2026-07-31T12:57:11.100Z
---

Nothing shipped into a repo may reference the private planning apparatus in `_workflow/`: no `FR-3`, no `spec-004`, no `004-A1`, no `_workflow/...` paths, no "as required by the spec". Applies to code comments, docstrings, test and `testing` description strings, and committed markdown.

**Why:** those identifiers only resolve inside the user's private `_workflow/` folder. A colleague reading the repo has no way to look them up, so the annotation is noise that also looks authoritative. Real shared tickets (Jira keys like `INT26-16`, `ARD-1234`) are the opposite — keep those.

**How to apply:** describe what the code does, not which requirement asked for it — `(testing "51 rows back means 50 returned and a further page announced")`, not `"... announced (FR-7, FR-8)"`. Traceability runs one way: the issue file cites the code, never the reverse. When dispatching agents that write code or docs, pass this rule explicitly — it is encoded in the `orchestrate` skill's Rules section, but applies to any agent in any context. Related: [[concise-code-documentation]], [[ai-generated-review-marker]].
