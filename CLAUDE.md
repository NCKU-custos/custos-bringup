# custos-bringup — CLAUDE.md

System bringup, the workspace manifest, the devcontainer, and the ADR log for the Custos drone stack. **Meta repo** — no ROS package of its own.

Workspace-wide rules and state caveats live at `../CLAUDE.md` (= `/space/drone/CLAUDE.md`). Read it before doing anything cross-repo. If you're reading this in a standalone clone, the upward pointer dangles — the locked decisions are also captured under `docs/adr/`.

## State of this repo

- `ros2.repos` pins every consumer repo to `v0.1.0`. **None of those tags exist yet.** `vcs import` will fail until the first releases land.
- `.devcontainer/devcontainer.json` references `ghcr.io/ncku-custos/ros-base:lyrical-26.04`, which is **not yet pushed to GHCR**. "Reopen in Container" will fail; build the image locally first from `custos-infra/docker/base/Dockerfile`.
- `docs/adr/0001-polyrepo.md` through `docs/adr/0013-public-from-day-one.md` are present and complete.
- `launch/` exists but is empty — system-composition launch files are the future tenants.
- `release-please-config.json` + `.release-please-manifest.json` are in single-package mode (this repo's own version).
- `.gitattributes` declares the standard LFS patterns (bags, USD, meshes, weights). The same patterns are duplicated across every Custos repo — keep them in sync if you edit.

## Cross-repo edges

- **Depends on:** nothing.
- **Depended on by:** every contributor and CI job (via `ros2.repos`). When you bump a pin here, you are shipping a new "tested-together" version of the whole stack.
- **Not** a `colcon` package — no `package.xml`, no build target.

## Repo-specific hard rules

- **`ros2.repos` is the source of truth for which versions ship together.** Bumping a pin is a coordinated cross-repo activity: the consumer's release lands first, then `release-please` opens a `chore: release` PR in the consumer, then this file is updated. A major bump in `custos-interfaces` requires every consumer pin to bump in the same merge cycle (ADR 0009).
- **Never edit an existing ADR in place.** ADRs are immutable once accepted. To change a decision, add a new ADR (`docs/adr/NNNN-...md`) that supersedes the old one, and mark the old one's `Status:` as `Superseded by NNNN`. Numbering is monotonic.
- **System-composition launch files belong in `launch/`** (this repo), not in individual product repos. Per-package launch files (e.g., launching only the planner) belong in that package.
- **The devcontainer is the canonical dev environment** (ADR 0011: Ubuntu 26.04 Tier 1). If you change Ubuntu version, ROS distro, or RMW implementation, expect a multi-repo coordination chore; capture it in an ADR.
- **Don't restate `.github/CONTRIBUTING.md`.** DCO, Conventional Commits, signed commits, branch naming all live there. Cite it instead of repeating it.

## Build / test cheat sheet

There is nothing to `colcon build` in this repo. The interesting commands operate on the *workspace* materialized from `ros2.repos`:

```bash
# Materialize the full workspace into src/ (will fail today; tags don't exist).
mkdir -p src
vcs import src < ros2.repos

# Resolve system deps (apt, pip, etc.) referenced by every package.xml under src/.
rosdep update
rosdep install --from-paths src --ignore-src -r -y

# Build the whole tree.
colcon build --symlink-install
colcon test

# Devcontainer (will fail until the GHCR image is pushed).
devcontainer up --workspace-folder .
```

Validation that a manifest bump did not break anything: run the full `vcs import → colcon build → colcon test` cycle, not just the bumped repo's own tests.

## Pointers specific to this repo

- ADRs: `docs/adr/` — start with `0001-polyrepo.md`; the README.md in that dir is an index.
- Manifest: `ros2.repos`
- Devcontainer: `.devcontainer/devcontainer.json`
- LFS patterns: `.gitattributes` (canonical copy — others mirror)

> TODO(post-first-release): the `vcs import` workflow becomes real once `v0.1.0` tags exist in every repo. Update the "will fail today" notes above when that happens.
> TODO(post-GHCR-push): drop the devcontainer caveat once `ghcr.io/ncku-custos/ros-base:lyrical-26.04` is pushed.
> TODO(post-first-commit): write `ONBOARDING.md` (human onboarding) and `OVERVIEW.md` (architecture for newcomers) — both planned, both out of scope right now.
> TODO(post-active): document the manifest-bump bot recipe (a workflow that watches consumer releases and opens a PR here). Until then, manifest bumps are manual.
