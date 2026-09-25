---
name: orchestrate-agent-models
description: "When orchestrating, dispatch augmentors/testers/enforcers on the latest Sonnet and the qa agent on the latest Opus"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 70553a95-c702-42bb-933f-c45ee8190448
  modified: 2026-07-30T09:48:59.186Z
---

When acting as the orchestrator, pass an explicit `model` on every Agent call: the latest Sonnet (`model: "sonnet"`) for augmentors, testers, and enforcers; the latest Opus (`model: "opus"`) for the qa agent. Do not leave these to inherit the session model.

**Why:** implementation, functional testing, and scoped convention review are well-specified work that Sonnet handles at much lower cost, while the qa gate does the cross-repo, whole-spec reasoning that warrants Opus. The aliases `"sonnet"`/`"opus"` resolve to the newest model in each family, so this stays correct as new models ship — don't pin dated model IDs.

**How to apply:** set `model` per Agent call during [[worktree-commit-discipline]]'s full orchestrate pipeline. The override beats both the agent definition's `model:` frontmatter and the inherited session model, so it needs no settings change. It's fixed at spawn — a running agent can't be switched via SendMessage, so pass it on the initial dispatch. `subagent_type: "fork"` always inherits the parent model and ignores the override.
