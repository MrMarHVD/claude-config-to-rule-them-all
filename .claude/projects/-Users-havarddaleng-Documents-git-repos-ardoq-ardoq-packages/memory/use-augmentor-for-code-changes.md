---
name: use-augmentor-for-code-changes
description: 'User wants the augmentor subagent used for making scoped code changes, not direct edits'
metadata:
  node_type: memory
  type: feedback
  originSessionId: f3c45aa6-8326-4f0c-aa45-aaf3d8c10849
  modified: 2026-07-23T10:23:49.276Z
---

When making well-scoped code changes (implementing a feature, adding a function, surgical edits), delegate to the `augmentor` subagent rather than editing files directly.

**Why:** The user explicitly asked for this after I implemented an API function via direct Edit calls.

**How to apply:** For a single, clearly-scoped addition/modification, spawn the `augmentor` agent with the full spec. Reserve direct edits for trivial one-liners or when the user says otherwise. Planning/exploration is still fine to do directly.
