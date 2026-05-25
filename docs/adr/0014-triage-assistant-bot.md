# ADR 0014 — Brain-dump triage assistant bot

**Status:** Accepted
**Date:** 2026-05-23

## Context

The brain-dump intake (the `[brain dump]` issue template + `triage` label) created a bucket for half-formed ideas, but the bucket is inert: a human must read each dump, judge whether it is feasible and which repo it belongs in, and then turn the worthwhile ones into PRs. We want Claude Code to do the first pass — (1) validate intent + feasibility against the stack's rules, and (2) optionally draft a small PR.

Constraints that shaped the design:

1. **Comment-only needs no GitHub App** — the built-in `GITHUB_TOKEN` + `issues: write` can post. Drafting code, however, needs `contents: write` + `pull_requests: write`.
2. **`custos-ci` cannot draft PRs.** Per ADR 0009 it is `contents: read` + `actions: write` only. Widening it would amend 0009 and conflate "cross-repo CI dispatch" with "writes code on my behalf."
3. **`claude-code-action` never opens PRs itself** — it pushes a branch and emits a pre-filled link. A draft PR therefore requires the *workflow* to run `gh pr create --draft` from the branch the action reports.
4. **Issue bodies are attacker-influenceable** on public repos (ADR 0013) → prompt-injection risk; and Anthropic token spend must be capped.

## Decision

A **two-phase triage bot**. Logic lives in reusable workflows in `custos-infra`; each repo has a thin `triage-assist.yml` caller (`on: issues`). Authentication is the org's Claude **subscription** token (`CLAUDE_CODE_OAUTH_TOKEN`); the API-key input is also wired as an alternative.

**Phase 1 — feasibility comment (live).**
- Fires when a `[brain dump]`-titled issue is **opened by an org insider** (`author_association ∈ {OWNER, MEMBER, COLLABORATOR}`). The action *additionally* checks the actor's repo write access.
- Token: inherited `GITHUB_TOKEN`, scoped `contents: read` + `issues: write`. No App.
- **Sandboxed:** `Edit`, `Write`, `Bash`, `NotebookEdit` are disallowed, and the comment is posted by the action itself (`track_progress: true`), not by Claude through a shell — so an injected command cannot exfiltrate the token.
- The run fetches the stack ADRs from `custos-bringup` into context, so feasibility is judged against the real rules, not just the local `CLAUDE.md`.

**Phase 2 — draft PR (dormant until the App exists).**
- Fires when a maintainer applies the **`draft-it`** label (labeling needs write — an explicit per-issue opt-in).
- Token: a **dedicated `custos-triage-bot` GitHub App** (`contents` + `pull_requests` + `issues: write`), installed on **public repos only — never the NOVATEK wrapper** (ADR 0010). `use_commit_signing: true` → commits show **Verified** (ADR 0006); the **DCO** `Signed-off-by:` trailer (ADR 0007) is baked into the commit message via the prompt. The workflow opens the **draft** PR via `gh pr create --draft`.

A **dedicated App** (not `custos-ci`, not the official Claude GitHub app) keeps least privilege, gives a clean signing/DCO identity, and avoids amending ADR 0009.

**Two human gates** protect the write path: the `draft-it` opt-in, and CODEOWNERS review at merge (draft PRs cannot self-merge).

## Consequences

**Positive:**
- Brain dumps get an instant, rule-aware first pass; the worthwhile ones can become draft PRs on demand.
- Phase 1 is fully sandboxed and uses a read-only token; Phase 2 uses a least-privilege App scoped to public repos, with the NOVATEK wrapper excluded by construction.
- Reusables centralize the prompt/guardrails so per-repo callers cannot drift.

**Negative:**
- The insider gate relies on **public org membership** — GitHub reports `author_association: NONE` for members whose membership is *private*, so their dumps would be silently skipped. Accepted for a small, trusted group (each makes membership public). Alternative, if needed later: drop `author_association` and gate solely on the action's write-access check, which is immune to membership visibility.
- Real cost is Anthropic token spend, capped by `--max-turns`, the insider gate, and per-issue concurrency. Run failures are silent at this stage.
- Model compliance with the DCO / NOVATEK / path rules is non-deterministic; the tool allowlist + read-only token (Phase 1) and CODEOWNERS review (Phase 2) are the hard backstops.
- That a standard DCO check accepts the App bot's noreply email is **unproven** until Phase 2 first runs; fallback is to exempt the bot in the DCO app.
- Phase 2 is deliberately ahead of the code it would edit (every `src/` is still empty scaffold), so it ships dormant.

## Rollout status (2026-05-23)

- **Phase 1 is live on all public repos** (callers merged) and verified end-to-end on `custos-hardware` — a rule-tripping dump ("store per-drone IMU calibration results here") was correctly flagged out-of-scope against this repo's "one rule" and ADR 0013.
- **Phase 2 is dormant.** To activate: create the `custos-triage-bot` App, add `CUSTOS_TRIAGE_APP_ID` / `CUSTOS_TRIAGE_PRIVATE_KEY` secrets, set `bot_id` in the callers, create the `draft-it` label per repo, and confirm the DCO check passes on the bot's first PR.
