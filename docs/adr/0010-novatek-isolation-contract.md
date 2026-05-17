# ADR 0010 — NOVATEK SDK isolation contract

**Status:** Accepted
**Date:** 2026-05-15

## Context

The project's most sensitive constraint: the NOVATEK SDK is closed-source, redistribution-prohibited, and must stay behind an access wall. The wrapper repo (`custos-novatek-sdk-wrapper`) is private and only org members with the right team membership can clone it.

The risk is **leakage of dependency**. If public Custos code ends up importing anything from the wrapper repo — even a transitive type, even a header — then the public stack cannot be built without org access, which defeats the entire open-source goal.

The classic failure mode: a perception node convenience function creeps into the wrapper, then a public node imports the wrapper "just for that function," and now the public CI can't build.

## Decision

The wrapper's **entire external surface** is expressed in `custos-interfaces`. Public Custos code never references the wrapper repo directly. The surface is:

- **Topics:** sensor data published with `custos_perception_msgs` types
- **Services:** calibration trigger, mode switch, etc., with `custos_perception_msgs` srv types
- **Parameters:** standard ROS2 parameters (exposure, frame rate, modes)
- **Lifecycle:** `lifecycle_msgs/State` transitions
- **Diagnostics:** `/diagnostics` topic with `diagnostic_msgs/DiagnosticArray`

Public CI uses a **mock node** (lives in `custos-perception` or `custos-infra`) that satisfies the exact same contract. Public builds succeed without the wrapper.

Any wrapper feature that would force a public dependency is a contract regression. It requires a new ADR before it can be added.

## Consequences

**Positive:**
- Public repos are buildable by anyone, anywhere, with no special access.
- The wrapper can be open-sourced later (if NOVATEK terms ever change) without rewriting the consumer.
- Mock node provides a reference implementation for sensor stubbing in tests.

**Negative:**
- Every wrapper feature that exposes new functionality has to be modeled in `custos-interfaces` first. Slight extra design cost upfront.
- The mock has to be maintained as the contract evolves. Mitigated by treating mock + wrapper as having a shared CI matrix.

## References

- ADR 0002 (interfaces as a separate repo)
- ADR 0013 (public from day 1)
