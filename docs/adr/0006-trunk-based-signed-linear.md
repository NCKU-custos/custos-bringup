# ADR 0006 — Trunk-based development, signed commits, linear history

**Status:** Accepted
**Date:** 2026-05-15

## Context

Branching and merge models that are easy on day 1 become painful to retrofit:

- Multi-version "release branches" with cherry-picks back to main encourage long-lived divergence and merge conflicts.
- Merge commits create non-linear history that's hard to bisect.
- Unsigned commits are hard to audit; backfilling signing for past commits requires history rewrite and key recovery from former contributors.

## Decision

- **Trunk-based:** `main` is the only long-lived branch. Feature work happens in short-lived branches off `main`, merged via PR.
- **Squash-merge only:** every PR becomes a single commit on `main`. Keeps history readable and bisectable.
- **Linear history required** at the branch protection level. No merge commits on `main`.
- **Signed commits required.** Every commit on `main` must carry a verified signature. Contributors set up SSH or GPG signing as part of onboarding.
- **No force push to `main`** under any circumstance.
- **1 approving review required**, CODEOWNERS approval required. Will adjust as the team grows.
- **All required status checks must pass** before merge.

## Consequences

**Positive:**
- Clean, linear, signed history. `git bisect` works trivially.
- Identity of every change is provable. Critical for a project that may ship to safety-relevant contexts (drones).
- Squash policy means PR titles are the canonical record. Conventional Commits in PR titles drive release-please.

**Negative:**
- Long-running feature work has to be carefully managed (rebase, not merge, against `main`). Documented in `CONTRIBUTING.md`.
- Signing setup is a small onboarding hurdle. Documented in `CONTRIBUTING.md`.
