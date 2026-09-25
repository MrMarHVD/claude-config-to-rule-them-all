---
name: no-coined-compact-terms
description: "Never invent compact/idiosyncratic terms (e.g. 'prose-with-code-fences') for something describable in a plain sentence — write the plain sentence"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: eae651e0-429a-48ed-b172-da77d3b9993d
  modified: 2026-09-12T09:36:48.960Z
---

Never coin a fancy or idiosyncratic term to compress something that can be laid out cleanly in a plain sentence. Example of the mistake: describing an LLM reply that mixes explanation with triple-backtick code blocks as "prose-with-code-fences". The correct version names the actual things: "Claude replies as one block of text that mixes explanation with code delimited by triple-backtick lines."

**Why:** a coined term is only legible to whoever coined it. The user had to ask what it meant, so the compression cost a round trip instead of saving words. Brevity means fewer words, not denser jargon.

**How to apply:** when about to write a hyphenated or invented noun phrase for a concept, write the plain description instead, naming the concrete items (file, syntax, field, value). Reserve technical terms for ones that already exist in the domain or the codebase. Related: [[terse-no-ornamental-framing]], [[terse-answers-no-hedging]].
