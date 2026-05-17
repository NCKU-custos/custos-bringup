# ADR 0008 — vcstool for vendoring, no git submodules

**Status:** Accepted
**Date:** 2026-05-15

## Context

A polyrepo workspace needs a mechanism to clone all member repos at consistent versions. Two candidates:

- **git submodules** — built into git, ubiquitous, but notorious in mixed-toolchain environments. They fight colcon's workspace assumptions (colcon expects `src/<package>/...`, submodules want to nest), confuse new contributors, and create surprising states on branch switches.
- **vcstool** (`vcs import < ros2.repos`) — the standard answer in the ROS2 ecosystem. YAML manifest of repo URLs and refs. Plays cleanly with colcon. Adopted by every major ROS2 project (nav2, MoveIt, autoware).

## Decision

**vcstool exclusively.** `custos-bringup/ros2.repos` is the single source of truth for which repos and refs make up a working Custos workspace. Submodules are not used anywhere.

## Consequences

**Positive:**
- Matches ROS2 ecosystem norms; new contributors familiar with ROS2 land in a familiar workflow.
- One-line workspace materialization: `vcs import src < ros2.repos`.
- Mixing public + private repos works naturally; auth is handled by the user's git credentials.

**Negative:**
- vcstool is a Python tool; it must be installed. Bundled in the devcontainer.
- Updating `ros2.repos` is a manual step when consumer repos release new versions. Automation is a follow-up (a workflow that watches releases and opens a bump PR).
