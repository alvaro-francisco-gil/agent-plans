# Trigger and behaviour evaluations

**Priority:** medium

## Goal

Prove with Claude Code's plugin evaluation runner that the skills fire when they should and change what the
agent does, compared with a run without the plugin.

## Context

The directory doesn't check whether a plugin helps, and no comparable planning tool
publishes evaluation results. Evaluations would also catch regressions when the skill text changes.

## Design / approach

Cases worth covering first:

- "Write up an idea for X" creates `docs/plans/ideas/x.md` with no date prefix.
- Retiring a plan produces `git rm` and a `docs/decisions/` entry, not an archive move.
- An `ongoing/` plan gets `Gate` and `Next`.
- With superpowers installed and `docs/plans/` present, brainstorming output lands in
  `docs/plans/ideas/`; plan mode output is moved there too.

## Open questions

- Where do evaluation fixtures live so they don't ship in the plugin folder?
