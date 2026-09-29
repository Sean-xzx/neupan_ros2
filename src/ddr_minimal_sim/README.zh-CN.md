# DDR Minimal Sim

[简体中文](README.zh-CN.md) · [English](README.md) · [工作空间](../../README_CN.md)

这是 NeuPAN ROS 2 工作空间中的轻量级差速机器人仿真器，用于快速验证导航算法。

## 功能与节点

| 节点 | 作用 |
| --- | --- |
| `simulator_node` | 差速运动学、里程计、TF、仿真时钟，订阅 `/cmd_vel` |
| `environment_node` | 静态障碍、边界、RViz 标记、占据栅格及 YAML 场景 |
| `laser_simulator_node` | 光线投射式二维激光扫描，发布 `/scan` |

场景配置位于 `config/`。现有场景：`scenario_corridor.yaml`（走廊）、`scenario_maze.yaml`（迷宫）、`scenario_narrow_passage.yaml`（窄通道）、`scenario_u_trap.yaml`（U 形陷阱）、`scenario_polygon_random.yaml`（随机多边形）和 `scenario_empty.yaml`（空场景）。

## 快速运行

先按照[工作空间指南](../../docs/PROJECT_GUIDE.zh-CN.md)安装依赖并编译，再运行独立仿真：

```bash
source install/setup.bash
ros2 launch ddr_minimal_sim complete_sim.launch.py sim_env_config:=scenario_maze.yaml
```

另开终端发送速度指令：

```bash
ros2 topic pub /cmd_vel geometry_msgs/Twist "{linear: {x: 0.3}, angular: {z: 0.0}}"
```

启动参数包括 `sim_env_config:=<scenario.yaml>` 和 `rviz:=<true|false>`。完整的 NeuPAN 与仿真器组合启动方式见工作空间 README。

## 配置和接口

场景 YAML 可配置初始位姿、线速度和角速度上限、机器人矩形尺寸、激光最大量程、光线数量、噪声标准差，以及圆形或多边形障碍物。详情及示例见[英文原文](README.md#configuration)。

订阅 `/cmd_vel`；发布 `/odom`、`/scan`、`/clock`、`/vehicle_markers`、`/environment_markers` 和 `/environment_grid`。TF 链为 `map → odom → base_link → laser_link`。

主要依赖为 ROS 2 Humble、Eigen3、yaml-cpp 及相关 ROS 消息和 TF 软件包。新增场景时，在 `config/` 添加 YAML，再以 `sim_env_config` 指定。核心实现位于 `include/ddr_minimal_sim/` 和 `src/`。

## 范围与排查

仿真器仅支持二维、静态障碍、简化碰撞和高斯传感器噪声。英文原文记录了默认动力学和激光频率分别为 100 Hz 与 20 Hz，以及低功耗设备上占据栅格生成的潜在启动耗时。

机器人不动时检查 `ros2 topic info /cmd_vel`、速度上限和 `use_sim_time`；没有激光数据时检查节点、量程和 TF；RViz 无画面时检查固定坐标系及标记话题。

许可证为仓库根目录的 [GPL v3](../../LICENSE)。原文作者署名 KevinLADLee；设计参考 [DDR-opt](https://github.com/ZJU-FAST-Lab/DDR-opt)。完整致谢见[工作空间 README](../../README_CN.md#致谢)。
