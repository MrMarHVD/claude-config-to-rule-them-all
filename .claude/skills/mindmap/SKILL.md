---
name: mindmap
description: >-
  Build a high-level, holistic conceptual map of how something in a codebase works — a whole codebase, a feature, a subsystem, a flow, or any specific thing the user points at. Use this when the user wants to *understand how something works* at an intuitive, big-picture level rather than a line-by-line walkthrough or a code edit. Triggers on requests like "explain how X works", "give me a mental model of", "how does this fit together", "map out", "I want to understand the architecture/flow of", "walk me through conceptually", "make sense of this codebase". NOT for making code changes, doing detailed code review, or answering a narrow factual lookup where a one-line answer suffices.
---

# mindmap

Your job is to give the user a **clear, intuitive mental map** of how a specific thing in a codebase works. Success = when you finish, the user *gets it* — they hold an accurate big-picture model and nothing central to their question is left blatantly unclear. Failure = you produced a correct-but-detailed dump they still have to decode, or you left an obvious gap that forces a "wait, but how does..." follow-up.

## The one rule that governs everything: stay holistic

Explain at the level of **concepts, roles, and relationships**, not syntax. The map is about *what the pieces are, what each is responsible for, and how they connect and flow* — not the exact code.

- **Pull in only load-bearing details.** Reference specific files, functions, types, services, or endpoints **only when naming them helps the user's map** (as anchors so they know where a concept lives). Drop everything that doesn't move understanding forward: signatures, parameter lists, error handling, edge cases, boilerplate, framework ceremony, syntactic detail.
- **Abstract aggressively but stay accurate.** Collapse many small functions into "the part that does X." Simplify, but never say something false — if you simplify away a caveat that actually matters to the question, keep a one-line note.
- **Anchor concepts to code, not the reverse.** "Requests come in through the API layer (`routes/`), get validated, then handed to the domain services" — the concept leads, the file is a pointer.
- **Ruthlessly cut anything off-question.** A mindmap of "how auth works" should not tour the logging system. Depth follows the user's question; everything else gets at most a passing mention.
- **Assume no prior knowledge — explain from first principles.** Do not lean on the user already understanding any library, framework, pattern, protocol, or piece of jargon that the code happens to use. When a concept like that is load-bearing for the map, explain *what role it plays* in a sentence, in plain terms, before building on it — e.g. "a library that holds the app's shared state in one central place so any part of the UI can read or update it." Name the technology (so the user can match it to what they'll see in the code), but never treat the name alone as an explanation. If it is not load-bearing, drop it entirely rather than name-dropping it. The map must stand on its own for someone who has never met the specific tools involved; when unsure whether the user knows something, explain it at the higher level rather than assuming.

## Workflow

1. **Pin down the question and its altitude.** What specifically does the user want mapped, and how zoomed-out? "How does the whole system fit together" is a different altitude than "how does a request flow through the checkout feature." If the target or scope is genuinely unclear, ask one sharp clarifying question before investing in exploration — otherwise proceed.

2. **Explore to understand, not to transcribe.** Investigate the relevant code enough to build an *accurate* model: entry points, the main moving parts, how data/control flows between them, external dependencies, and the boundaries. For anything beyond a small scope, delegate the legwork to the `Explore` agent (or `general-purpose`) so you gather the shape of things without drowning in files — you want the structure, not every line. Verify your understanding against the actual code; never map from assumption.

3. **Find the spine.** Identify the few core concepts and the primary flow that carry most of the understanding. This backbone is the map; everything else hangs off it or gets cut.

4. **Choose the form that best conveys it** (see below). Pick whatever makes the model clearest for *this* question and honor any format the user asked for.

5. **Deliver the map**, then a short "if you want to go deeper, look at ..." pointer so the user knows where to dig — without cluttering the map itself.

## Choosing the explanatory form

Match the medium to the shape of the thing. Combine forms when it helps; default to whatever is clearest and simplest.

- **Logical flow between functions/steps** — for processes and request/data flows. Show the journey as a sequence of named stages with a concise "what happens and why" per stage. Great for "how does a request/job/event get handled."
- **Infrastructure / component diagram** — for how services, modules, or systems relate. A simple ASCII or Mermaid diagram of the boxes and arrows, each box annotated with its *role* (one line), each arrow with what flows across it. Great for "how does it all fit together."
- **Layered / responsibility view** — for architecture: what each layer or module is responsible for and what it may/may not talk to.
- **Plain prose** — when the idea is simple or conceptual and a diagram would be overkill. A few tight paragraphs building the model from the top down.
- **Anything else** the user requests or that fits better (analogy, a small table of "component → responsibility", a state diagram).

Prefer diagrams and structured layouts when relationships are the point; prefer prose when a narrative or a single concept is the point. Keep every diagram small — a mindmap is legible at a glance.

## Style

- **Top-down.** Start with the one-sentence big picture, then expand into the handful of core parts, then the flow between them. The reader should understand the gist from the first few lines and gain resolution as they read.
- **Concise and confident.** Short sentences. Name each concept once, clearly. Use the codebase's own terminology so the map matches what the user sees in the code.
- **Intuition over completeness.** It is better to convey the right *shape* memorably than to be exhaustively precise. Completeness is the enemy here — omit by design.
- **Close the loop.** Before finishing, sanity-check: given the user's original question, is there any obvious "but how does X connect / where does Y come from" gap that would leave them puzzled? If so, add a line to close it. That final check is the whole point of the skill.
