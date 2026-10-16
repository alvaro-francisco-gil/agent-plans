# A /plans command for surveying and moving plans

**Priority:** medium

## Goal

Ship optional slash commands so the most common lifecycle moves are one step instead of
a prompt the user has to phrase well: `/plans` to survey, `/plan-promote` and
`/plan-retire` to move a plan.

## Context

The skill already describes the survey ("what's in flight, what's stale") and the moves,
but only fires when the user's wording matches its description. Comparable tools make the
moves explicit: OpenSpec ships `/opsx:*` commands, cc-sdd has one router command, and
planning-with-files has `/plan-doctor`. Commands load in Claude Code and Cowork, and in chat
a command loads as a skill, so this costs nothing on other surfaces.

## Design / approach

- `commands/plans.md`: list `ideas/`, `ready/` and `ongoing/` with each plan's Priority
  and Next line, then flag stale plans from `git log` (no commit touching the file in N
  weeks, where N comes from the repo's policy, default 4).
- `commands/plan-promote.md`: run the existing promotion steps for one named plan,
  including the ready → ongoing verification against the current code.
- `commands/plan-retire.md`: run the retirement steps: durable rationale to
  `docs/decisions/`, then `git rm` the plan.

Commands are Markdown that point back at the skill, so the skill stays the single source
of truth for the rules.

## Open questions

- Do commands duplicate the skill enough to drift? Keep each one a thin pointer.
- Codex, Cursor and Gemini handle commands differently. Ship for Claude only first?
