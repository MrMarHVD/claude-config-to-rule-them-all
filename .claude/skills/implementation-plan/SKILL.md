---
name: implementation-plan
description: >-
  Produce a terse, fully-informed implementation plan for a specific piece of functionality — one that names exact files/layers/endpoints to touch, backed by having actually checked the code (including dependent/backend repos), with zero narrative clutter. Use when the user asks "lay out how we'd implement X", "what's the wiring needed for X", "give me an implementation plan", or similar — especially after a discussion has already established scope and the user wants the result distilled. NOT for exploratory "how does this work" mental-model requests (use mindmap for those), and NOT a substitute for actually writing the code.
---

# implementation-plan

Produce an implementation plan that is **fully informed and maximally concise at the same time** — never trade one for the other. Success = every line is a load-bearing fact the implementer needs; nothing is guessed, and nothing is filler.

## Non-negotiable: verify before proposing

Never write a plan from convention-guessing alone. If the functionality crosses into another repo, service, or team's code (a backend, a shared package, an external API), go read that code first — actual routes, request/response shapes, validation rules, accepted formats, limits. Convention-matching within the current repo ("this is probably shaped like the sibling feature") is a reasonable starting hypothesis but is not sufficient on its own when a real contract exists to check. If you can't reach the dependency (no access to the repo, no docs), say so explicitly in the plan as an open question — do not silently fall back to invented shapes and present them as fact.

Use the `Explore` or `general-purpose` agent to do this legwork so it doesn't bloat your context — but read enough to state contracts precisely (exact endpoint, method, param location, body shape, field names, accepted formats, limits), not just "there's probably an endpoint for that."

If verification surfaces a real discrepancy (frontend and backend disagree on a field name, a format, a shape), state it plainly as a correction to make, in one line, before the plan — don't bury it, don't soften it with hedging language, don't editorialize about why it happened.

## Ruthlessly delimit scope

Before writing anything, pin down exactly what functionality is being planned — and exclude everything else, including adjacent or downstream steps that feel related. A plan for "upload and preprocess documents" must not drift into drafting, extraction, review, or any other step that has its own distinct trigger/contract, even if it's the "next thing that happens." If you catch yourself explaining a downstream system to justify why it's mentioned, that system doesn't belong in the plan — cut it, don't caveat it.

If existing naming in the codebase conflates a general concept with one specific mechanism (e.g. a resource named after "preprocessing" when the user-facing concept is just "documents"), use the accurate/general name in the plan and note the rename once, briefly — don't propagate a misleading name forward.

## Format

State scope in one line: input → output → what layer does the processing (and confirm processing already exists elsewhere if it does — a plan for wiring is not a plan to reimplement logic that's already there).

Then a flat numbered list, one item per layer/file/module actually touched. Each item:

- Names the concrete file, path, or module (existing or new).
- States what goes in it in as few words as possible — a type, a function signature's shape, an endpoint + method, an action name.
- No prose sentences justifying the item, no "this follows the pattern of X because Y" — if the convention needs a pointer, name the analogous file in parentheses, nothing more.

End the list. Do not add a summary, a "next steps," a "let me know if," or any closing narration. If open questions or unverifiable assumptions remain, list them as a final short bullet block labeled plainly, not woven into prose.

## Cut on sight

- Alternatives you didn't pick and why.
- Caveats that don't change what to build.
- Restating what the user already told you.
- Any sentence whose removal loses no information the implementer needs.
- Hedging ("might", "could potentially", "it's worth considering") — state what is true and what is still unknown, nothing in between.
