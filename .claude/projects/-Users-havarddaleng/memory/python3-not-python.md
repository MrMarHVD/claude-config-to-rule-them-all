---
name: python3-not-python
description: "Always write Python commands with `python3`, never `python`"
metadata:
  node_type: memory
  type: feedback
  originSessionId: f1a049ea-d7a3-4d21-9915-057abd7b07f5
  modified: 2026-09-23T08:58:37.826Z
---

Use `python3` in every Python command given to the user or run (e.g. `python3 -m src.pipeline.run_flagged_queries`, `../.venv/bin/python3`), never `python`.

**Why:** User correction on 2026-09-23.
**How to apply:** Applies to all commands, instructions to dispatched agents, and docs.
