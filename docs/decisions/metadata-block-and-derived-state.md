# One metadata block; state is derived, not written

## Context

v1 asked for a hand-written `## Status` section with `Updated`, `Stage`, `Done`, `Next`,
`Blockers` and `Handoff`, and v1.0.4 added `Due`, `Blocked-by` and `Waiting-on`. In
practice, the largest adopting repo (160 plans) found the Status header in only 2 of its
23 `ongoing/` plans. A cross-repo scanner built on `Updated` then showed the
other failure: a mechanical commit (a migration, a rename) made stalled plans look fresh.

## Decision

Adopt the model that repo had already moved to:
- a block of `Priority` / `Gate` / `Next` (plus `Landed` where a repo deploys, and `Due`
  for real deadlines) between the title and the first `## `;
- one typed `Gate` for every kind of wait;
- the last touch derived from git, walking past sweep commits (more than 4 plan files
  and nothing shipped, or more than 8 if code shipped).

## Rejected alternatives

- **Keep `Updated` and trust it.** It rots first, and trusting it is what let a
  migration hide stalled plans.
- **Separate `Blocked-by` / `Waiting-on` keys.** Three places to say one thing.
- **A `waiting/` folder.** A condition is not a stage; moving files breaks every link.

## What this binds

Any tool that reads plans must read the block from the preamble only, and must derive
freshness rather than read it. A migration must land as one commit per repo.

## Revisit when

A repo needs a state the five gate forms cannot express, or the sweep thresholds
misclassify a real commit in practice.
