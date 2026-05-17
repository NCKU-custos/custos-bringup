# ADR 0012 — Multi-package layout inside `custos-interfaces`

**Status:** Accepted
**Date:** 2026-05-15

## Context

`custos-interfaces` is its own repo (ADR 0002), but within the repo there are two layouts to choose between:

- **Single package** (`custos_interfaces`) — one `package.xml`, all msg/srv/action files inside. Simpler.
- **Multiple packages by domain** — one package per logical area (`custos_common_msgs`, `custos_perception_msgs`, `custos_control_msgs`, `custos_navigation_msgs`).

The choice is hard to reverse: splitting a single package later means renaming every `<depend>` in every consumer's `package.xml`, every C++ `#include`, every Python import. Exactly the pain-to-change scenario this project is trying to avoid.

## Decision

**Four packages by domain:**

| Package | Scope |
|---|---|
| `custos_common_msgs` | Shared header extensions, enums, generic types |
| `custos_perception_msgs` | Vision pipeline outputs, detections, tracked features |
| `custos_control_msgs` | Pixhawk bridge surface, mission state, flight commands |
| `custos_navigation_msgs` | SLAM/VIO output, planning, trajectory representation |

`custos_common_msgs` may be depended on by the other three; the other three do not depend on each other. release-please runs in monorepo manifest mode with one version line per package.

## Consequences

**Positive:**
- Consumer build sizes are smaller — a perception-only consumer pulls only `custos_perception_msgs` (and `custos_common_msgs`).
- CI builds for interface changes only rebuild affected packages.
- Ownership boundaries align with team boundaries (perception team owns `custos_perception_msgs`, flight team owns the other two domain packages, core owns common).

**Negative:**
- More `package.xml` and `CMakeLists.txt` boilerplate to maintain.
- Cross-domain types (e.g., a "detection from a planned trajectory") need to live in `custos_common_msgs` or accept an explicit cross-package dependency.

## References

- ADR 0002 (interfaces as a separate repo)
- `custos-interfaces/release-please-config.json` (release configuration)
