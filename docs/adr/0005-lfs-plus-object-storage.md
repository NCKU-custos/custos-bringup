# ADR 0005 — Git LFS + object storage for heavy assets

**Status:** Accepted
**Date:** 2026-05-15

## Context

The Custos stack will accumulate several classes of binary assets:

- Recorded flight bags (`*.bag`, `*.mcap`) — 100MB to multi-GB per recording
- ML model weights (`*.pt`, `*.onnx`, `*.engine`) — 10MB to ~1GB
- Isaac/USD simulation scenes (`*.usd*`) — 100MB to multi-GB with textures
- 3D meshes (`*.dae`, `*.stl`, `*.obj`, `*.glb`, `*.ply`) — 1KB to 100MB
- Training datasets — multi-GB to TB scale

Storing these in plain git balloons clone times and pack sizes catastrophically. Migrating files in or out of LFS later requires a history rewrite that invalidates every clone and fork.

LFS handles most of the list well, but training-dataset-class objects (TB scale) blow past LFS's per-file and bandwidth budgets in any practical hosting setup.

## Decision

Two-tier storage strategy:

1. **Git LFS** for code-adjacent binaries: meshes, models, small bags, scene files, calibration targets. Tracked via `.gitattributes` patterns committed in commit 1 of every repo.
2. **Object storage** (provider TBD — S3 / GCS / Backblaze B2 based on team's existing cloud billing) for large datasets and recorded flight bags exceeding ~500MB. Git repos hold small pointer files (`.s3-ref` or similar) listing the bucket URI and SHA256.

## Consequences

**Positive:**
- Each mechanism is used where it fits. Repos stay clone-friendly; datasets stay in proper data infrastructure.
- `.gitattributes` is committed in commit 1, so the first binary commit goes to LFS, not pack. No history rewrite ever.

**Negative:**
- Two mechanisms to teach new contributors.
- Pointer-file convention not yet standardized — see follow-ups.

## Follow-ups

- Pick object storage provider once team cloud billing is confirmed.
- Standardize pointer-file format (probably small YAML: `{bucket, key, sha256, size}`).
- Add tooling (script in `custos-infra`) to upload/download data via pointer file.
