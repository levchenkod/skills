# repo-map.json schema

Single source of truth for the map-repository output and the visualise-repository input. Valid JSON, UTF-8, ids are lowercase kebab-case and unique.

```json
{
  "meta": {
    "repo": "name",
    "commit": "git sha or 'uncommitted'",
    "generated": "ISO date",
    "stack": ["language/framework/runtime ..."],
    "purpose": "what the system does, 1-3 sentences",
    "entrypoints": ["node-id"],
    "excluded": [{ "path": "dist/", "reason": "generated" }],
    "claims_vs_code": [{ "claim": "doc says X", "source": "README.md", "reality": "code does Y", "evidence": "path:line" }]
  },
  "nodes": [{
    "id": "chat-controller",
    "name": "ChatController",
    "kind": "entrypoint | controller | service | stage | store | external | config | job | ui | tool | prompt",
    "path": "src/chat/controller.ts",
    "summary": "what it is and why it exists",
    "interface": { "inputs": "shape/source", "outputs": "shape/destination", "side_effects": "writes, calls, emits" },
    "use_cases": ["when and why it runs"],
    "edge_cases": ["failure modes, limits, odd inputs, with behavior"],
    "example": { "lang": "ts", "path": "src/chat/controller.ts", "lines": "12-30", "code": "verbatim excerpt" },
    "evidence": ["path:line"]
  }],
  "edges": [{
    "from": "node-id", "to": "node-id",
    "kind": "calls | emits | reads | writes | configures | extends | renders",
    "label": "what flows",
    "data": "payload shape",
    "evidence": "path:line"
  }],
  "scenarios": [{
    "id": "user-sends-message",
    "name": "User sends a message",
    "trigger": "what starts it",
    "nodes": ["ordered node ids"],
    "edges": [["from-id", "to-id"]],
    "outcome": "what the caller observes",
    "edge_cases": ["variants and failures along this path"]
  }],
  "extension_points": [{
    "id": "add-tool",
    "name": "Add a new tool",
    "touches": ["node ids"],
    "steps": [{ "do": "imperative step", "path": "file to create or edit", "example": "verbatim snippet from an existing instance" }],
    "gotchas": ["registration order, naming rules, required tests"]
  }],
  "practices": [{
    "concern": "tests | evals | ci | deploy | rate-limiting | auth | observability | error-handling | retries-timeouts | config-secrets | data-migrations | security | cost-controls | docs",
    "status": "present | partial | absent",
    "evidence": ["path:line, or 'absent: <where it would live>'"],
    "notes": "what exists, how it works"
  }]
}
```

Rules:
- `kind` is an open vocabulary; the values above are the defaults. Keep one kind per node, and reuse the repo's own terms for names.
- Every edge endpoint, scenario node and `touches` entry resolves to a node id.
- `example.code` is copied from the file at the cited lines.
- Unknowns are `"unknown: <what to check>"`, never omitted and never guessed.
