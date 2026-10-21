---
name: rexplore
description: Explore an unfamiliar repo in two stages - map-repository (code to a structured actor graph, repo-map.json) then visualise-repository (that map to an interactive HTML for full understanding of capabilities, limitations, edge cases, gaps). Use when the user invokes /rexplore, or asks to map, explore, understand or visualise a codebase or how it compares to industry standards.
disable-model-invocation: true
metadata:
  version: 0.1.0
---

# rexplore

Two stages joined by one file. The map is the contract: stage 1 writes it, stage 2 reads only it (plus the cited sources).

| Stage | Reads | Writes | Instructions |
|---|---|---|---|
| map-repository | the repo's code | `tmp/.rexplore/repo-map.json` | [map-repository.md](map-repository.md) |
| visualise-repository | `repo-map.json` | `tmp/.rexplore/index.html` | [visualise-repository.md](visualise-repository.md) |

Map schema (both stages depend on it): [references/map-schema.md](references/map-schema.md).

## Routing

- User names one stage ("map", "visualise"): run that stage only.
- Otherwise run both, map first. The visualise stage starts from the finished file, not from your memory of the exploration.
- Visualise requested and `tmp/.rexplore/repo-map.json` absent: run map first.
- Both files exist and the repo changed since `meta.commit`: ask whether to re-map.

`tmp/.rexplore/` is scratch output in the repo root. Never overwrite another user's file there without saying so, and tell the user not to commit it.

Finish with 3-5 lines in chat (what the repo is, headline gaps) and the path to the HTML on its own line.
