# 使用 PlotJuggler 实时绘制 uORB 主题数据

本节介绍如何使用 [PlotJuggler](../log/flight_log_analysis.md#plotjuggler) 和 _uXRCE-DDS Agent_ 实时绘制 [uORB 主题](../msg_docs/index.md) 的值。

该技术利用 PX4 [uXRCE-DDS](../middleware/uxrce_dds.md) 中间件将 uORB 主题导出为 ROS2 主题，PlotJuggler 可读取并绘制这些主题的实时变化（PlotJuggler 无法直接读取 uORB 主题，但对应 ROS2 主题的值是相同的）。

下图演示了模拟机体的实时调试方法，该方式同样适用于真实硬件。

<video src="../../assets/debug/realtime_debugging/realtime_debugging.mp4" width="720" controls></video>

## 先决条件

请按照 _ROS2 用户指南_ 中的 [ROS 2 安装与配置](../ros2/user_guide.md#installation-setup) 指南安装以下内容：

- ROS 2
- [Micro XRCE-DDS Agent](../ros2/user_guide.md#setup-micro-xrce-dds-agent-client)
- [PX4/px4_msgs](https://github.com/PX4/px4_msgs): PX4/ROS2 共享消息定义。
- PX4 源代码并构建模拟器。

  ::: tip
  如果使用真实硬件而非模拟器，则只有在需要修改发布到 ROS2 的主题集时才需要 PX4 源代码（默认只发布部分 uORB 主题）。
  :::

还需安装：

- [PlotJuggler for ROS2](https://github.com/facontidavide/PlotJuggler)

  ::: tip
  建议使用 Debian 包（不支持 snap 文件）。
  :::

## 使用方法

首先需要构建包含与要监控的 PX4 构建对应的 `px4_msgs` 的 ROS2 工作空间，然后在该工作空间内启动 PlotJuggler。
这使 ROS2 和 PlotJuggler 能够解析消息。
如果使用未修改的 PX4，可直接使用 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 中的定义。

::: info
此过程与 [构建 ROS2 工作空间](../ros2/user_guide.md#build-ros-2-workspace) 中的说明相同。
:::

假设 ROS2 工作空间命名为 `~/ros2_ws/`，请在终端中按如下方式获取并构建 `px4_msgs` 包：

```sh
cd ~/ros2_ws/src/
git clone https://github.com/PX4/px4_msgs.git
cd ..
colcon build
source install/setup.bash
```

然后通过以下命令在终端中运行 PlotJuggler：

```sh
ros2 run plotjuggler plotjuggler
```

要开始从 PX4 发送 ROS2 主题，需在 PX4 上运行 uXRCE-DDS **客户端**，并在 PlotJuggler 所在计算机上运行 `MicroXRCEAgent`。

### PX4 模拟器

接下来启动 [Gazebo](../sim_gazebo_gz/index.md) 四旋翼飞行器模拟器。
由于使用 PX4 模拟器，客户端会自动启动，但仍需启动代理并连接客户端。

首先打开另一个终端。
然后导航到 PX4 源代码根目录并使用以下命令启动模拟器：

```sh
cd ~/PX4-Autopilot
make px4_sitl gz_x500
```

打开另一个终端并启动 `MicroXRCEAgent` 连接模拟器：

```sh
MicroXRCEAgent udp4 -p 8888; exec bash
```

这应该就是连接模拟器所需的所有步骤。

### PX4 硬件

如果使用真实硬件，需在 PX4 上显式启动客户端，且代理连接命令会略有不同。
_ROS2 用户指南_ 中的 [使用飞控硬件](../ros2/user_guide.md#using-flight-controller-hardware) 提供了相关设置信息的链接。

## 不可用/新消息

所有来自 PX4 主分支的的消息定义都已导出到 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 仓库。
这些消息必须导入到 ROS2 工作空间中，以便 PlotJuggler 能够解析 PX4 消息。

::: info
导出消息使 ROS2 和 uXRCE-DDS 代理能够独立于 PX4 运行，因此只有在需要构建模拟器或修改消息时才需要 PX4 源代码。
:::

虽然 `px4_msgs` 包含了 PX4 中所有 uORB 主题的消息，但并非所有 `px4_msgs` 中的消息默认都对 ROS2/PlotJuggler 可用。
可用消息集必须构建到 PX4 上运行的客户端中。
这些消息在 [dds_topics.yaml](../middleware/dds_topics.md) 中定义。

以下说明解释了监控默认不可用主题所需的修改步骤。

### 缺失主题

如果正常 uORB 主题在 PlotJuggler 中不可用，需要修改 [dds_topics.yaml](../middleware/dds_topics.md) ([源码](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)) 添加该主题并重新构建 PX4。

如果使用真实硬件，在修改 YAML 文件后需要构建并[安装](../config/firmware.md#installing-px4-main-beta-or-custom-firmware) 自定义固件。

### 修改后的消息

如果修改了任何 uORB 消息，必须更新 PlotJuggler 使用的 ROS2 消息。

需要使用新消息重新构建 PX4，并用新消息替换工作空间中的 `px4_msgs`（仓库中的版本）。

假设已将 PX4 构建在 `~/PX4-Autopilot/` 目录中，且 `~/ros2_ws` 是 ROS2 工作空间，请输入以下命令复制消息并重新构建工作空间：

```sh
rm -f ~/ros2_ws/src/px4_msgs/msg/*.msg
cp ~/PX4-Autopilot/msg/*.msg ~/ros2_ws/src/px4_msgs/msg/
cd ~/ros2_ws/ && colcon build
```

### 自定义主题

定义主题后，按照上述说明将其添加到 [dds_topics.yaml](../middleware/dds_topics.md) 中，并将新消息导出到 ROS2 工作空间。

## 参见

[ROS2 用户指南](../ros2/user_guide.md)