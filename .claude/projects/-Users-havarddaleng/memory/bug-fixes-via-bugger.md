---
name: bug-fixes-via-bugger
description: "When explicitly asked to fix a bug, delegate to the bugger agent, working on the current branch (not a worktree)"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 23a2e618-7e49-4715-b054-b25ab7215008
  modified: 2026-07-27T08:04:57.046Z
---

When the user explicitly asks to fix something that is a **bug**, delegate the fix to the **bugger** agent rather than editing directly. For ad-hoc fixes (not a full orchestrate-pipeline run), the bugger works **on the currently checked-out branch, not in a git worktree**, and does not commit.

**Why:** The user prefers bug fixes routed through the specialist bugger agent, and (per [[worktree-commit-discipline]]) reserves worktrees/commits for the full pipeline only.

**How to apply:** On an explicit bug-fix instruction, dispatch `bugger` scoped to the current branch/working tree, no worktree, no commit. Only fix after an explicit instruction — see [[consider-means-analyze-only]]. (Non-bug edits the user directly requests can still be done in place per the same no-worktree/no-commit rule.)
