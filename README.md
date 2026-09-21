# agent-plans

A lifecycle convention for plan documents, packaged as a skill for coding agents.

Plans are temporary coordination docs. They exist to make upcoming and in-progress work
findable — and once the code, tests and release notes are the source of truth, they are
deleted. This skill defines where a plan lives at each stage, when it moves, and what it
must contain to be picked up by someone else.

```
docs/
├── plans/
│   ├── ideas/     Proposals. May or may not happen.
│   ├── ready/     Decided. Plan and tasks written. Not started.
│   └── ongoing/   Being implemented. Status header required.
└── decisions/     Durable rationale, written when a plan retires.
```

One file per topic. **The filename never changes** — only the directory does. When the work
is verified in the environment that matters, the durable *why* moves to `docs/decisions/`
and the plan file is deleted. Nothing is archived; git history is the archive.

## Why

An agent that opens your repo has no idea what is in flight. Chat scrollback is gone, the
issue tracker says "open", and a merged PR does not say whether the thing actually works in
production. `ls docs/plans/ongoing/` answers it in one command, and each file's Status
header says where it stopped and what to do next.

The convention is deliberately small: folders, `git mv`, and one required header. No
scripts, no config file, no tooling to install.

## Install

**Claude Code**

```
/plugin marketplace add alvaro-francisco-gil/agent-plans
/plugin install plans-lifecycle
```

**Codex / Cursor** — the repo ships `.codex-plugin/` and `.cursor-plugin/` manifests that
point at the same `skills/` directory.

**Any other agent** — clone the repo and point your agent at `skills/`, or copy
`skills/managing-plans-lifecycle/` into wherever your tool keeps skills. The skill is a
single Markdown file with YAML frontmatter and no runtime dependencies.

## What ships

| Skill | Purpose |
|---|---|
| `managing-plans-lifecycle` | The convention. Stands alone. |
| `superpowers-plans-bridge` | Optional. Loads only if you also use [superpowers](https://github.com/obra/superpowers), and redirects its spec output into `docs/plans/ideas/`. |

Both ship in one install. The bridge stays dormant unless superpowers is actually in play,
so there is no dependency on it and nothing to configure if you do not use it.

## Layering

The skill defines the lifecycle and nothing else. Everything repo-specific — which stages
you use, whether a priority label is required, what counts as "verified", which repo a
cross-cutting plan belongs in — belongs in your own agent instructions (`AGENTS.md`,
`CLAUDE.md`, or equivalent), **which win wherever they differ**.

Optional stages (`ongoing/soak/`, `docs/incidents/`, `docs/ops/`) exist only if your
instructions declare them. Don't create them speculatively.

## Conventions worth knowing before you adopt it

- **No date prefixes.** `image-cropper-ui.md`, not `2026-03-14-image-cropper-ui.md`. The
  name is stable across the lifecycle; git log carries the dates.
- **Finished plans are deleted, not archived.** An archived plan is a stale snapshot that
  lies to the next reader as soon as the code drifts.
- **"Merged" is not the retirement gate — "verified" is.** Retire on confirmed behaviour in
  the environment that matters, not on a green PR.
- **No `queued/` or `blocked/`.** Decided-not-started is `ready/`. Waiting on a trigger is
  `ideas/`, or `ready/` with the gate stated inline.

## Versioning

Claude Code pins an installed plugin to the `version` string in `.claude-plugin/plugin.json`
and auto-updates when it changes. **Content changes ship only if that version is bumped** —
pushing commits without a bump leaves existing users on the cached copy.

## Licence

MIT. See [LICENSE](LICENSE).
