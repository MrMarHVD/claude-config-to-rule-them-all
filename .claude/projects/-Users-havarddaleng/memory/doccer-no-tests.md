---
name: doccer-no-tests
description: "The doccer agent must not run any tests or builds — documentation changes don't alter behavior, so verification is wasted"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 23a2e618-7e49-4715-b054-b25ab7215008
  modified: 2026-07-27T08:37:31.872Z
---

When dispatching the doccer agent (documentation-only work: docstrings/comments, no code/logic change), explicitly instruct it NOT to run any tests, builds, or linters. Documentation edits do not change behavior, so test runs are unnecessary overhead.

**Why:** The user flagged doccer running the test suite after a comments-only edit as totally unnecessary.

**How to apply:** In every doccer brief, state "do not run tests/builds." Rely on the constraint that doccer never edits executable code as the guarantee that behavior is unchanged. Relates to [[concise-code-documentation.md]].
