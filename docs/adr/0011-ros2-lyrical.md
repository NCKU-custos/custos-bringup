# ADR 0011 — ROS2 Lyrical on Ubuntu 26.04 (Tier 1)

**Status:** Accepted
**Date:** 2026-05-15

## Context

ROS2 distribution choice locks the ABI, the supported language standards (C++ version, Python version), the apt sources, the rosdep keys, every Docker base image, and the support window. Switching distros mid-project is a multi-week chore: every CMakeLists.txt, every package.xml minimum, every CI matrix entry, every devcontainer needs updating, and every developer needs to re-source.

Available distributions at decision time (2026-05-15):

- **Jazzy** — LTS, released May 2024, supported through May 2029. Ubuntu 24.04. Mature.
- **Lyrical** — LTS, formal release 2026-05-22 (1 week from this ADR). Ubuntu 26.04 Tier 1; Ubuntu 24.04 Tier 3. Supported through May 2031.
- **Rolling** — development trunk. Breaks regularly.

The project is greenfield with a multi-year horizon. Picking the **newest LTS** maximizes the support window. The drawback is that Lyrical and Ubuntu 26.04 are both day-1 fresh; first-month distro packages typically have rough edges.

## Decision

**ROS2 Lyrical** (LTS) on **Ubuntu 26.04** (Tier 1).

CI builds also exercise Ubuntu 24.04 (Tier 3) as a fallback during the early-life window. The Tier 3 job is marked `continue-on-error` during the first 4–6 weeks while distro packages stabilize, then promoted to required if stable, or dropped if Lyrical proves solid on Tier 1.

## Consequences

**Positive:**
- Maximum support window — Lyrical EOL is May 2031.
- New project means no migration cost; just start on the right distro.
- Tier 1 OS support means full distro package coverage from upstream.

**Negative:**
- Day-1 risk: Lyrical packages may have edge cases discovered only after wide adoption. Mitigated by the Tier 3 fallback build.
- The NOVATEK SDK was likely built against an older glibc/libstdc++. If vendor only ships for Ubuntu 22.04 or 24.04, the wrapper repo may need to target Tier 3 base or ship a compatibility shim. **This must be verified before locking the wrapper's Docker base image.**

## Follow-ups

- Verify NOVATEK SDK Ubuntu support matrix.
- After 6 weeks on Lyrical, decide whether Tier 3 stays in CI.
