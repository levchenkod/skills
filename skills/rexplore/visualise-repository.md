# visualise-repository

Turn `tmp/.rexplore/repo-map.json` ([schema](references/map-schema.md)) into `tmp/.rexplore/index.html`, an interactive page that gives the reader a complete understanding of the project: capabilities, limitations, edge cases and gaps. The required sections below are a floor. Extend them wherever the map supports a deeper insight.

## Constraints

- One self-contained HTML file: map data embedded as JSON, inline CSS and JS, SVG or canvas graph, opens from `file://` with no build step. Libraries only from a pinned CDN version, with the page still usable when they fail.
- Every statement traces to the map or to a cited external source. Judgement beyond the map (standards alignment, risk) is labelled as assessment.
- Light and dark theme, keyboard-reachable controls, readable at laptop width.

## Required sections

1. **Overview.** `meta.purpose`, stack, entrypoints, and `claims_vs_code` as a "docs vs reality" callout.
2. **Actors graph.** Nodes for key actors, controllers, services, stages and stores; edges for data flow, labelled with `label` and `data`. Colour and shape by `kind`, with a legend.
   - **Scenarios list** on the side. Clicking a scenario highlights its nodes and edges and dims the rest. Show its trigger, outcome and edge cases. A reset control restores the full graph.
   - **Node pane.** Clicking a node opens a pane with summary, interface, use cases, edge cases, a syntax-highlighted code example with its path and lines, and links to connected nodes.
3. **How to.** An accordion with one item per `extension_points` entry (add a prompt, tool, route, ...): ordered steps, file paths, the verbatim example, gotchas.
4. **Gaps.** What separates this system from production-grade deployment: every `practices` entry that is `partial` or `absent` (tests, evals, CI, deploy, rate limiting, auth, observability, ...), with the evidence, the risk it carries, and what closing it looks like. Order by severity.

## Extend with

- **Capabilities and limitations**, derived from nodes, scenarios and edge cases.
- **Edge-case catalogue**, searchable and filterable by node and scenario.
- **Standards alignment.** Compare each practice and architectural choice with current industry practice for this stack and domain (for an LLM system: evals, guardrails, tracing, prompt versioning, cost controls; for services: the twelve-factor, SLOs, security baselines). Use web search when available, so "current" means today. Cite sources, and give each area a rating (aligned, partial, behind) with one sentence of reasoning.
- **Prioritised recommendations**, ordered by impact against effort.

## Steps

1. Read the map in full and validate it parses. A map that fails the schema goes back to map-repository.
2. Plan the sections: list the additional insights the map supports, and research the standards for this stack.
3. Build the page.
4. **Verify in a browser** (the run skill or a browser tool). Done when the page loads without console errors, every scenario highlights the nodes it lists while the others dim, reset restores the graph, every node opens a pane with its example, and every accordion item expands. Fix and re-check until all hold.
