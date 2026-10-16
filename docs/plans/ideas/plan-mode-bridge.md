# Point Claude Code's plan mode at docs/plans/ideas

**Priority:** low

## Goal

Document, and possibly automate, setting Claude Code's `plansDirectory` so plan mode
writes into `docs/plans/ideas/` instead of `~/.claude/plans/`, giving plan mode the same
single index the superpowers bridge gives superpowers.

## Context

Plan mode is the most common way Claude Code users plan, and by default its output lives
outside the repo, where nobody else can find it. The superpowers bridge already solves the
same problem for one framework. Third-party guides disagree on the `plansDirectory`
default; Anthropic's docs say `~/.claude/plans/`.

## Design / approach

Start with a README recipe: the one line of project settings. If that holds up, consider
importers that move other frameworks' spec folders (Spec Kit's `specs/NNN-*`, OpenSpec's
`changes/`) into `docs/plans/` with dates and numbers stripped.

## Open questions

- Plan mode's filenames are generated. Does the rename to a topic slug need a rule in
  the skill?
