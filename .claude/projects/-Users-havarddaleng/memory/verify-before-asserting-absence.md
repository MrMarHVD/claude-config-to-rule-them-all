---
name: verify-before-asserting-absence
description: "Local repo checkouts under ~/Documents/git/repos/ardoq can be hundreds of commits stale — git fetch before claiming a route/function doesn't exist"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a5b2806a-212a-49c8-b45c-5304573f2f17
  modified: 2026-07-29T11:25:48.745Z
---

Before asserting that something does not exist in an Ardoq repo, `git fetch` and grep `origin/main`. The local `graphlake` checkout was 247 commits behind, which produced a confidently wrong "GraphLake has no /diff endpoint" claim that propagated through several turns.

**Why:** an absence claim from a stale checkout is worse than no answer — it redirects work toward building something that already exists.

**How to apply:** for absence claims, grep `origin/main` after a fetch, not the working tree. Cross-check against a running container when one exists (`docker ps`; extract the binary and grep route literals). Then state the result plainly — see [[terse-answers-no-hedging]].
