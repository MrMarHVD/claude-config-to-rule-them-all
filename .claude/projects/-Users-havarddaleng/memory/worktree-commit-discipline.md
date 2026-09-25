---
name: worktree-commit-discipline
description: "Only use git worktrees/commits when running the full orchestration pipeline; otherwise edit the current branch in place and don't commit"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 23a2e618-7e49-4715-b054-b25ab7215008
  modified: 2026-07-27T07:58:30.121Z
---

For ad-hoc work (single edits, bug fixes, follow-up tweaks — anything that is NOT a full run of the orchestrate pipeline), do the work directly on the currently checked-out branch, do NOT create git worktrees, and do NOT commit. Leave changes uncommitted for the user to review.

Worktrees, per-issue branches, and orchestrator-authored commits are reserved for when the user explicitly invokes the full pipeline (the `orchestrate` skill over a spec's issues).

**Why:** The user reviews changes before they're committed and finds worktree/commit ceremony unnecessary overhead for small changes; it also fragments history and leaves stray branches.

**How to apply:** Default to in-place edits on the active branch with no commit. Only reach for the worktree-per-issue + commit + merge model when running `orchestrate` end-to-end. Relates to [[ai-generated-review-marker]] (another standing convention for this repo's PDF/AI code).
