# Topic catalog

Ask in this order. Skip a topic when it does not apply to the stack or when tooling already settles it (record that as a rule without asking). "Detect" says what to sample in Mode B to find conflicts. "Key decision" is the question for Mode A, with up to 5 options.

| # | Topic (doc file) | Detect | Key decision |
|---|---|---|---|
| 1 | Testing (`testing.md`) | test frameworks, file placement (`__tests__`, `*.test.*`, `tests/`), mocking style, coverage config | Test levels required; framework; file placement; mocking policy; coverage bar |
| 2 | Comments and inline docs (`comments.md`) | comment density, doc-comment style (JSDoc, docstrings), TODO format | When to comment (why not what); doc-comments on public API only or everywhere; TODO policy |
| 3 | Naming (`naming.md`) | identifier case per kind, file name case, boolean/function prefixes, abbreviations | Case per kind (variables, types, constants, files); abbreviations policy |
| 4 | Formatting and linting (`formatting.md`) | formatter/linter configs, line length, import order | Formatter and linter of record; who runs them (hook, CI, both); line length |
| 5 | Types and data modeling (`types.md`) | strictness flags, `any`/untyped usage, schema libraries, DTO vs entity | Strictness level; validation at boundaries; nullability policy |
| 6 | Error handling (`errors.md`) | throw vs result types, custom error classes, swallowed errors | Throw vs return errors; error taxonomy; what may be caught and how |
| 7 | Logging and observability (`logging.md`) | logger library, levels, structured vs text, PII in logs | Logger; levels; structured fields; PII rules |
| 8 | Dependencies (`dependencies.md`) | lockfile, version pinning, number of overlapping libs | Add-a-dependency bar; pinning policy; banned or preferred libs |
| 9 | Configuration and secrets (`config.md`) | env var handling, config files, `.env` in git | Config source of truth; validation; secret handling |
| 10 | API and interface design (`api.md`) | REST/RPC/GraphQL style, versioning, response envelopes, error shape | Style; versioning; naming; pagination and error shape |
| 11 | Data and persistence (`data.md`) | ORM/query builder, migrations, transaction handling | Access layer; migration policy; transaction boundaries |
| 12 | Security (`security.md`) | auth checks location, input validation, secret scanning | Validation location; authz pattern; forbidden practices |
| 13 | Performance and concurrency (`performance.md`) | async style, caching, N+1 patterns | Async model; caching policy; when to optimize |
| 14 | Git, commits and PRs (`git.md`) | commit message history, branch names, PR template | Commit format; branching model; PR size and review rules; who may push to main |
| 15 | Documentation (`documentation.md`) | README, ADRs, changelog | What must be documented; ADR policy; changelog format |
| 16 | UI and accessibility (`ui.md`, only if there is a UI) | component patterns, styling approach, a11y lint | Component structure; styling system; a11y bar |
| 17 | Project structure and modules (`structure.md`) | directory layout, barrel files, file size, co-location | Layout by feature or by layer; file and module size limits; public vs internal exports |
| 18 | Architecture (`architecture.md`) | layering, dependency direction, cross-module imports, shared code | Layers and allowed dependency direction; module boundaries; where shared code lives; patterns in and out (DI, events, etc.) |

## Notes

- Topics 17 and 18 come last because earlier answers (types, errors, data) constrain them.
- Add a custom topic if the user names one the catalog lacks (monorepo tooling, ML pipelines, infrastructure as code, mobile). Use the same doc template.
- Combine tiny topics into one doc only if each would be under about 10 lines.
