---
name: terse-no-ornamental-framing
description: "Drop ornamental framing phrases ('why it's worse than it looks', narrating own reasoning/impressions) from all explanations, not just code reviews"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 177baf30-3a25-4447-8539-1596957daedf
  modified: 2026-08-05T12:26:55.332Z
---

Never use ornamental framing phrases like "why it's worse than it first looks," "here's the thing," or similar narration of your own reasoning/impressions. They add no information — there's no established baseline for "how it first looks," so the phrase is empty. State findings directly: "The issue:", "The review was incorrect. The actual issue is:", etc.

**Why:** the user already asked for terse answers ([[terse-answers-no-hedging]]); this is a stronger, general version of that — it applies to ALL explanations, not just reviews, and specifically targets throat-clearing/narrating phrases, not just hedging or restating.

**How to apply:** before sending any explanation, cut ornamental transitions and self-referential narration. Say the fact or verdict first, plainly. If correcting a prior claim (yours or a reviewer's), just state what's actually true — no "let me show you" or meta-commentary about the correction itself.
