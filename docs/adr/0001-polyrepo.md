# ADR 0001 — Polyrepo over monorepo

**Status:** Accepted
**Date:** 2026-05-15

## Context

The Custos stack spans wire-format definitions, perception, control, navigation, simulation, hardware configuration, shared infrastructure, and a proprietary wrapper around a closed third-party SDK. These layers ship at different cadences, are owned by different sub-teams, and have very different licensing and access requirements (the wrapper must stay private; everything else is open-source).

Two structural options:

- **Monorepo** — single repository, atomic refactors across boundaries, single CI graph
- **Polyrepo** — one repository per logical component, independent semver, independent access control

## Decision

Polyrepo. ~9 repositories under the `custos` GitHub org, each independently versioned.

## Consequences

**Positive:**
- Per-repo access control. The proprietary `custos-novatek-sdk-wrapper` can stay private while every other repo is public from day 1.
- Independent semver. A perception release does not force a control release.
- Clearer ownership. CODEOWNERS rules per repo align with the team boundaries.
- Faster CI for everyday changes. A typo fix in perception does not rebuild simulation.

**Negative:**
- Atomic refactors across two repos require coordinated PRs. Mitigated for the most common case (interfaces changes) by ADR 0009.
- New contributors must clone many repos. Mitigated by `custos-bringup/ros2.repos`: one clone, one `vcs import`, full workspace.
- More CI plumbing to maintain. Mitigated by reusable workflows in `custos-infra`.

## Alternatives considered

- **Monorepo with subdirectory access controls** — GitHub does not support per-directory visibility; the whole repo would have to be private, defeating the open-source goal.
- **Hybrid (one big public monorepo + private wrapper repo)** — still requires the wrapper repo split, and the monorepo would still couple unrelated cadences.

## References

- `/home/lamb/.claude/plans/repo-plan-github-wondrous-cake.md` (planning record)
