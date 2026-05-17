# ADR 0009 — Pre-merge cross-repo CI via GitHub App

**Status:** Accepted
**Date:** 2026-05-15

## Context

The polyrepo Achilles heel: when `custos-interfaces` lands a breaking message change, consumer repos can silently break. Three options:

1. **Pre-merge integration:** the interfaces PR triggers builds in every consumer repo against the PR's ref. Merge is blocked if any consumer fails.
2. **Post-merge nightly:** consumers run nightly CI against latest interfaces. `main` stays broken until someone notices.
3. **Manual coordination:** humans coordinate before merging. Works at 2 contributors, fails at 5+.

Setting up pre-merge integration after the fact requires GitHub App registration, org secrets, reusable workflow plumbing in every repo, and per-repo workflow_dispatch wiring. None of that is hard, but all of it is harder once there are dozens of existing PRs and active developers expecting CI to behave a certain way.

## Decision

**Pre-merge integration via a GitHub App** named `custos-ci`.

- App installed org-wide with `contents:read` + `actions:write`.
- App ID and private key stored as org-level secrets (`CUSTOS_CI_APP_ID`, `CUSTOS_CI_PRIVATE_KEY`).
- The `interfaces-consumers.yml` reusable workflow in `custos-infra` is called by `custos-interfaces` PR builds. It dispatches a build in every consumer repo against the PR's commit SHA. All consumers must build clean for the interfaces PR to merge.

## Consequences

**Positive:**
- Breakage caught before merge. `main` of every repo stays buildable.
- Identity is an App, not an individual PAT — survives team offboarding without rotation toil.
- The dispatch mechanism (reusable workflow) is one place to update if the strategy evolves.

**Negative:**
- CI runtime for an interfaces PR is the slowest consumer's build time. Acceptable: interfaces changes are rare; correctness > speed.
- The App must be created and the org secrets populated before the first interfaces PR. Documented in repo setup steps.

## References

- ADR 0002 (why interfaces is its own repo — sets up the consumer matrix)
- `custos-infra/.github/workflows/interfaces-consumers.yml`
