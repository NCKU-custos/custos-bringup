# ADR 0007 — Conventional Commits + DCO sign-off

**Status:** Accepted
**Date:** 2026-05-15

## Context

Two orthogonal disciplines, both expensive to retrofit:

1. **Commit message convention** — drives automated changelog generation and semver bumps. Conventional Commits (`feat:`, `fix:`, `breaking:`, etc.) is the de facto standard and the input format release-please understands. Retrofitting after months of free-form messages is impractical.
2. **Contribution attestation** — every contributor needs to assert they have the right to contribute the code. Two options: DCO (Developer Certificate of Origin, sign-off-by line in commit message) or CLA (signed agreement, often via a bot). DCO is lighter weight, no per-contributor signature management, sufficient for Apache 2.0 outbound licensing.

## Decision

**Conventional Commits format on PR titles**, since squash-merge (ADR 0006) makes the PR title the single commit message on `main`. Commit body free-form. Enforced by a CI check in every repo.

**DCO sign-off required** on all commits via `git commit -s`. Enforced by the DCO probot or a workflow check on every repo.

## Consequences

**Positive:**
- release-please works out of the box.
- Changelog is automatic per release.
- DCO is a one-line burden per commit, no centralized contributor agreement to manage.

**Negative:**
- Contributors who don't know Conventional Commits get blocked on first PR. Mitigated by `CONTRIBUTING.md` and a friendly CI error message.
- DCO does not give the project a path to relicense the code without re-soliciting every contributor. Acceptable: Apache 2.0 is not expected to change.

## Follow-ups

- If outside corporate contributions become substantial and relicensing flexibility matters, revisit moving to a CLA. Switching DCO → CLA later requires re-signing.
