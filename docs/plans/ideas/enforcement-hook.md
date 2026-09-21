# Enforcement hook for the superpowers redirect

**Priority:** low

## Goal

Decide whether `superpowers-plans-bridge` should be backed by a `PreToolUse` hook that
denies writes to a tool-specific plan folder, instead of relying on the skill text alone.

## Context

The bridge resolves one conflict: superpowers' `brainstorming` and `writing-plans` write to
`docs/superpowers/specs|plans/` with date-prefixed filenames, while this convention wants
`docs/plans/ideas/<topic>.md`.

Today that resolution is **persuasion plus repair**. Both skills load into context, the
model picks, and a repair rule ("move it and strip the date before doing anything else")
catches the misses. Cross-plugin skill precedence is undocumented in Claude Code, so there
is no arbitration to appeal to.

Measured failure rate before publishing: across 10 private repos using the convention over
several months, one leaked file and one empty stray directory. Self-repairing.

## Design / approach

`PreToolUse` on `Write`, matching a tool-specific plan path, returning deny with a
corrective message:

```
Write to docs/superpowers/specs/<dated-name>.md blocked.
This repo uses the plans lifecycle — write to docs/plans/ideas/<topic>.md instead.
```

Deny rather than `PostToolUse` relocation: moving the file behind the agent leaves a stale
path in its context, and it then edits a file that no longer exists.

Ships per agent, alongside the existing manifests — `hooks/hooks.json` for Claude,
`hooks/hooks-cursor.json` for Cursor. Codex and Gemini stay on the skill text.

## Open questions

- **Scope.** A plugin hook fires wherever the plugin is installed, and `/plugin install`
  defaults to user scope. Without a guard it would block `docs/superpowers/` writes in
  repos that never adopted this convention. Guard on `docs/plans/` existing in cwd — but
  a guard that mis-fires silently blocks real work.
- **Trust cost.** The plugin is currently two Markdown files a stranger can audit in four
  minutes. Shipping shell that runs on tool calls is a different trust category, and works
  against adoption.
- **Split behaviour.** Enforced on Claude and Cursor, advisory on Codex and Gemini. Is an
  inconsistent guarantee worse than a consistent weak one?

## Why this is parked

The failure rate does not yet justify the cost, and there is no evidence about *which*
agents leak or what the guard condition should be. Revisit when real installs report
files landing in the wrong folder.

Adding it later is additive — the per-agent manifest directories already exist, so it is a
new `hooks/` directory and a version bump, not a restructure.
