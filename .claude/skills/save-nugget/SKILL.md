---
name: save-nugget
version: 1.0.0
description: Record a durable, cross-cutting finding about a repo — an unusual convention, a documented "do X this way" rule, a structural principle, a test/build peculiarity, a known exception — into the agent workspace's nuggets/ directory so any later agent can find it. Trigger when you (or an agent you are running as) hit a piece of information that is not specific to the current task but that other agents would want to know before writing or reviewing code in this repo. Always checks the existing nuggets of other agents for the same fact before writing, so nuggets are not duplicated. Not for task findings, not for obvious practices, and never a place to record conclusions about the work in progress.
argument-hint: [optional: the nugget in a sentence]
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# Save nugget

Capture one durable fact about the codebase into the shared workspace nuggets, with its source, so the next agent does not have to rediscover it.

A **nugget** is information that is *about the repo*, not about your task: a convention, a rule, a structural principle, a peculiarity, an explicit instruction found in documentation. It outlives the task that surfaced it and is useful to **any** agent — an augmentor about to write code, an enforcer judging conventions, an investigator orienting itself.

## What qualifies

Save it if another agent, arriving cold, would write worse code or waste effort without knowing it:

- **Unusual or repo-specific conventions** — a naming, layering, module or error-handling pattern that is consistent here but not standard practice generally.
- **Explicit documented instructions** — a README/CONTRIBUTING/CLAUDE.md/ADR/wiki page that states "do X this way" or "never do Y". Note where it says so.
- **Structural principles** — what lives in which layer or package and why; what the boundary rules are; where a given kind of code is registered.
- **Test, build and tooling peculiarities** — how tests must be run here, a fixture/harness requirement, a lint rule that bites, a generated artifact that must be regenerated rather than edited.
- **Exceptions and traps** — a place that deliberately violates the general rule, a deprecated path that still looks current, a helper that must be used instead of the obvious inline approach, an abstraction that is load-bearing in a non-obvious way.

## What does NOT qualify

- **Anything specific to the task at hand.** Findings that answer your current question belong in your report to your parent, not here. This is the most common misuse.
- Conclusions, plans, progress, or status of work in flight.
- General software practice that is not specific to this codebase.
- Anything already stated in a `CLAUDE.md`, or plainly visible from a file's name or location.
- A restatement of what some code does. A nugget is a rule or a principle, not a description of one function.
- Speculation. If you have not verified it, do not save it. "Two files do this" is a pattern worth one careful look, not yet a convention.

## Workflow

### 1. Locate the workspace

The agent workspace is the directory that sits alongside the repo roots and holds the `specs/` folder — in the Ardoq checkout that is `.aispace/`, but the name varies, so detect it rather than assuming:

```bash
# walk up from the working directory looking for the workspace
d=$PWD; while [ "$d" != "/" ]; do
  for c in "$d"/.aispace; do [ -d "$c" ] && echo "$c"; done
  d=$(dirname "$d"); done
```

Prefer a candidate that contains `specs/`. If several exist, pick the one nearest the repos you are working in. If none exists, do not invent a location elsewhere — create `nuggets/` inside the workspace the parent named, or ask.

Nugget files live in `<workspace>/nuggets/`. Create the directory if it is missing.

### 2. Check for duplication — before writing anything

Read what is already recorded. This is mandatory: the nugget files are shared, and a second copy of a known fact makes the whole set less trustworthy.

```bash
ls <workspace>/nuggets/
grep -ril "<distinctive keyword from your nugget>" <workspace>/nuggets/
```

Search on the distinctive terms of the fact itself — the namespace, helper, flag, file or rule name — not on your phrasing of it. Then read the files that hit. Outcomes:

- **Already recorded, same fact** → do not save. Say so in your report and move on.
- **Recorded but you have verified it more precisely, or found the authoritative source it lacked** → add the refinement to **your own** file, with a one-line cross-reference to the existing note (`See also: nuggets/<file>.md — "<nugget title>"`).
- **Recorded and now wrong or outdated** → record the correction in **your own** file, cite your evidence, and cross-reference the stale note. State plainly that it contradicts the earlier one.
- **Not recorded** → save it.

**Never edit or delete another agent's nugget file.** You only ever append to your own. Reconciling and reorganising the collected nuggets is a separate job.

### 3. Write it

One file per agent. Name it `<agent-type>-<YYYYMMDD-HHMMSS>.md`, timestamped at the agent's first nugget, e.g. `nuggets/investigator-20260916-143210.md`. If you save several nuggets during one run, **append** to that same file rather than creating more.

File header, written once:

```markdown
# Nuggets — <agent-type>, <YYYY-MM-DD>

Context: <one clause naming what this agent was doing when it found these — enough to judge the finding's provenance, no more.>
```

Then one block per nugget:

```markdown
## <short, declarative title stating the rule>

- **Kind:** convention | structure | documented-rule | test | build | exception | trap
- **Applies to:** <repo, package or path scope this holds within>
- **Source:** <path/to/file.clj:120> · <doc path or URL> · <command whose output shows it>
- **Confidence:** verified in N places | stated in documentation | single instance, unconfirmed
- **Nugget:** <the rule, stated as a rule in one or two sentences>
- **Comment:** <optional: why it matters, when it does not apply, what breaks if ignored>
```

Rules for the content:

- **State it as a rule**, in the imperative or as a general fact — not as a story about how you found it.
- **Always cite a source** a later agent can open: `file:line`, a doc path, a URL, or the exact command. A nugget without a source is not usable.
- **Be honest about confidence.** Mark a single unconfirmed instance as such rather than promoting it to a convention.
- **Scope it.** A rule that holds only in one package must say so; an over-broad nugget is worse than none.
- **Keep it short.** A few lines. No background, no narrative, no code blocks unless the exact text *is* the rule.

### 4. Report it in one line

Tell your parent, briefly: what you saved and where (`saved nugget "<title>" → nuggets/<file>.md`), or that the fact was already recorded. Never let the nugget displace or pad the answer your parent actually asked for.
