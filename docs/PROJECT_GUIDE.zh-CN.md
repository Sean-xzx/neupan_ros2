# 仓库使用指南

[简体中文](PROJECT_GUIDE.zh-CN.md) · [English](PROJECT_GUIDE.md) · [工作空间说明](../README_CN.md)

## 仓库内容

这个派生仓库以原始 NeuPAN 算法为基础，保留原项目的许可证、论文引用、作者和致谢。工作空间包含两个本地 ROS 2 软件包，以及固定版本的 NeuPAN 源码子模块。

| 路径 | 用途 | 详细文档 |
| --- | --- | --- |
| `src/neupan_ros2/` | Python ROS 2 导航封装、机器人配置、启动文件和 RViz | [English](../src/neupan_ros2/README.md) · [中文](../src/neupan_ros2/README_cn.md) |
| `src/ddr_minimal_sim/` | C++ 差速机器人仿真器和场景 | [仿真器说明](../src/ddr_minimal_sim/README.md) |
| `src/neupan_ros2/config/robots/` | Limo、Ranger、Scout、仿真及机器人模板 | [配置说明](../src/neupan_ros2/config/robots/README.md) |
| `src/NeuPAN/` | 固定版本的上游算法源码 Git 子模块 | [NeuPAN 上游](https://github.com/hanruihua/NeuPAN) |
| `.github/workflows/` | ROS 2 持续集成 | [CI 说明](../.github/CI_README.md) |

子模块用于保存对应版本的源码；现有 `setup.sh` 不安装 NeuPAN 的 Python 依赖。Python 环境请按上游 NeuPAN 的说明安装。

## 复现工作空间

文档所用的目标环境是 Ubuntu 22.04、ROS 2 Humble 和 Python 3.10。以下命令从仓库根目录执行：

```bash
git clone --recurse-submodules https://github.com/Sean-xzx/neupan_ros2.git
cd neupan_ros2
source /opt/ros/humble/setup.bash
./setup.sh
```

`setup.sh` 安装 ROS 和 C++ 系统依赖。PyTorch、`neupan` 等 Python 依赖请参照 [NeuPAN 上游项目](https://github.com/hanruihua/NeuPAN)；仓库 README 给出使用 `numpy<2.0` 的典型安装方式。之后编译并启动：

```bash
./build.sh
source install/setup.bash
ros2 launch neupan_ros2 sim_complete.launch.py sim_env_config:=scenario_corridor.yaml
```

其他场景包括 `scenario_empty.yaml`、`scenario_maze.yaml`、`scenario_narrow_passage.yaml`、`scenario_polygon_random.yaml` 和 `scenario_u_trap.yaml`。场景用途见仿真器 README。连接真实机器人前，应核对驱动、话题、坐标系和对应的机器人配置。

## 验证与维护

```bash
colcon build --symlink-install
colcon test
colcon test-result --verbose
```

CI 工作流在 ROS 2 Humble 环境中构建和测试；代码风格任务只给出提示。文档检查或 Git 检查成功不等于 ROS 运行验证。克隆时使用 `--recurse-submodules`，或在克隆后执行 `git submodule update --init --recursive`。

修改机器人配置时，从 `src/neupan_ros2/config/robots/<robot>/robot.yaml` 和 `planner.yaml` 入手，再检查对应启动文件。修改仿真场景时，从 `src/ddr_minimal_sim/config/` 入手。整理这个派生仓库时，应保留原论文引用和上游贡献者的署名。
