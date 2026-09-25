---
name: no-unconfirmed-claims
description: "Never state a conclusion about how a system behaves (by design vs. failing, always vs. never) until the causal chain is traced end to end"
metadata:
  node_type: memory
  type: feedback
  originSessionId: f1a049ea-d7a3-4d21-9915-057abd7b07f5
  modified: 2026-09-23T09:49:07.277Z
---

Only state what has been confirmed. Label anything else as unknown or as an inference, explicitly.

**Why:** On 2026-09-23 I told the user "ardoqbundlesproduction syncs only via full loads, never live" and "no step that used to work has started failing" after seeing only that `cdc_publication` lacked the org's tables. Tracing further showed the seed is designed to add them (backup.py → post-copy → setup-org-schema-cdc-publication), so a step was failing. Earlier, the same session claimed "components have no ardoq-common:id" from a stale GraphLake head. The user called both out as incorrect information.

**How to apply:** Before answering "is X by design or broken?" or "does X always/never happen?", trace the mechanism that is supposed to produce X, including its callers. A single observation of current state (a missing row, an empty result) is not a conclusion about design. Related: [[verify-before-asserting-absence]], [[terse-answers-no-hedging]].
