---
name: init-conventions
description: Interview the user, one multiple-choice question at a time, about code style, patterns and conventions, then write them to docs/conventions/*.md and add a router to AGENT.md so agents follow one standard. Manual use only, for a new project or an existing one with contradicting conventions.
disable-model-invocation: true
metadata:
  version: 0.1.0
---

# init-conventions

Turn the user's preferences into a written standard that agents read and follow.

- **Output:** `<root>/docs/conventions/**/*.md` (one short doc per topic, plus an index) and a router section in `<root>/AGENT.md`.
- **Use case A, new project:** no conventions exist; the user chooses from sensible defaults.
- **Use case B, existing project:** code already shows conflicting styles; the user picks a winner per conflict.
- **Idempotent:** running again updates existing docs. It never discards rules the user did not touch.

`<root>` is the git root (or the directory the user names). Use `AGENT.md` as the router file. If `AGENTS.md` or `CLAUDE.md` exists and `AGENT.md` does not, ask which one to use.

## Interview rules

1. **One question per turn.** Never batch questions.
2. **Use the agent's selector tool** (Claude Code: `AskUserQuestion`; elsewhere, the equivalent). If none exists, print a numbered list and ask for a number.
3. **At most 5 options.** Put the recommended option first and label it `(Recommended)`. Add a free-text "Other" if the tool does not supply one. Each option gets a one-line consequence, not just a name.
4. **Ground questions in the repo.** Before asking, look at the code. Offer the detected choice as the recommended option, with evidence ("142 files use `camelCase`, 9 use `snake_case`").
5. **Skip what the repo already enforces.** If a linter, formatter or compiler setting settles a topic, record it as a rule and do not ask.
6. **Allow "skip" and "no rule".** An unanswered topic produces no doc rather than an invented rule.
7. **Do not lecture.** One sentence of context per question at most.

## Procedure

### 1. Scan (silent, no questions yet)

- Languages, frameworks, package manager, monorepo layout (manifests, lockfiles).
- Tooling that already encodes conventions: linter/formatter/type-checker configs, `.editorconfig`, CI workflows, pre-commit hooks, commit-lint.
- Existing guidance: `AGENT.md`, `AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`, `docs/`, and anything already under `docs/conventions/`.
- Code samples per topic, enough to measure the dominant style and find conflicts (see `references/topics.md`, "Detect" column).
- Decide the mode: no code or conventions means **A**; conflicting styles or docs means **B**. A mix is fine, handled per topic.

### 2. Plan

Tell the user in a few lines: the detected stack, the mode, which topics will be asked (from `references/topics.md`, skipping those that do not apply), and which are already settled by tooling. Ask one selector question: proceed, add or remove topics, or stop.

### 3. Interview, topic by topic

Work through the topics in the order of `references/topics.md`: tests and comments first, architecture and modules last. For each:

- **Mode B with a detected conflict:** show both patterns with counts and a representative file path each, then ask which to standardize on. Add an option for "keep both, scoped" (for example, legacy directory exempt).
- **Mode A or no conflict:** ask the topic's key decision with up to 5 options.
- Follow up only when the answer opens a real fork (for example, "mock at the boundary" leads to "which mocking library"). Keep it to two or three follow-ups per topic.
- Keep a running list of decisions in memory. Do not write files yet.

### 4. Review before writing

Show a compact summary: one line per topic with the chosen rule, flagging any rule that contradicts existing code (migration needed) or existing docs (will be overwritten). Ask one selector question: write all, revise a topic, or cancel.

### 5. Write

Follow `references/doc-template.md` exactly.

- One file per topic at `docs/conventions/<topic>.md`. Group into subfolders only when a topic splits (for example, `testing/unit.md`, `testing/e2e.md`).
- `docs/conventions/README.md` is the index: topic, one-line rule summary, link.
- When a doc already exists, merge: keep rules the user did not revisit, replace the ones they changed, and drop nothing silently. List removed rules in your final report.
- Rules must be imperative, testable and short. Write "MUST use `vitest`; MUST NOT use `jest`", not "we prefer modern testing". Include a minimal good/bad example only when the rule is ambiguous without one.
- For Mode B, add a "Legacy exceptions" section to the doc listing the paths or patterns exempt for now. Do not rewrite existing code unless asked.

### 6. Update the router

Insert or replace this block in `AGENT.md`, between the markers, leaving everything else untouched. Create the file if missing.

```markdown
<!-- conventions:start (managed by init-conventions) -->
## Conventions

Before writing or changing code, read the convention docs that match the task. Follow them over personal habit or inferred style. If a rule conflicts with the request, say so and ask.

| When you are... | Read |
|---|---|
| Writing or changing tests | [docs/conventions/testing.md](docs/conventions/testing.md) |
| Adding code comments or docs | [docs/conventions/comments.md](docs/conventions/comments.md) |
<!-- conventions:end -->
```

Generate one row per doc actually written, with a "When you are..." phrase specific enough that an agent can match it to a task. Also link the index `docs/conventions/README.md` on the first line after the heading.

### 7. Report

List the files created and updated, rules removed or changed, topics skipped, and conflicts left with legacy exceptions. Suggest committing, but do not commit unless asked.

## Gotchas

- Do not ask about things the tools already enforce; it wastes the user's attention and risks contradicting the config.
- Do not invent rules from a single sample. A style used in fewer than a few files is not a convention; ask instead.
- Keep each doc under about 80 lines. Agents skip long docs, and the router makes many small docs cheap to load.
- Do not duplicate the same rule across docs; link to the owner doc.
- The router is the only part of `AGENT.md` this skill owns. Preserve all content outside the markers.
