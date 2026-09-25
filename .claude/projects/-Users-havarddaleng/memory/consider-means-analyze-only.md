---
name: consider-means-analyze-only
description: "Never change code unless explicitly told; the word \"consider\" specifically means analyze-only, make no changes"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 23a2e618-7e49-4715-b054-b25ab7215008
  modified: 2026-07-27T08:04:51.949Z
---

Do not make any code changes unless the user explicitly instructs a fix/change. When the user says "**consider** X" (e.g. "consider this issue and whether it should be resolved"), that means: analyze and report your assessment ONLY — make NO edits. Default posture for ambiguous asks is propose/diagnose, not act.

**Why:** The user wants to decide what gets changed and review reasoning first; unrequested edits are unwanted.

**How to apply:** On "consider" / analysis-style requests, investigate and give a verdict + recommendation, then stop. Wait for an explicit "fix it" / "do it" before editing. Exception the user granted: do not revert changes already made in a prior explicit instruction. Relates to [[bug-fixes-via-bugger]] and [[worktree-commit-discipline]].
