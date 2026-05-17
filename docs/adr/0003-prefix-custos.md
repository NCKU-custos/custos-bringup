# ADR 0003 — Prefix `custos` for repos and packages

**Status:** Accepted
**Date:** 2026-05-15

## Context

A consistent prefix is the cheapest disambiguation tool in a polyrepo project. It also surfaces project membership when a `custos_*` topic appears in a stranger's `ros2 topic list`.

Renaming this prefix later requires editing every `package.xml`, every `CMakeLists.txt`, every Python/C++ import, every launch file, every topic namespace, and every external integration that references our messages by name. It is the single most expensive item to change after commit 1.

## Decision

**Prefix: `custos`.**

- Repositories use hyphens: `custos-interfaces`, `custos-perception`, …
- ROS2 packages use underscores (ROS convention): `custos_common_msgs`, `custos_perception_msgs`, …
- Topics and parameters follow the package name: `/custos_perception/...`

The prefix is exactly 6 characters and is the agreed upper bound for typing comfort.

## Consequences

**Positive:**
- Easy to filter logs, topic lists, package lookups.
- Trademark and brand identity collapse to one name.

**Negative:**
- Locks the project identity to `custos`. Rebranding requires a rename pass over every repo.

## Pre-flight checks

Before pushing the org publicly, verify:
- No active trademark conflict on the name `custos` in the relevant jurisdictions.
- Domain availability (`custos.dev` or similar) and key social handles.
- No existing major open-source ROS package using the prefix.
