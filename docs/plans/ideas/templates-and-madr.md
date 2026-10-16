# Templates for plans and decision records

**Priority:** low

## Goal

Give adopters a starting file for each stage, and decide whether `docs/decisions/`
should follow the MADR shape instead of the current ADR-lite one.

## Context

Every comparable tool ships templates (Spec Kit, OpenSpec, Agent OS). Today an agent
reconstructs the metadata block and section headings from the skill text each time,
which works but varies between runs. MADR is the most widely recognised decision-record
format, so using it would make `docs/decisions/` readable by existing ADR tooling.

## Design / approach

Add `skills/managing-plans-lifecycle/templates/` with `idea.md`, `ongoing.md` (including
the File Structure and Tasks sections written at start) and `decision.md`. The skill
names each template at the step that uses it.

## Open questions

- MADR adds fields (status, deciders, options considered). Is that weight worth the
  compatibility, or does it work against "small convention"?
- Templates must not become a second copy of the rules. Headings only, no prose rules.
