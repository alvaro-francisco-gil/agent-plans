# Verify the Codex and Cursor packaging

**Priority:** medium
**Gate:** none
**Next:** install the plugin in Cursor and check the skill fires on "where should this plan live?" in a scratch repo with `docs/plans/` (Codex verified 2026-10-20)

## Goal

Confirm that `.codex-plugin/plugin.json` and `.cursor-plugin/plugin.json` actually load
`skills/` in Codex and Cursor, rather than merely being well-formed JSON.

## Context

The repo claims to package the plans lifecycle for Claude Code, Codex, Cursor and Gemini.
Only the Claude Code path has been exercised end to end: clean clone → marketplace resolves
→ `claude plugin install` → the skill parses → a version bump propagates to an installed
copy.

The other three manifests were written by copying the shape superpowers uses — a real
published plugin, so it is good evidence, but it is not verification. `scripts/validate.py`
checks they are valid JSON with a name, a description and a matching version. Nothing has
confirmed that either tool reads `"skills": "./skills/"` the way Claude Code reads its own
manifest, or that a `SKILL.md` written for Claude loads unmodified elsewhere.

Until that is done, the README's platform claim is inferred, not tested.

## Design / approach

Install the plugin in each tool and observe the skill firing on a prompt it should match —
"where should this plan live?" — in a scratch repo containing `docs/plans/`.

Gemini is the odd one out: `gemini-extension.json` plus a `GEMINI.md` that `@`-includes the
skill is a different mechanism from a skills directory, so it needs its own check.

If a platform turns out not to work, the honest fix is to narrow the README's claim rather
than leave it implied.

## File structure

- `.codex-plugin/plugin.json` — verify, correct if the schema differs
- `.cursor-plugin/plugin.json` — verify, correct if the schema differs
- `gemini-extension.json` + `GEMINI.md` — verify the `@`-include resolves
- `README.md` — narrow the platform claim for anything that does not work
- `scripts/validate.py` — extend if a platform needs a structural check we can automate

## Tasks

### Codex
- [x] Install the plugin from this repo in Codex — `codex plugin marketplace add
      alvaro-francisco-gil/agent-plans` reads `.claude-plugin/marketplace.json` directly;
      `codex plugin add plans-lifecycle@agent-plans` installs 3.0.0 (codex-cli 0.153.0)
- [x] Confirm the skill is discovered, with its name and description intact — listed as
      `plans-lifecycle:managing-plans-lifecycle`
- [x] Confirm `managing-plans-lifecycle` fires unprompted — "write this idea down as a
      plan" produced `docs/plans/ideas/dark-mode.md` with the `Priority` header and template
      sections, the skill never named
- [ ] Confirm the `interface` block renders as intended — needs the Codex app or the OpenAI
      directory listing; the CLI shows no plugin card

### Cursor
- [ ] Install the plugin from this repo in Cursor
- [ ] Confirm the skill is discovered
- [ ] Confirm the skill fires on the same prompt

### Gemini
- [ ] Confirm `gemini-extension.json` is picked up
- [ ] Confirm `GEMINI.md`'s `@./skills/...` include resolves to the full skill

### Close out
- [ ] Narrow the README for any platform that does not work
- [ ] Add whatever structural check `validate.py` can automate
- [ ] Note in the README which platforms are tested and which are inferred

## Out of scope

The "Plans written by other tools" rule on non-Claude platforms. superpowers ships its own
Codex and Cursor manifests, but whether its skills conflict there in the same way is a
separate question from whether our packaging loads at all.
