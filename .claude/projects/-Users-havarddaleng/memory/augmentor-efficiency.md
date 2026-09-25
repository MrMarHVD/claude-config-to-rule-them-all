---
name: augmentor-efficiency
description: "Augmentor should work fast and lightly, not pedantically — verify once, defer deep checks to enforcer/qa"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 177baf30-3a25-4447-8539-1596957daedf
  modified: 2026-08-05T11:33:42.817Z
---

The augmentor agent (`~/.claude/agents/augmentor.md`) was too pedantic in practice: it ran project test suites repeatedly, chased down and fixed tangential pre-existing bugs it noticed along the way, and generally optimized for exhaustiveness over turnaround speed.

**Why:** the user wants efficiency from augmentor dispatches. Convention conformance and deeper correctness verification are already covered downstream by the enforcer and qa agents ([[orchestrate-agent-models]]) — augmentor re-doing that work is redundant and slow.

**How to apply:** the augmentor definition now says to run the relevant build/typecheck/test check **once**, not iterate or hunt for extra bugs, and to note (not fix or verify) anything tangential it notices, leaving it for enforcer/qa. When dispatching augmentor from the orchestrator, don't ask it to run tests repeatedly or do exhaustive verification in the prompt — keep its scope tight and let the enforcer stage that runs after it handle conformance checks.
