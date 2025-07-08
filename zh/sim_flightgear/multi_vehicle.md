# 多机体模拟

:::warning
此模拟器由[社区维护和支持](../simulation/community_supported_simulators.md)。
它可能与当前版本的PX4兼容也可能不兼容。

有关核心开发团队支持的环境和工具的信息，请参见[工具链安装](../dev_setup/dev_env.md)。
:::

本主题介绍如何在SITL中使用FlightGear模拟多个机体。
所有机体实例的参数由其启动脚本定义。

::: info
这是模拟多个运行PX4的机体最接近真实环境的方式，允许轻松测试多种不同类型的机体。
适用于测试_QGroundControl_、[MAVSDK](https://mavsdk.mavlink.io/)等的多机体支持功能。

对于需要模拟大量机体的集群模拟，或测试仅受Gazebo Classic支持的计算机视觉等功能，请改用[多机体Gazebo Classic模拟](../sim_gazebo_classic/multi_vehicle_simulation.md)。
:::

## 如何启动多个实例

要启动多个实例（在不同端口和ID上）：

1. 检出支持多机体的[PX4分支](https://github.com/ThunderFly-aerospace/PX4Firmware/tree/flightgear-multi)（在ThunderFly-aerospace）：

   ```sh
   git clone https://github.com/ThunderFly-aerospace/PX4Firmware.git
   cd PX4Firmware
   git checkout flightgear-multi
   ```

1. 使用标准工具链（需安装FlightGear）构建PX4固件。
1. 通过[预定义脚本](https://github.com/ThunderFly-aerospace/PX4-FlightGear-Bridge/tree/master/scripts)启动第一个实例：

   ```sh
   cd ./Tools/flightgear_bridge/scripts
   ./vehicle1.sh
   ```

1. 使用另一个脚本启动后续实例：

   ```sh
   ./vehicle2.sh
   ```

每个实例都应有其自己的启动脚本，可以代表完全不同的机体类型。
使用预配置脚本时应获得以下视图。

![使用PX4 SITL和FlightGear的多机体模拟](../../assets/simulation/flightgear/flightgear-multi-vehicle-sitl.jpg)

地面站（如_QGroundControl_）通过标准UDP端口14550连接到所有实例（所有通信都指向同一端口）。

同时运行的实例数量主要受计算机资源限制。
FlightGear是单线程应用程序，但气动求解器消耗大量内存。
因此运行大量机体实例时可能需要拆分到多个计算机，并使用[多人游戏服务器](https://wiki.flightgear.org/Howto:Multiplayer)。

## 附加资源

- 有关端口配置的更多信息，请参见[模拟](../simulation/index.md)。