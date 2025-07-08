# 使用 JMAVSim 进行多机体仿真

本主题介绍如何使用 [JMAVSim](../sim_jmavsim/index.md) 和 SITL 模拟多个无人飞行器（多旋翼）机体。所有机体实例将在仿真中同时启动。

:::tip
这是模拟多个 PX4 机体运行的最简单方式。适用于测试 _QGroundControl_（或 [MAVSDK](https://mavsdk.mavlink.io/) 等）中的多机体支持功能。  
[使用 Gazebo 进行多机体仿真](../simulation/multi-vehicle-simulation.md) 应用于大规模集群仿真，或测试 Gazebo 独有的功能（如计算机视觉）。
:::

## 如何启动多个实例

要启动多个实例（在不同端口上）：

1. 构建 PX4

   ```sh
   make px4_sitl_default
   ```

1. 运行 **sitl_multiple_run.sh**，指定要启动的实例数量（例如 2）：

   ```sh
   ./Tools/sitl_multiple_run.sh 2
   ```

1. 在同一终端中启动第一个实例（将在前台运行）：

   ```sh
   ./Tools/simulation/jmavsim/jmavsim_run.sh -l
   ```

1. 为后续每个实例打开新终端，指定该实例的 _仿真_ TCP 端口：

   ```sh
   ./Tools/simulation/jmavsim/jmavsim_run.sh -p 4560 -l
   ```

   端口号应设置为 `4560+i`，其中 `i` 为每个实例的迭代值（从 `0` 到 `N-1`）

_QGroundControl_ 将自动连接到所有新机体实例（所有地面站通信均发送到 PX4 的远程 UDP 端口 `14550`）。当前被控制的机体将在应用程序状态栏中显示；点击该机体文本可显示所有已连接仿真机体实例（`Vehicle 1`、`Vehicle 2` 等）的列表，并可选择新的机体进行控制。

开发者 API（如 _MAVSDK_ 或 _MAVROS_）可通过监听 PX4 远程 UDP 端口依次分配的 `14540`（第一个实例）到 `14549` 来连接单个实例。额外的实例均连接到端口 `14549`。

> **Tip** **sitl_multiple_run.sh** 脚本为每个机体启动独立进程。  
> 在终止其中一个实例后重新启动仿真，必须重新调用 **sitl_multiple_run.sh**，并在各自终端中重新启动每个独立实例。

## 补充资源

- 有关端口配置的更多信息，请参见 [仿真](../simulation/index.md)。