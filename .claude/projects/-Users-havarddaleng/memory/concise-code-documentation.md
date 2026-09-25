---
name: concise-code-documentation
description: Keep code comments/docstrings terse — state what the code does; omit lengthy background/rationale discussion
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 23a2e618-7e49-4715-b054-b25ab7215008
  modified: 2026-07-27T08:34:06.461Z
---

Code documentation (docstrings and comments) must be concise and to the point. State what the code does and, briefly, any non-obvious constraint — but do NOT add multi-sentence discussion of the background, motivation, or reasoning behind decisions. The verbose, rationale-heavy docstrings/comments that agents (augmentor, bugger, doccer, enforcers) tend to write are too cluttered.

**Why:** The user finds long background/decision-rationale in documentation cluttering and hard to scan.

**How to apply:** When briefing any code-writing/documenting agent, instruct it to keep comments and docstrings terse (a line or two; what + essential caveat only, no background essays). Apply the same restraint to any documentation I write directly. Relates to [[consider-means-analyze-only]] (don't over-explain in general).
