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
│   ├── ready/     Approved. Not started. Detailed plan written at start.
│   └── ongoing/   Being implemented. Metadata block required.
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

> **Tested:** Claude Code, end to end — clean clone, install, both skills parse, version
> bump propagates. **Inferred:** Codex, Cursor and Gemini. Those manifests follow the shape
> a published plugin uses and are checked by `scripts/validate.py`, but nothing has yet
> loaded them in those tools. Tracked in
> [docs/plans/ready/verify-codex-and-cursor-packaging.md](docs/plans/ready/verify-codex-and-cursor-packaging.md).

**Any other agent** — clone the repo and point your agent at `skills/`, or copy
`skills/managing-plans-lifecycle/` into wherever your tool keeps skills. The skill is a
single Markdown file with YAML frontmatter and no runtime dependencies.

## What ships

| Skill | Purpose |
|---|---|
| `managing-plans-lifecycle` | The convention. Stands alone. |
| `superpowers-plans-bridge` | Optional. Loads only if you also use [superpowers](https://github.com/obra/superpowers) in a repo that already has `docs/plans/`. Changes where superpowers saves and reads plan files, never what it does. |

Both ship in one install. The bridge stays dormant unless superpowers is actually in play,
so there is no dependency on it and nothing to configure if you do not use it.

## Try it

In any repo, after installing:

- "Write up an idea for adding rate limiting to the API." Creates `docs/plans/ideas/api-rate-limiting.md` with a `Priority` line.
- "We've decided on rate limiting, move it to ready, and start it." Moves the same file with `git mv` through `ready/` to `ongoing/`, with `Gate` and `Next` filled in.
- "What's in flight, and what's stale?" Lists `docs/plans/ongoing/`, reads each plan's `Next` line, and uses `git log` to flag plans nobody has touched recently.

## Privacy and data

Both skills are plain Markdown instructions. They contain no code, run no commands of their
own, make no network requests and collect nothing. Everything they produce is ordinary files
in your own repo, under `docs/plans/` and `docs/decisions/`.

## Troubleshooting

- **The skill doesn't fire.** Ask about plans directly ("where should this plan live?"), or
  run `/plugin` and check that `plans-lifecycle` is installed and enabled.
- **Superpowers still writes to `docs/superpowers/`.** The bridge only acts in repos that
  already have `docs/plans/`. Create the folders first. If a file still lands there, the
  bridge tells the agent to move it to `docs/plans/ideas/` and strip the date.
- **An update didn't arrive.** Claude Code updates only when the plugin's `version`
  changes. Run `/plugin` and update the marketplace.

## Support

Questions and bugs:
[GitHub issues](https://github.com/alvaro-francisco-gil/agent-plans/issues). Report
security problems privately through
[GitHub security advisories](https://github.com/alvaro-francisco-gil/agent-plans/security/advisories/new).

## Layering

The skill defines the lifecycle and nothing else. Everything repo-specific — which stages
you use, whether a priority label is required, what counts as "verified", which repo a
cross-cutting plan belongs in — belongs in your own agent instructions (`AGENTS.md`,
`CLAUDE.md`, or equivalent), **which win wherever they differ**.

Optional stages (`ongoing/soak/`, `docs/incidents/`, `docs/ops/`) exist only if your
instructions declare them. Don't create them speculatively.

## Conventions worth knowing before you adopt it

- **v2 (2.0.0): one metadata block, state derived.** Each plan carries `Priority`, `Gate`
  and `Next` (plus `Landed` and `Due` where they apply) under its title. `Updated` and
  `Stage` are gone: the folder and git carry them. See "Migrating from v1" in the skill.

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
