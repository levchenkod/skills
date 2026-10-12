---
name: unvibe
description: Analyze rough, prototype-quality code (uncommitted changes, or everything since a commit, branch or PR) and produce a plan-only cleanup document - system state, key actors, loose ends, and a restructuring plan with concrete fixes. Use when the user invokes /unvibe or asks to "unvibe", clean up, review, restructure or make production-ready the result of an exploration, spike or vibe-coding session where behavior is right but the code is messy. Never modifies code.
metadata:
  version: 0.1.0
---

# Unvibe

After a long exploration session the behavior is usually what the user wanted, but the code is a draft:
duplicated, loosely bounded, over-mocked, full of hardcoded values. This skill turns that draft into a
**plan** for making it production-ready. It does not touch the code. The user decides what to act on, and
a plan they trust is more valuable than a refactor they have to re-review line by line.

**Plan-only.** Do not edit, move, delete or commit source files, and do not start fixing things "while
you're there". The only file you write is the plan document. If the user wants the plan executed, that is
a separate follow-up request.

## 1. Pin down the change

The user may name the fixed point: a commit, branch, tag, PR number, or "my uncommitted changes". If they
didn't, infer it, and say which one you chose at the top of the document:

1. Uncommitted work exists (`git status --porcelain` is non-empty): use `git diff HEAD` plus untracked files.
2. Otherwise, on a feature branch: diff against `git merge-base HEAD <default-branch>`.
3. Otherwise, ask the user for the starting point. Don't guess a range.

For a PR number use `gh pr diff <n>`. Collect the file list (`--stat`/`--name-status`) first, then read
the changed files in full, not just hunks (up to roughly 50 files / 5k lines; beyond that, triage as below). Hunks hide the context you need to judge boundaries and
duplication. 
For huge changes, triage instead of reading everything: read tracked diffs fully, read new files far enough
to judge their boundaries, skip generated and vendored code, and state in the document what you skimmed.

Two checks that shape the plan, so do them early:
- **Inconsistent baseline**: if committed files import untracked ones, HEAD doesn't build on its own.
  That dictates commit order (land the new files first). To check, list untracked files
  (`git ls-files --others --exclude-standard`) and grep the modified tracked files' imports for those paths.
- **Formatting churn**: compare `git diff --stat` with `git diff -w --stat`. If a formatter or reflow
  inflated the diff, it hides the real change and is a finding itself; propose a separate commit for it.

Read surrounding code too: callers, sibling modules, existing tests. A change can only be judged "duplicate"
or "off-pattern" relative to what already exists in the repo.

## 2. Gather the project's standards

The plan is only useful if it measures the code against *this* project's rules, not generic taste.

- Read `AGENTS.md`, `AGENT.md`, `CLAUDE.md`, `CONTRIBUTING.md` and any `docs/` style or architecture guides.
  Extract the concrete rules (layout, naming, error handling, testing, lint/format expectations).
- Look for installed skills that carry best practices: scan skill directories such as `.claude/skills`,
  `.agents/skills`, `.cursor`, `skills/`, and the user-level equivalents (`~/.claude/skills`, `~/.agents/skills`).
  Select those whose `name` or `description` mentions "best practice(s)", and also those relevant to the
  stack or to the problems you find (design, architecture, testing), even if the phrase is absent.
  Read the few most relevant (a handful, not every match) and apply them. Note which ones you used in the document.
- Check tooling config (linters, formatters, tsconfig/pyproject, CI) so the plan doesn't contradict it.

If none of these exist, say so and fall back to widely accepted practice for the stack.

## 3. Analyze

Look for the following, and back every finding with evidence: `path:line`, a plain `path` for whole-file or
directory findings, or `absent: <where it should be>` for missing things. A finding without a location is
hard to act on and hard to verify.

- **Risks and contract changes** (check first, they outrank everything else): secrets or tokens exposed to
  clients or logs, removed safeguards or guards, behavior that contradicts docs/ADRs/PRDs, UI fields or
  options with no backend behind them, schema/API changes that break callers.
- **Unused code**: dead functions, unreachable branches, leftover debug output, commented-out code,
  unused exports/imports/files, abandoned experiments, stale TODOs.
- **Useless tests**: tests that mock the unit under test, assert only that mocks were called, duplicate
  other tests, can't fail, or test implementation details instead of behavior. Also note missing tests
  for important behavior, and what level (unit/integration) fits.
- **Pattern and guide violations**: deviations from existing repo conventions or from the standards
  gathered in step 2. Cite the rule.
- **Vague boundaries**: modules that do several jobs, layers that reach across each other (UI calling DB,
  controllers holding business logic), concrete dependencies where an interface belongs, shared mutable
  state, circular imports, leaky abstractions.
- **Duplication**: copy-pasted logic, near-identical functions, parallel structures that should share one
  implementation. Skip duplication that is coincidental and would be worse when merged.
- **Hardcoded values**: URLs, paths, ids, thresholds, timeouts, model names, magic numbers and strings that
  belong in config or constants. Say where config would live per the repo's conventions.
- **Formatting-only churn**: reflowed code that buries the real diff (see step 1).
- **Naming and clarity**: misleading or generic names (`utils`, `helper`, `data2`), files whose name no
  longer matches their content.
- **Commit hygiene**: if the change should be split, propose logical commits (each one coherent and
  reviewable, ideally passing tests on its own).

Number loose ends globally so fixes can reference them; put cross-cutting findings (inconsistent baseline,
formatting churn, stray files) in one group rather than duplicating them. Mark confidence on uncertain items
("likely", "confirm by..."). Stray scratch files (html pages, copied tooling, lockfiles for ignored dirs)
are worth a line each. Be honest about uncertainty. If something looks unused but might be reached dynamically, say "likely unused"
and what to check. Don't pad: a short list of real problems beats a long list of nitpicks, and the user
should be able to trust that every item matters. Order by impact.

## 4. Write the document

Write to `unvibe-plan.md` in the repo root unless the user names another path. Never overwrite an existing
file (pick a new name), and never write inside a repo the user said not to modify (use the path they give,
or fall back to the OS temp directory). Mention that the file is an untracked
scratch file and shouldn't be committed. Use this structure:

```markdown
# Unvibe plan: <short description of the change>

**Scope:** <ref/range analyzed, files and lines changed, anything skimmed>
**Standards applied:** <AGENTS.md etc., best-practice skills used, or "none found">

## 1. System state
What the change does, in a few sentences of plain behavior (what works today and must keep working).

### Key actors
| Actor | Location | Responsibility | Talks to |
(modules, controllers, services, jobs, schemas... the things that matter, not every file)

## 2. Loose ends
Numbered globally (L1, L2...), grouped by category, each item: location - what's wrong - why it matters.
- Risks and contract changes (first)
- Unused code
- Useless or missing tests
- Pattern / guide violations
- Vague boundaries and isolation
- Duplication
- Hardcoded values (note proposed config home)
- Repo state and commit hygiene (baseline, strays, formatting-only churn)

## 3. Proposed structure
High-level target: modules and their single responsibilities, interfaces between them, what moves where.
Include a before/after tree or short diagram when it clarifies. Explain the reasoning for each boundary.

## 4. Suggested fixes
Ordered, numbered list. Each: what to do, the loose ends it resolves (L-numbers), files involved, size (S/M/L), and dependencies on other items.
Put behavior-preserving, low-risk steps first (delete dead code, extract config) and the structural moves
after. Add a proposed commit sequence. Finish with "Safe to skip" for items that are optional, and
"Open questions" for decisions only the user can make, each with your recommended default.
```

Keep it scannable. The reader is about to decide what to act on, so lead each item with the conclusion.

## 5. Hand off

In chat, give a 3-5 line summary (headline problems, biggest structural suggestion), then the path to the
document on its own line. Don't paste the whole plan, and don't offer to start fixing unless it's one short sentence at the end.
