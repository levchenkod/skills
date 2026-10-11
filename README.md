# skills

Agent-agnostic [Agent Skills](https://agentskills.io) by [@levchenkod](https://github.com/levchenkod).
Works with Claude Code, Cursor, Codex, OpenCode, and any other agent supported by the `skills` CLI.

## Install

Pick skills interactively:

```bash
npx skills@latest add levchenkod/skills
```

Install a specific skill:

```bash
npx skills@latest add levchenkod/skills --skill <skill-name>
```

List what's available without installing:

```bash
npx skills@latest add levchenkod/skills --list
```

Pin to a released version (git tag):

```bash
npx skills@latest add levchenkod/skills#v0.1.0
```

## Layout

```
skills/<skill-name>/SKILL.md   # one directory per skill (installable)
template/SKILL.md              # starting point for new skills (not installable)
CHANGELOG.md
```

Each skill is a directory under `skills/` with a `SKILL.md` (YAML frontmatter with `name` and
`description`, then instructions). Extra files (scripts, references, assets) live next to it
and are referenced relatively.

## Adding a skill

1. `cp -r template skills/<skill-name>` and edit `SKILL.md`. The `name` must match the directory name.
2. Set `metadata.version` in the frontmatter (SemVer).
3. Add an entry under `[Unreleased]` in `CHANGELOG.md`.

## Versioning

- **Repo**: SemVer, released as git tags (`v0.1.0`) and tracked in `CHANGELOG.md` / `package.json`.
- **Skill**: each skill carries its own SemVer in `metadata.version`. Bump it whenever the skill's behavior changes.

Release: move `[Unreleased]` entries under a new version heading, bump `package.json`, then:

```bash
git tag v<version> && git push --tags
```

## License

MIT
