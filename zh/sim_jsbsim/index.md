# JSBSim 模拟

:::warning
此模拟器由社区支持和维护。
它可能与当前版本的 PX4 兼容，也可能不兼容。

有关核心开发团队支持的环境和工具的信息，请参阅[工具链安装](../dev_setup/dev_env.md)。
:::

[JSBSim](http://jsbsim.sourceforge.net/index.html) 是一个开源飞行模拟器（"飞行动力学模型 (FDM)"），可在 Microsoft Windows、Apple Macintosh、Linux、IRIX、Cygwin (Unix on Windows) 等平台上运行。
其功能包括：完全可配置的空气动力学和推进系统，可模拟复杂飞行动力学。
地球旋转效应也被建模到动力学中。

**支持的机体:** 飞机、四旋翼、六旋翼

<lite-youtube videoid="y5azVNmIVyw" title="JSBSim with APX4 Software-In-The-Loop Simulation"/>

::: info
有关模拟器、模拟环境和模拟配置（例如支持的机体）的一般信息，请参阅[仿真](../simulation/index.md)。
:::

## 安装 (Ubuntu Linux)

::: info
这些说明已在 Ubuntu 18.04 上测试
:::

1. 安装 [Ubuntu LTS / Debian Linux 的开发环境](../dev_setup/dev_env_linux_ubuntu.md)。
1. 从 [发布页面](https://github.com/JSBSim-Team/jsbsim/releases/tag/Linux) 安装 JSBSim 发行版：

   ```sh
   dpkg -i JSBSim-devel_1.1.0.dev1-<release-number>.bionic.amd64.deb
   ```

1. （可选）可（可选）使用 FlightGear 进行可视化。
   要安装 FlightGear，请参阅[FlightGear 安装说明](../sim_flightgear/index.md))。

## 运行模拟

JSBSim SITL 模拟可以通过以下 `make` 命令方便地运行：

```sh
cd /path/to/PX4-Autopilot
make px4_sitl jsbsim
```

这将同时运行 PX4 SITL 实例和 FlightGear UI（用于可视化）。
如果希望在没有 FlightGear UI 的情况下运行，可以在 `make` 命令前添加 `HEADLESS=1`。

支持的机体和 `make` 命令如下（点击链接查看机体图像）。

| 机体          | 命令                            |
| ------------- | ------------------------------- |
| 标准飞机      | `make px4_sitl jsbsim_rascal`   |
| 四旋翼        | `make px4_sitl jsbsim_quadrotor_x` |
| 六旋翼        | `make px4_sitl jsbsim_hexarotor_x` |

上述命令将启动带有完整 UI 的单个机体。
_QGroundControl_ 应能自动连接到模拟的机体。

## 通过 ROS 运行 JSBSim

要通过 ROS 运行 JSBSim：

1. 将 `px4-jsbsim-bridge` 包克隆到您的 catkin 工作区中：

   ```sh
   cd <path_to_catkin_ws>/src
   git clone https://github.com/Auterion/px4-jsbsim-bridge.git
   ```

1. 构建 `jsbsim_bridge` catkin 包：

   ```sh
   catkin build jsbsim_bridge
   ```

   ::: info
   您必须已在工作区中设置了 MAVROS（如果没有，请按照 [MAVROS 安装指南](../ros/mavros_installation.md) 中的说明进行操作）。
   :::

1. 通过 ROS 使用启动文件启动 JSBSim，如下所示：

   ```sh
   roslaunch jsbsim_bridge px4_jsbsim_bridge.launch
   ```

## 更多信息

- [px4-jsbsim-bridge 说明文档](https://github.com/Auterion/px4-jsbsim-bridge)