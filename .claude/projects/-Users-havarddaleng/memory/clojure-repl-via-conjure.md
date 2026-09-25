---
name: clojure-repl-via-conjure
description: "User's Clojure REPL editor is Conjure (Neovim), not Calva/VS Code"
metadata: 
  node_type: memory
  type: user
  originSessionId: d40fee00-b073-40f7-869b-9155958ea436
  modified: 2026-07-28T12:31:36.474Z
---

The user drives their Clojure nREPL from Conjure in Neovim. Do not suggest Calva,
VS Code Jack-in, or VS Code command-palette actions for REPL work; give plain
forms to evaluate, or `clj-nrepl-eval`-style CLI invocations.

Note that `ardoq-api/CLAUDE.md` states the project "uses an nREPL-based workflow;
start the REPL via Calva in VS Code (Jack-in)" — that instruction reflects the
team default, not this user's setup.
