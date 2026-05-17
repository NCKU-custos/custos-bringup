# custos-bringup

System launch composition, workspace manifest, and architecture docs for the Custos drone stack.

This is the **entry point for new contributors**. If you only clone one repo, clone this one.

## Quick start

```bash
git clone https://github.com/NCKU-custos/custos-bringup.git
cd custos-bringup
vcs import < ros2.repos
colcon build
```

## Repos in the stack

See [`ros2.repos`](./ros2.repos) for the authoritative manifest. Short version:

| Repo | Role |
|---|---|
| [`custos-interfaces`](https://github.com/NCKU-custos/custos-interfaces) | ROS2 msg/srv/action definitions |
| [`custos-infra`](https://github.com/NCKU-custos/custos-infra) | Shared CI workflows, Docker bases, lint configs |
| [`custos-perception`](https://github.com/NCKU-custos/custos-perception) | Vision pipelines, sensor fusion |
| [`custos-control`](https://github.com/NCKU-custos/custos-control) | Pixhawk bridge, mission management |
| [`custos-navigation`](https://github.com/NCKU-custos/custos-navigation) | SLAM/VIO, planning |
| [`custos-simulation`](https://github.com/NCKU-custos/custos-simulation) | Gazebo worlds, Isaac scenes, URDF/SDF |
| [`custos-hardware`](https://github.com/NCKU-custos/custos-hardware) | Generic Pixhawk params, calibration procedures, airframe templates |
| `custos-novatek-sdk-wrapper` | **private.** ROS2 wrapper around closed NOVATEK SDK. Publishes only standard `custos-interfaces` messages. |

## Architecture decisions

All foundational decisions are documented as ADRs in [`docs/adr/`](./docs/adr/). Start with [ADR 0001](./docs/adr/0001-polyrepo.md).

## ROS2 distribution

Targets ROS2 **Lyrical** (LTS, released 2026-05-22) on Ubuntu 26.04 (Tier 1). Tier 3 (Ubuntu 24.04) is supported in CI during the early-life window of Lyrical.

## License

Apache License 2.0. See [LICENSE](./LICENSE).
