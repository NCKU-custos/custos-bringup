# Architecture Decision Records

Foundational decisions for the Custos drone stack. One markdown per decision, numbered sequentially. ADRs are append-only — to revise, write a new ADR that supersedes the old one and update the old one's Status to `Superseded by 00NN`.

ADRs 0001–0013 capture the locked decisions made before commit 1. They are the answer to "why is the project set up this way?" when someone joins in 6 months. 0014 onward record decisions taken as the project evolves.

## Index

| # | Title | Status |
|---|---|---|
| [0001](./0001-polyrepo.md) | Polyrepo over monorepo | Accepted |
| [0002](./0002-interfaces-as-separate-repo.md) | Interfaces as a separate repo | Accepted |
| [0003](./0003-prefix-custos.md) | Prefix `custos` for repos and packages | Accepted |
| [0004](./0004-apache-2-0.md) | Apache 2.0 license for all public repos | Accepted |
| [0005](./0005-lfs-plus-object-storage.md) | Git LFS + object storage for heavy assets | Accepted |
| [0006](./0006-trunk-based-signed-linear.md) | Trunk-based, signed commits, linear history | Accepted |
| [0007](./0007-conventional-commits-dco.md) | Conventional Commits with DCO sign-off | Accepted |
| [0008](./0008-vcstool-not-submodules.md) | vcstool for vendoring, no git submodules | Accepted |
| [0009](./0009-pre-merge-cross-repo-ci.md) | Pre-merge cross-repo CI via GitHub App | Accepted |
| [0010](./0010-novatek-isolation-contract.md) | NOVATEK SDK isolation contract | Accepted |
| [0011](./0011-ros2-lyrical.md) | ROS2 Lyrical (LTS, 2026-05-22) on Ubuntu 26.04 | Accepted |
| [0012](./0012-interfaces-multipackage.md) | Multi-package layout in `custos-interfaces` | Accepted |
| [0013](./0013-public-from-day-one.md) | Public from day 1 (except NOVATEK wrapper) | Accepted |
| [0014](./0014-triage-assistant-bot.md) | Brain-dump triage assistant bot | Accepted |
