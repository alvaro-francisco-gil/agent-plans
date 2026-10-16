# Trigger and behaviour evals

**Priority:** medium

## Goal

Prove with `claude plugin eval` that the skills fire when they should and change what the
agent does, compared with a run without the plugin.

## Context

The directory doesn't check whether a plugin helps, and no comparable planning tool
publishes eval results. Evals would also catch regressions when the skill text changes.

## Design / approach

Cases worth covering first:

- "Write up an idea for X" creates `docs/plans/ideas/x.md` with no date prefix.
- Retiring a plan produces `git rm` and a `docs/decisions/` entry, not an archive move.
- An `ongoing/` plan gets `Gate` and `Next`.
- With superpowers installed and `docs/plans/` present, brainstorming output lands in
  `docs/plans/ideas/`; without `docs/plans/`, the bridge does nothing.

## Open questions

- Where do eval fixtures live so they don't ship in the plugin folder?
