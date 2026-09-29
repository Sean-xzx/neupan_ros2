# Repository guide

[English](PROJECT_GUIDE.md) · [简体中文](PROJECT_GUIDE.zh-CN.md) · [Workspace README](../README.md)

## What this repository contains

This fork is a ROS 2 workspace built around the upstream NeuPAN algorithm. It keeps the original project's license, citation, authorship, and acknowledgments. The workspace has two local ROS packages and a pinned NeuPAN source submodule.

| Path | Role | Detailed documentation |
| --- | --- | --- |
| `src/neupan_ros2/` | Python ROS 2 navigation wrapper, robot configurations, launch files, RViz | [English](../src/neupan_ros2/README.md) · [中文](../src/neupan_ros2/README_cn.md) |
| `src/ddr_minimal_sim/` | C++ differential drive simulator and scenario files | [Simulator guide](../src/ddr_minimal_sim/README.md) |
| `src/neupan_ros2/config/robots/` | Limo, Ranger, Scout, simulation, and robot template | [Configuration guide](../src/neupan_ros2/config/robots/README.md) |
| `src/NeuPAN/` | Pinned upstream algorithm source as a Git submodule | [NeuPAN upstream](https://github.com/hanruihua/NeuPAN) |
| `.github/workflows/` | ROS 2 CI | [CI notes](../.github/CI_README.md) |

The submodule is source reference material; the current setup script does not install its Python dependencies. Follow the upstream NeuPAN installation instructions for the Python environment.

## Reproduce the workspace

The documented target is Ubuntu 22.04 with ROS 2 Humble and Python 3.10. Start in the repository root.

```bash
git clone --recurse-submodules https://github.com/Sean-xzx/neupan_ros2.git
cd neupan_ros2
source /opt/ros/humble/setup.bash
./setup.sh
```

`setup.sh` installs system ROS and C++ packages. Install PyTorch, `neupan`, and the remaining Python dependencies as described in the [upstream NeuPAN project](https://github.com/hanruihua/NeuPAN); the repository README documents a typical `numpy<2.0` setup. Then build and source this workspace:

```bash
./build.sh
source install/setup.bash
ros2 launch neupan_ros2 sim_complete.launch.py sim_env_config:=scenario_corridor.yaml
```

Alternative scenarios include `scenario_empty.yaml`, `scenario_maze.yaml`, `scenario_narrow_passage.yaml`, `scenario_polygon_random.yaml`, and `scenario_u_trap.yaml`. See the simulator README for their behavior. For robot hardware, use the corresponding launch file only after its drivers, topics, frames, and robot configuration have been checked.

## Verify and maintain

```bash
colcon build --symlink-install
colcon test
colcon test-result --verbose
```

The CI workflow builds and tests on ROS 2 Humble; its style job is advisory. Local verification requires a ROS 2 environment, which cannot be inferred from a successful documentation or Git check. The working tree also contains a pinned NeuPAN submodule; clone with `--recurse-submodules` or run `git submodule update --init --recursive` after cloning.

For changes to a robot, start with `src/neupan_ros2/config/robots/<robot>/robot.yaml` and `planner.yaml`, then inspect the matching launch file. For simulator scenarios, start with `src/ddr_minimal_sim/config/`. Do not replace the original paper citation or upstream contributor credits when editing this fork.
