# Remove scripts/validate.py before the next release

**Priority:** high — the next version of Plans Lifecycle will be refused by Anthropic's plugin directory while the repo holds a script
**Landed:** none
**Gate:** none
**Next:** verify the plan against main, then build it (workspace worker, 2026-11-20); the release and directory resubmission stay Álvaro's

**Goal:** Keep Plans Lifecycle publishable in Anthropic's plugin directory. The live 3.0.0
(`ae42355`) is unaffected; the rule applies to every new version.

## Context

On 2026-11-06 the directory refused System of Record (sibling repo `agent-record`) under
this rule: *"The directory accepts scripts, programs, and files outside the allowed types
only from Verified organizations."* Allowed: Markdown, text, JSON, YAML, PNG/JPEG/GIF/WebP/ICO.
Also refused, whatever the name: any file starting with `#!`, and package-manager config
files. Text must be UTF-8 with no NUL bytes. A license, readme, `.gitignore` and the icon may
stay. "Verified" means Anthropic knows the organization is a real company, which a personal
account isn't. The directory unpacks the whole repo, so dev tooling counts.

In this repo the one offending file is `scripts/validate.py` (starts with `#!`). Nothing
ships it to users; only CI runs it. History:
`~/githubs/_workspace/docs/plans/ongoing/plugin-directory-listing.md`.

## Design

- Move what `validate.py` checks into `.github/workflows/validate.yml` as short steps:
  `claude plugin validate .` if the CLI can run in CI (check first), otherwise `jq`/`yq`
  lines for JSON validity, frontmatter `name` matching its directory, and the `version`
  keys agreeing. A long program inside the YAML would be the same dodge as a script in
  Markdown, so keep each step to a line or two.
- Delete `scripts/validate.py` and update its mentions: `README.md` (~line 63) and
  `docs/plans/ready/verify-codex-and-cursor-packaging.md` (lines 20, 44, 71).
- Add to `AGENTS.md`: no file in this repo may be anything but Markdown, text, JSON, YAML or
  an allowed image, and none may start with `#!`, with the reason.
- Ship it with the next content release; it needs no release of its own.

## Acceptance

- `git ls-files` lists nothing outside the allowed types except `LICENSE` and
  `.gitignore`, and no tracked file starts with `#!`.
- CI still fails on a broken manifest or a version mismatch.
