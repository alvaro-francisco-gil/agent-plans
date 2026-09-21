# AGENTS.md

Instructions for anyone — human or agent — working in this repo.

## What this repo is

`agent-plans` packages the `docs/plans` lifecycle convention as a skill, for Claude Code,
Codex, Cursor, Gemini, and anything else that can read a `SKILL.md`.

**This repo uses the convention it ships.** Plans live in `docs/plans/`, follow the stages
in [skills/managing-plans-lifecycle/SKILL.md](skills/managing-plans-lifecycle/SKILL.md),
and are deleted when the work is verified. If working here feels inconsistent with that
skill, the skill is what's wrong — fix it, and say so in the commit.

## Per-repo policy

The skill defers these to the adopting repo. This repo's answers:

- **Priority label: required.** Every file under `docs/plans/` declares exactly one of
  `low`, `medium`, `high`.
- **Stages used:** `ideas/`, `ready/`, `ongoing/`, and `docs/decisions/`.
- **No `ongoing/soak/`, no `docs/incidents/`, no `docs/ops/`.** Nothing here deploys, so
  there is no soak window and no operational surface.
- **"Verified" means** the skill has been installed from this marketplace into a clean repo
  and the described behaviour observed — not that the Markdown reads correctly.

## Single source of truth

`skills/` is the only copy of any skill. Every per-agent manifest
(`.claude-plugin/`, `.codex-plugin/`, `.cursor-plugin/`, `gemini-extension.json`) points at
it rather than holding its own copy. **Never duplicate a `SKILL.md`** — a second copy is
drift waiting to happen, which is the failure this packaging exists to prevent.

## Releasing

Claude Code pins an installed plugin to the `version` in `.claude-plugin/plugin.json` and
auto-updates when it changes. **A content change that does not bump that version never
reaches existing users.** Bump it in the same commit as the content, and keep the version
identical across every per-agent manifest.

Renames go in the `renames` map in `.claude-plugin/marketplace.json`, which is
**append-only** — old entries stay forever so migration chains keep resolving.

## Writing style for skills

Skills here are read by agents, in context, competing for attention with everything else
loaded. So:

- The `description` is the trigger. It decides whether the skill loads at all. Spend it on
  when to use the skill, not on caveats.
- State the rule, then the anti-pattern. Agents follow "never do X" better than prose.
- No repo-specific assumptions. If a line only makes sense in the author's repos, it is a
  bug — this ships to strangers.
