# 设置开发环境（工具链）

PX4 开发的**支持平台**包括：

- [Ubuntu Linux（22.04/20.04/18.04）](../dev_setup/dev_env_linux_ubuntu.md) — 推荐
- [Windows（10/11）](../dev_setup/dev_env_windows_wsl.md) — 通过 WSL2
- [Mac OS](../dev_setup/dev_env_mac.md)

## 支持的目标

下表显示了每个操作系统上可以构建的 PX4 目标。

| 目标                                                                                                                                 | Linux (Ubuntu) | Mac | Windows |
| -------------------------------------------------------------------------------------------------------------------------------------- | :------------: | :-: | :-----: |
| **基于 NuttX 的硬件：** [Pixhawk 系列](../flight_controller/pixhawk_series.md), [Crazyflie](../complete_vehicles_mc/crazyflie2.md) |       ✓        |  ✓  |    ✓    |
| **基于 Linux 的硬件：** [Raspberry Pi 2/3](../flight_controller/raspberry_pi_navio2.md)                                              |       ✓        |     |         |
| **仿真：** [Gazebo SITL](../sim_gazebo_gz/index.md)                                                                               |       ✓        |  ✓  |    ✓    |
| **仿真：** [Gazebo Classic SITL](../sim_gazebo_classic/index.md)                                                                  |       ✓        |  ✓  |    ✓    |
| **仿真：** [ROS with Gazebo Classic](../simulation/ros_interface.md)                                                              |       ✓        |     |    ✓    |
| **仿真：** ROS 2 with Gazebo                                                                                                      |       ✓        |     |    ✓    |

有 Docker 使用经验的用户也可以使用我们持续集成系统使用的容器进行构建：[Docker 容器](../test_and_ci/docker.md)

## 后续步骤

完成上述命令行工具链设置后：

- 安装 [VSCode](../dev_setup/vscode.md)（如果您更倾向于使用 IDE 而不是命令行）。
- 安装 [QGroundControl Daily Build](../dev_setup/qgc_daily_build.md)
- 继续 [构建 PX4 软件](../dev_setup/building_px4.md)。