# Point Claude Code's plan mode at docs/plans/ideas

**Priority:** low

## Goal

Go one step beyond the skill's "Plans written by other tools" rule, which moves a plan-mode
file after the fact: document, and possibly automate, setting Claude Code's
`plansDirectory` so plan mode writes into `docs/plans/ideas/` in the first place.

## Context

Plan mode is the most common way Claude Code users plan, and by default its output lives in
`~/.claude/plans/`, outside the repo. Since 3.0.0 the skill moves such files into
`docs/plans/ideas/`, but only when it notices them. Third-party guides disagree on the
`plansDirectory` default; Anthropic's docs say `~/.claude/plans/`.

## Design / approach

Start with a README recipe: the one line of project settings. If that holds up, consider
importers that move other frameworks' spec folders (Spec Kit's `specs/NNN-*`, OpenSpec's
`changes/`) into `docs/plans/` with dates and numbers stripped.

## Open questions

- Plan mode's filenames are generated. Is "rename to a topic slug" in the skill enough, or
  does the recipe need its own rule?
