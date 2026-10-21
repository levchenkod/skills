# map-repository

Produce a comprehensive analysis of the repo as `tmp/.rexplore/repo-map.json` ([schema](references/map-schema.md)). A separate agent will build an interactive UI from this file alone, so it needs names, descriptions, interfaces and outputs, use cases, edge cases, and code examples for every actor.

## Code is the source of truth

README and docs are **claims**. Read them last, if at all, and record every claim the code contradicts in `meta.claims_vs_code`. Everything else in the map traces to code you read.

## Steps

1. **Orient.** Read manifests, config, directory layout, and `git ls-files`. Fill `meta` (stack, purpose, `excluded` for generated, vendored, lockfiles). Locate every entrypoint: servers, routes, CLIs, workers, cron jobs, UI roots, exported library API. Done when each entrypoint is a node.
2. **Enumerate actors.** Walk source directories. Each controller, service, pipeline stage, store, external integration, config surface, job, prompt, tool and UI surface becomes a node. Done when every source file is covered by a node or listed in `meta.excluded`. Read each file in full.
3. **Fill the details.** Per node: `interface`, `use_cases`, `edge_cases`, `example` (verbatim excerpt) and `evidence`. Edge cases come from reading error handling, validation, limits, retries, timeouts, defaults and TODOs. Done when no node has an empty field except as `unknown: <what to check>`.
4. **Wire the graph.** Follow imports, calls, events, queues, DB and network access to create edges with the payload that flows. Done when every node has at least one edge, or is an entrypoint or external with a stated reason.
5. **Trace scenarios.** For each distinct thing the system does end to end (each entrypoint, plus each significant branch such as auth failure, retry, fallback, cache hit), record the ordered nodes and edges, the outcome and the variants. Done when every entrypoint appears in at least one scenario.
6. **Record extension points.** For each kind of instance the repo lets you add (route, tool, prompt, job, model, migration, config entry...), derive the steps from an existing instance and the files that registration touches, with a verbatim example. Done when each registry or plugin-style directory has an extension point.
7. **Survey practices.** For every `practices` concern, search for it and record present, partial or absent with evidence. Absence is a finding: cite where it would live.
8. **Validate.** Parse the JSON and check the schema rules: ids resolve, excerpts match their lines, no placeholder text. Fix and re-run until clean.

Write `tmp/.rexplore/repo-map.json` and finish by reporting counts (nodes, edges, scenarios, extension points, absent practices) and anything marked `unknown`.

## Large repos

When the source exceeds what one pass can read in full, split steps 2-3 by top-level area and fan out one subagent per area. Each returns node fragments in schema form; you merge them, then do steps 4-8 yourself on the combined graph, since cross-area edges and scenarios need the whole picture.
