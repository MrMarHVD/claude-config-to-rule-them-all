---
name: tune
description: >-
  Extract the user's corrections and preferences from the current conversation and write them into the persistent behavioural instructions (~/.claude/CLAUDE.md and the project memory directory). Use when the user says "tune", "update your instructions", "remember how I want you to work", "apply my feedback to your behaviour", or after a conversation where they corrected style, process, or output format. NOT for saving factual/project knowledge (that is ordinary memory) and NOT for one-off instructions that apply only to the current task.
---

# tune

Turn feedback from this conversation into durable instructions.

## 1. Collect

Scan the whole conversation for user turns that correct or direct behaviour: rejected phrasing, rewritten output, "don't do X", "always do Y", visible frustration at a pattern, explicit approval of an approach. Ignore task content — only how the work should be done.

For each item record: the trigger, the wrong behaviour, the correct behaviour, and the evidence (quote or paraphrase the user turn).

Discard anything with no evidence in the transcript. Do not infer preferences from silence or from a single ambiguous turn.

## 2. Classify

- **Cross-project, applies always** → `~/.claude/CLAUDE.md`. Keep it to short imperative bullets under a topical heading.
- **Project- or workflow-specific** → a memory file in the project memory directory (`~/.claude/projects/<slug>/memory/`), with `type: feedback`, a `**Why:**` line, and a `**How to apply:**` line. Add one pointer line to that directory's `MEMORY.md`.

## 3. Reconcile

Read the existing instructions first. Then, per item:

- Already covered, same meaning → skip.
- Covered but weaker or narrower → edit the existing entry in place; do not add a second one.
- Contradicts an existing entry → the newer feedback wins. Replace the old text and say which entry changed.
- New → add it.

Never append a near-duplicate. The instruction set must stay short enough to read in full.

## 4. Write

Rules for the text itself:

- Imperative, second person, one behaviour per bullet.
- State the behaviour, not the rationale, in `CLAUDE.md`. Rationale belongs in memory files under `**Why:**`.
- No examples unless the rule is unclear without one.
- Prefer editing an existing bullet over adding a new heading.

## 5. Report

List, in one line each: what was added, what was edited, what was skipped as already covered. Show the final diff of any file touched.
