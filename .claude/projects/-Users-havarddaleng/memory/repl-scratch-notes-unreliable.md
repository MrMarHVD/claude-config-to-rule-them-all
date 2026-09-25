---
name: repl-scratch-notes-unreliable
description: Never trust capability claims in ardoq-api/repl/** scratch namespaces — verify against graphlake origin/main Go source instead
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a5b2806a-212a-49c8-b45c-5304573f2f17
  modified: 2026-07-30T08:45:19.284Z
---

Comments in `ardoq-api/repl/<person>/*.clj` scratch namespaces are personal notes that go stale. Do not cite them as evidence for what GraphLake can or cannot do. Verify against the Go source on `graphlake` `origin/main`.

Concrete case: `repl/hamza/workspace_model.clj:9-28` claims references need "an RDF-star endpoint" and that type IRIs "never join". The first is false — RDF 1.2 reification is fully implemented (`query/sparql/parser.go:232`, executor `reifyTripleTerm` in `query/sparql/query.go:1100`, `core/reification.go`). Repeating it produced a wrong design constraint across several turns.

**Why:** a stale note read as authoritative sends design down a path that avoids capability that already exists.

**How to apply:** treat `repl/**` as hypotheses, not facts. See [[verify-before-asserting-absence]] for the checkout-staleness half of the same problem.
