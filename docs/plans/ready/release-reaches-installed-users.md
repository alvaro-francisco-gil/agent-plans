# Make a release actually reach installed users

**Priority:** medium — the repo's release rule rests on an auto-update that is off by default for this marketplace; the author's own installs are three releases behind

**Goal:** Users who install from this GitHub marketplace get each version bump, so a release such as 3.0.0 reaches the people who already installed the plugin.

## Context

As of `d28d290` (2026-10-16):

- `AGENTS.md` § Releasing and `README.md` § Versioning both say Claude Code
  "auto-updates when it changes", and README § Install has no step after
  `/plugin install`. README's "Tested" note says "version bump propagates". Only
  § Troubleshooting ("An update didn't arrive") already says to run `/plugin` and update
  the marketplace by hand.
- Claude Code's docs ("Install and manage plugins" → "Keep plugins updated",
  code.claude.com/docs/en/discover-plugins) say otherwise. Auto-update is on by default
  only for the official marketplace names and for marketplaces added from claude.ai. It is
  "Off by default: every other marketplace, including the community marketplace,
  third-party marketplaces, and local development marketplaces." It is turned on per
  marketplace in `/plugin` → Marketplaces → **Enable auto-update**.
- This shows on the author's own machine. `claude plugin list` (2026-10-17) shows
  `plans-lifecycle@agent-plans` installed at **2.0.1** (user scope and four project scopes)
  and **1.0.2** (three project scopes), while `origin/main` is at **3.0.0**.
  `~/.claude/plugins/known_marketplaces.json` has `agent-plans` `lastUpdated`
  2026-09-30, so 2.1.0 (`89af41f`), 2.1.1 (`869aeed`) and 3.0.0 (`d28d290`) never
  arrived. `claude plugin details plans-lifecycle` still lists `superpowers-plans-bridge`,
  which 3.0.0 removed.
- Every one of the 8 commits that changed `skills/` bumped the version in every manifest,
  so the release discipline itself holds. The weak link is delivery.

## Design

A docs change, in three places:

1. **README § Install (Claude Code):** after the install commands, add one step: open
   `/plugin` → Marketplaces → `agent-plans` → **Enable auto-update**. Without it, run
   `/plugin` → Marketplaces → **Update marketplace** (or
   `claude plugin update plans-lifecycle@agent-plans`) to pick up a release.
2. **README § Versioning and `AGENTS.md` § Releasing:** replace "auto-updates when it
   changes" with the true statement: Claude Code updates the plugin when the version
   changes *and* the marketplace has auto-update on, which for this marketplace the user
   must turn on. The rule "bump the version in the same commit" stays as it is. README
   § Troubleshooting ("An update didn't arrive") already gives the manual update; add that
   turning on auto-update stops it recurring.
3. **README "Tested" note:** say the version-bump check was done with a manual update or
   with auto-update on, whichever it was.

If the directory submission puts the plugin in a marketplace that auto-updates by default,
the README names both install paths and says which one updates on its own.

**Alternatives considered:**
- Leave it, since the directory listing may fix it. Lost: the GitHub install path stays
  documented, and the community marketplace is also off by default.
- Release tags (`claude plugin tag` makes `plans-lifecycle--v<version>` tags; the repo
  has none) or a CHANGELOG so users notice releases. Lost as the first step: a user still
  has to update by hand. Worth doing later, as its own plan.

**Testing strategy:** the AGENTS.md definition of verified: in a clean repo, add the
marketplace, install, enable auto-update, push a version bump to a test branch or fork,
start a new session, and confirm `claude plugin list` shows the new version.

## Paths

As of 2026-10-17:
- `README.md`: § Install, § Versioning, § Troubleshooting, the "Tested" note
- `AGENTS.md`: § Releasing

## Approval

Decided 2026-10-17 (Álvaro): yes — tell users to turn on auto-update for the agent-plans marketplace, and correct the "auto-updates" claim in the README and AGENTS.md?

## Out of scope

- Updating the stale installs on this machine (a one-off, reported separately).
- Release tags, a CHANGELOG or GitHub releases.
- Codex, Cursor and Gemini update behaviour (not covered by
  `ready/verify-codex-and-cursor-packaging.md`, which checks only that those manifests
  load; a separate question once they do).
