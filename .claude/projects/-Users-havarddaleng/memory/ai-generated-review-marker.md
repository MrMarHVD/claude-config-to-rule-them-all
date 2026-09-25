---
name: ai-generated-review-marker
description: Mark AI-written production code in ardoq repos with an AI-GENERATED REVIEW comment
metadata:
  node_type: memory
  type: feedback
  originSessionId: c43fd9f4-8fa0-41d7-981c-32783178a0d5
  modified: 2026-08-05T14:34:06.797Z
---

**Only add the `AI-GENERATED: REVIEW` marker when running the full orchestrate pipeline (with worktrees and commits).** For ordinary in-place edits on the current branch (the default per [[worktree-commit-discipline]]), do NOT add the marker — and instruct any dispatched agents (augmentor, bugger, etc.) not to add it either.

When the pipeline IS running: in an ardoq repo (ardoq-api, ardoq-packages, devops-monorepo, ardoq-docker), for code added/edited **outside a clear testing/dev environment** (NOT under `dev/`, `test/`, `tests/`, or similar throwaway scopes), add `AI-GENERATED: REVIEW` at the top of each function created or edited. Host-language comment syntax (Clojure `;; …`, TS/JS `// …`, Python `# …`). Per-function, only functions actually touched; skip dev/test scopes.

**Strip them at the very end of orchestration.** After the QA gate and before the final report, the orchestrator itself (never a subagent) removes every marker in place on each repo's checked-out base branch, across all repos in scope — and leaves the removal **uncommitted**.

**Then stop mentioning it.** The user commits the strip themselves, promptly, and does not want to be warned again that subsequent edits will "mix into" that diff. Say once, in the final report, that the strip is uncommitted; after that, never re-raise it. Before making any claim about the working tree, read `git status` — do not assert an uncommitted diff still exists from memory of having created it.

**Why:** The user wants AI-authored production code flagged for human review — but only for pipeline-produced work that lands via commits, not for interactive in-place edits where the human is already in the loop reviewing directly. The end-of-run strip is what makes the flag useful: the leftover working-tree diff shows a deleted marker line directly above every function an agent wrote, turning `git diff` into a precise index of agent-authored code. Repeating the warning after they have committed is noise, and they have called it out as such.

**How to apply:** Delete only whole lines that are solely the marker comment; verify `git diff` contains nothing but marker deletions and that a grep for `AI-GENERATED` returns zero hits; leave a clean index and a dirty working tree, and say so once in the report. Encoded in the `orchestrate` skill. Related: [[worktree-commit-discipline]], [[no-planning-vocabulary-in-code]], [[terse-answers-no-hedging]].
