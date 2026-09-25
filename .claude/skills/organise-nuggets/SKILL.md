---
name: organise-nuggets
version: 1.0.0
description: Go through every nugget collected in the agent workspace's nuggets/ directory, verify each one against the repo it claims to describe, correct or discard the ones that don't hold up, and fold the survivors into each repo's durable agent-facing instruction file. Trigger when the user says "organise nuggets", "organize the nuggets", "process the nuggets", "consolidate agent nuggets", "fold the nuggets into the repos", or hands over the nuggets directory to be curated. The user may give specific instructions (a repo to limit to, nuggets to drop or keep, where to write) — those always win. For Ardoq repos the destination is the repo's gitignored .nuggets file, never its shared .claude directory.
argument-hint: [optional: repo to limit to, or specific instructions]
allowed-tools: Read, Write, Edit, Grep, Glob, Bash, Agent, TodoWrite
---

# Organise nuggets

Agents drop raw findings into `<workspace>/nuggets/` via the `save-nugget` skill — one file per agent, unreviewed, unverified, and duplicated across agents. Your job is to turn that pile into a curated, verified, per-repo body of instruction that any future agent reads before working in that repo.

Two responsibilities, in order: **verify**, then **consolidate**. Never consolidate an unverified nugget.

User instructions override every default here — a named repo to limit to, nuggets to drop or keep, a different destination, a specific organising scheme.

## 1. Collect

Locate the workspace — the directory alongside the repo roots that holds `specs/` (`.aispace/` in the Ardoq checkout; detect, don't assume). Read **every** file in `<workspace>/nuggets/`, in full. Build a working list of every nugget with: its title, claim, stated scope, cited source, stated confidence, and which nugget file it came from.

Track the list with TodoWrite if it is long. Report the count before you start verifying.

## 2. Verify every nugget against its repo

For each nugget, two questions, both answered against the actual code:

**(a) Is the claim true, as stated?** Open the cited source. Then check whether the claim generalises the way it says it does: if it is stated as a convention, sample several places that should exhibit it; if it cites documentation, confirm the document still says that; if it names a file, symbol or flag, confirm it still exists. A nugget whose source has moved or been deleted is stale until re-grounded.

**(b) Does it qualify as a nugget at all?** Apply the `save-nugget` criteria strictly. It qualifies only if an agent arriving cold would write worse code or waste effort without it. It does **not** qualify if it is specific to the task that produced it, is general software practice, is already stated in a `CLAUDE.md` the agents read anyway, merely describes what a piece of code does, or is plainly visible from a file's name or location.

Then act:

- **Valid and qualifying** → keep. Tighten the wording if it is loose, but do not change the claim.
- **True but over-broad** → narrow the scope to where it actually holds (a package, a layer, a service) and say so explicitly. This is the most common repair.
- **True but under-specified** → add the missing precision: the exact name, the real boundary, the authoritative source rather than an incidental example.
- **Partly true** → split it. Keep the part that holds, discard the rest, and note that the original conflated the two.
- **Duplicated across nugget files** → merge into one nugget with the strongest source and the widest verified scope. This is expected: agents check for duplicates but cannot always recognise a differently-worded twin.
- **Contradicted by another nugget** → resolve it in the code and keep the verified one. If you cannot resolve it, keep neither as a rule; record the open question instead, marked as unresolved.
- **False, stale, or not qualifying** → discard.

**Edit the nugget files to reflect your verdicts before you finish.** For every nugget you changed, narrowed, split, merged or discarded, correct the source file: fix the entry in place, or remove it and leave a one-line tombstone stating what it claimed and why it was discarded (so the same wrong fact does not get re-saved next week). The nuggets directory must end up containing only nuggets you have verified, plus those tombstones. Do not silently leave a discarded claim sitting in the nugget files.

If the nugget set is genuinely large — many dozens of nuggets across several big repos — dispatch one `investigator` per repo to verify that repo's nuggets against that repo, giving each the nugget list for its repo, the verification questions above, and instruction to return a per-nugget verdict (`valid` / `narrow to X` / `split` / `discard`, with evidence) and nothing else. Do the consolidation yourself. For an ordinary set, verify it yourself — delegation costs more than it saves.

## 3. Consolidate into each repo's instruction file

Group the surviving nuggets by the repo they apply to, and write them into that repo's durable agent-facing file.

**Destination:**

- **Ardoq repos** (`ardoq-api`, `ardoq-packages`, `devops-monorepo`, `graphlake`, `ardoq-ai-research`) → the repo's **`.nuggets`** file at its root. These are gitignored via `.git/info/exclude`, so they are local and safe to rewrite. **Never edit an Ardoq repo's `.claude/` directory or its `CLAUDE.md`** — those are committed, shared with the whole team, and not yours to change. If a repo has no `.nuggets` file, create one and add `.nuggets` to that repo's `.git/info/exclude` (not to the shared `.gitignore`).
- **Other repos** → `.agents/` or `.claude/` as that repo's convention indicates, defaulting to whichever already exists. If both are absent, write `.nuggets` as above and say so in your report.
- A nugget that spans repos goes in **each** applicable repo's file, worded for that repo. Do not create a cross-repo file.

**Structure inside the file.** Rewrite it whole, organised by subject so an agent can find the rule it needs — typical sections: Structure & layering · Conventions · Tests · Build & tooling · Exceptions & traps · Unresolved. Drop empty sections. Keep the entries as terse rules:

```markdown
## Conventions

- **<the rule, stated imperatively>** — <scope where it holds>. Source: `path/to/file.clj:120`.
```

Rules for the written output:

- **State rules, not stories.** Imperative or general fact. No account of how it was discovered, no agent names, no task context.
- **Every entry keeps a citable source.** An entry a later agent cannot check is worthless.
- **Mark anything less than verified.** `(single instance, unconfirmed)` or `(from docs, not verified in code)`. Never promote a weak nugget by omitting its caveat.
- **Preserve what is already in the file.** These files accumulate across runs: merge into the existing content, keep entries you cannot disprove, and do not drop an entry just because this run's nuggets did not mention it. Remove an existing entry only when you have verified it is wrong or obsolete — and say so in your report.
- **Terse.** A repo's file is a reference an agent skims, not a document it reads. No preamble, no rationale beyond what changes behaviour.

## 4. Clear the queue and report

Leave the nuggets directory in the verified state described in step 2. Then report, tersely:

- Counts: nuggets read, kept, narrowed, merged, split, discarded.
- Per repo: the destination file and how many entries it now holds.
- Every **discarded** nugget: one line each — the claim and why it failed. This is the part the user most needs to see.
- Every **existing** entry you removed from a repo file, with the evidence.
- Unresolved contradictions, and anything you could not verify.

No padding. Do not restate the surviving nuggets in the report — they are in the files.

## Rules

- Verify before consolidating. Never copy an unverified claim into a repo file.
- Never edit an Ardoq repo's `.claude/`, `CLAUDE.md`, or shared `.gitignore`.
- Never modify code. This skill only reads code and writes instruction and nugget files.
- Discarding is normal and expected. A wrong nugget is worse than a missing one, because agents will act on it.
- User instructions win over every default in this skill.
