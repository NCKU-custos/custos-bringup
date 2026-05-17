# ADR 0002 — Interfaces as a separate repo

**Status:** Accepted
**Date:** 2026-05-15

## Context

A classic ROS polyrepo failure mode: messages live inside the package that emits them. A node that subscribes to `perception/Detection` ends up pulling the entire perception package — its build deps, its third-party libs, its CI matrix — just to compile a listener. Within months, every consumer transitively depends on every producer; the dependency graph becomes a cycle in all but name.

The fix is well known and standard in mature ROS ecosystems (nav2, MoveIt, etc.): keep message definitions in a dedicated package with nothing else in it.

## Decision

`custos-interfaces` is its own repository, containing only ROS2 msg/srv/action definitions. Every other Custos package may depend on it; nothing depends on the other direction.

## Consequences

**Positive:**
- Dependency graph has a clear root. `custos-interfaces` depends only on `rcl_interfaces`, `std_msgs`, `geometry_msgs`, etc. — no Custos dependencies.
- Public CI can build `custos-perception` without pulling the proprietary `custos-novatek-sdk-wrapper`, because both depend only on `custos-interfaces`.
- A field rename forces an explicit decision: which interfaces version is correct, and what does the major bump mean? See ADR 0009 for the pre-merge integration test.

**Negative:**
- More release plumbing — every interface change is a PR + tag + downstream pin update.
- Splitting interfaces *later* is painful because every consumer's `package.xml` and every `.msg` import would need renaming. Doing it on day 1 is the only sane time.

## References

- ADR 0009 (pre-merge cross-repo CI for interfaces)
- ADR 0012 (multi-package layout inside `custos-interfaces`)
