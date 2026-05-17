# ADR 0013 — Public from day 1 (except NOVATEK wrapper)

**Status:** Accepted
**Date:** 2026-05-15

## Context

When to flip a repo from private to public is a one-way door once forks exist. Two options:

1. **Public from day 1.** Forces good hygiene from commit 1 — no embedded secrets, no scraped code, no internal-only language. Community can watch the project evolve.
2. **Private now, public when ready.** Iterate freely, polish before flipping. Risk: long private periods accumulate habits (committed credentials, internal references, undocumented assumptions) that need cleanup before flipping. And once public, fork existence makes reversal impossible.

The Custos project is designed to be open-source from the start. Delaying that for "polish" tends to delay it indefinitely.

## Decision

**All Custos repos are public from creation**, with one exception: `custos-novatek-sdk-wrapper` is private (the closed SDK forbids redistribution, see ADR 0010).

The `custos-hardware` repo is also public, but scoped to **generic configurations only** — airframe templates, default Pixhawk parameter sets, calibration procedures. Per-physical-drone serial-specific data (calibration results, fleet identities) lives outside git.

## Consequences

**Positive:**
- Hygiene discipline starts at commit 1.
- Community visibility from the start; faster feedback.
- Trademark/branding pressure resolved immediately (no risk of someone else grabbing `custos` after months of private work).

**Negative:**
- Any secret committed in week 1 is in scraper repos forever. Mitigated by: gitleaks/trufflehog in the lint workflow; GitHub push protection enabled at the org level; pre-commit hooks for contributors.
- Public-ness from day 1 means we can't iterate on naming or repo structure without leaving forks behind. Mitigated by this plan: lock everything before commit 1.
- Per-physical-drone calibration cannot land in `custos-hardware`. Inventory/calibration tooling becomes a separate (private) follow-up.

## Pre-flight checks

Before pushing the org publicly, verify:
- GitHub secret scanning + push protection enabled at org level.
- gitleaks pre-commit hook configured in `custos-infra/lint/`.
- No secrets present in any of the scaffolded artifacts on first push.
- Trademark / domain checks complete (see ADR 0003).
