# uXRCE-DDS（PX4-ROS 2/DDS 桥接器）

<Badge type="tip" text="PX4 v1.14" />

::: info
uXRCE-DDS 替换了 PX4 v1.13 中使用的 [Fast-RTPS 桥接器](https://docs.px4.io/v1.13/en/middleware/micrortps.html#rtps-dds-interface-px4-fast-rtps-dds-bridge)。
如果之前使用的是 Fast-RTPS 桥接器，请遵循 [迁移指南](#fast-rtps-to-uxrce-dds-migration-guidelines)。
:::

PX4 使用 uXRCE-DDS 中间件，使得 [uORB messages](../middleware/uorb.md) 可以像 [ROS 2](../ros2/user_guide.md) 主题一样在伴飞计算机上发布和订阅。
这为 PX4 与 ROS 2 提供了快速可靠的集成方式，并使 ROS 2 应用程序更易于获取机体信息和发送指令。

PX4 使用的 XRCE-DDS 实现基于 [eProsima Micro XRCE-DDS](https://micro-xrce-dds.docs.eprosima.com/en/stable/introduction.html)。

以下指南描述了架构以及设置客户端和代理的各种选项。
特别是涵盖了对 PX4 用户最重要的配置选项。

## 架构

uXRCE-DDS 中间件由运行在 PX4 上的客户端和运行在伴飞计算机上的代理组成，它们通过串口或 UDP 链路进行双向数据交换。
该代理作为客户端的代理，使其能够发布和订阅全局 DDS 数据空间中的主题。

![ROS 2 环境下的 uXRCE-DDS 架构](../../assets/middleware/xrce_dds/architecture_xrce-dds_ros2.svg)

为了使 PX4 uORB 主题在 DDS 网络中共享，需要在 PX4 上运行 _uXRCE-DDS 客户端_，并连接到伴飞计算机上的 _micro XRCE-DDS 代理_。

PX4 的 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 会将定义好的 uORB 主题集发布/订阅到全局 DDS 数据空间。

[eProsima micro XRCE-DDS _代理_](https://github.com/eProsima/Micro-XRCE-DDS-Agent) 运行在伴飞计算机上，作为客户端在 DDS/ROS 2 网络中的代理。

该代理本身不依赖客户端代码，可以独立于 PX4 或 ROS 进行构建和/或安装。

想要订阅/发布 PX4 的代码确实需要依赖客户端代码；它需要与用于创建 PX4 uXRCE-DDS 客户端的 uORB 消息定义相匹配，以便能够解析这些消息。

## 代码生成

PX4 的 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 在构建时生成并默认包含在 PX4 固件中。代理不依赖于客户端代码，可以独立构建、在 ROS 2 工作空间中构建，或通过 Ubuntu 上的 snap 包安装。

当 PX4 构建时，代码生成器会使用源代码树中的 [PX4-Autopilot/msg](https://github.com/PX4/PX4-Autopilot/tree/main/msg) uORB 消息定义，将 [/src/modules/uxrce_dds_client/dds_topics.yaml](../middleware/dds_topics.md) 中指定的 uORB 主题子集编译到 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 中。

PX4 主分支或发布版本构建时会自动将构建中使用的 uORB 消息定义导出到 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 的关联分支中。

ROS 2 应用程序需要在包含与 PX4 固件中 uXRCE-DDS 客户端模块创建时使用的 _相同_ 消息定义的工作空间中构建。可以通过将接口包 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 克隆到您的 ROS 2 工作空间并切换到适当分支来实现。请注意，所有与消息相关的代码生成均由 ROS 2 处理。

## Micro XRCE-DDS Agent 安装

Micro XRCE-DDS Agent 可以通过二进制包安装在协处理器上，也可以从源代码构建安装，或者在 ROS 2 工作空间内构建运行。  
所有方法都会获取与客户端通信所需的所有依赖项（如 FastCDR）。

::: info
官方（且更完整的）安装指南请参考 Eprosima：[micro XRCE-DDS 安装指南](https://micro-xrce-dds.docs.eprosima.com/en/latest/installation.html)  
本节总结了在编写本文档时与 PX4 验证过的安装选项。
:::

::: warning
PX4 Micro XRCE-DDS 客户端基于 `v2.x` 版本，与最新版的 `v3.x` Agent 不兼容。
:::

### 从源代码独立安装

在 Ubuntu 上可通过以下命令从源代码构建并安装独立版 Agent：

```sh
git clone -b v2.4.2 https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
cd Micro-XRCE-DDS-Agent
mkdir build
cd build
cmake ..
make
sudo make install
sudo ldconfig /usr/local/lib/
```

::: info
[官方指南](https://micro-xrce-dds.docs.eprosima.com/en/latest/installation.html#installing-the-agent-standalone)中链接了多种构建配置选项，但这些选项尚未经过测试。
:::

要使用与模拟器中运行的 uXRCE-DDS 客户端连接的设置启动代理：

```sh
MicroXRCEAgent udp4 -p 8888
```

### 从 Snap 包安装

在 Ubuntu 上使用以下命令从 Snap 包安装：

```sh
sudo snap install micro-xrce-dds-agent --edge
```

要使用与模拟器中运行的 uXRCE-DDS 客户端连接的设置启动代理（注意命令名称与本地构建的代理不同）：

```sh
micro-xrce-dds-agent udp4 -p 8888
```

::: info
目前通过 Snap 安装的稳定版本可以连接 PX4，但会报告创建话题的错误。使用上述 `--edge` 标志安装的开发版可以正常工作。
:::

### 在 ROS 2 工作空间内构建/运行

代理可以在 ROS 2 工作空间内构建和启动（或独立构建并从工作空间启动）。  
您必须已按照 [ROS 2 用户指南 > 安装 ROS 2](../ros2/user_guide.md#install-ros-2) 中的说明安装 ROS 2。

::: warning
此方法会使用现有 ROS 2 中的 Agent 依赖项，如 `fastcdr` 和 `fastdds`。  
这会显著加快构建速度，但要求 Agent 依赖版本与 ROS 2 中的版本匹配。
:::

在 ROS 中构建代理：

1. 创建代理的工作空间目录：

   ```sh
   mkdir -p ~/px4_ros_uxrce_dds_ws/src
   ```

1. 将 eProsima [Micro-XRCE-DDS-Agent](https://github.com/eProsima/Micro-XRCE-DDS-Agent) 源代码克隆到 `/src` 目录（默认克隆 main 分支）：

   ```sh
   cd ~/px4_ros_uxrce_dds_ws/src
   git clone -b v2.4.2 https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
   ```

1. 源入 ROS 2 开发环境，并使用 `colcon` 编译工作空间：

   :::: tabs

   ::: tab humble

   ```sh
   source /opt/ros/humble/setup.bash
   colcon build
   ```

   :::

   ::: tab foxy

   ```sh
   source /opt/ros/foxy/setup.bash
   colcon build
   ```

   :::

   ::::

   此命令将使用源入的工具链编译 `/src` 下的所有文件夹。

在工作空间中运行 micro XRCE-DDS 代理：

1. 源入 `local_setup.bash` 以在终端中启用可执行文件（新终端中还需源入 `setup.bash`）：

   :::: tabs

   ::: tab humble

   ```sh
   source /opt/ros/humble/setup.bash
   source install/local_setup.bash
   ```

   :::

   ::: tab foxy

   ```sh
   source /opt/ros/foxy/setup.bash
   source install/local_setup.bash
   ```

   :::

   ::::

1) 使用与模拟器中运行的 uXRCE-DDS 客户端连接的设置启动代理：

   ```sh
   MicroXRCEAgent udp4 -p 8888
   ```

## 启动 Agent 和 Client

### 启动 Agent

Agent 用于通过特定通道（如 UDP 或串口连接）与 Client 建立连接。
通道设置通过启动 Agent 时的命令行选项指定。
详细信息可参考 eProsima 用户指南：[Micro XRCE-DDS Agent > Agent CLI](https://micro-xrce-dds.docs.eprosima.com/en/latest/agent.html#agent-cli)。
注意 Agent 支持多种通道选项，但 PX4 仅支持 UDP 和串口连接。

::: info
您需要为每个需要连接的通道创建一个独立的 Agent 实例。
:::

例如，PX4 模拟器通过 UDP 端口 8888 运行 uXRCE-DDS Client，因此要连接模拟器，可使用以下命令启动 Agent：

```sh
MicroXRCEAgent udp4 -p 8888
```

在真实硬件环境中，设置取决于硬件、操作系统和通道。
例如，如果使用 RPi 的 `UART0` 串口，可基于以下命令连接（参考 [Raspberry Pi Documentation > Configuring UARTS](https://www.raspberrypi.com/documentation/computers/configuration.html#configuring-uarts)）：

```sh
sudo MicroXRCEAgent serial --dev /dev/AMA0 -b 921600
```

::: info
关于通信通道设置的更多详情，请参见 [Pixhawk + Companion Setup > Serial Port setup](../companion_computer/pixhawk_companion.md#serial-port-setup) 及子文档。
:::

### 启动 Client

uXRCE-DDS Client 模块 ([uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client)) 默认包含在所有固件和模拟器中。
必须使用与 Agent 通信的通道设置启动该模块。

::: info
模拟器默认在本地主机 UDP 端口 `8888` 使用默认的 uxrce-dds 命名空间启动 Client。
:::

配置可通过 [UXRCE-DDS 参数](../advanced_config/parameter_reference.md#uxrce-dds-client) 完成：

- [UXRCE_DDS_CFG](../advanced_config/parameter_reference.md#UXRCE_DDS_CFG): 设置连接端口，如 `TELEM2`、`Ethernet` 或 `Wifi`。
- 若使用以太网连接：

  - [UXRCE_DDS_PRT](../advanced_config/parameter_reference.md#UXRCE_DDS_PRT):
    用于指定 Agent 的 UDP 监听端口。
    默认值为 `8888`。
  - [UXRCE_DDS_AG_IP](../advanced_config/parameter_reference.md#UXRCE_DDS_AG_IP):
    用于指定 Agent 的 IP 地址。
    该 IP 地址需以 `int32` 格式提供，因为 PX4 不支持字符串参数。
    默认值为 `2130706433`，对应 _localhost_ `127.0.0.1`。

    可使用 [Tools/convert_ip.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/convert_ip.py) 在格式间转换：

    - 获取 `int32` 版本的 IP 地址，命令为：

      ```sh
      python3 ./PX4-Autopilot/Tools/convert_ip.py <the IP address in decimal dot notation>
      ```

    - 从 `int32` 版本获取 IP 地址：

      ```sh
      python3 ./PX4-Autopilot/Tools/convert_ip.py -r <the IP address in int32 notation>
      ```

- 若使用串口连接：

  - [SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD) 等串口参数：
    用于指定串口波特率等设置。
    例如 `SER_TEL2_BAUD=921600`。

- 某些特殊参数如 [UXRCE_DDS_DOM_ID](../advanced_config/parameter_reference.md#UXRCE_DDS_DOM_ID) 可通过环境变量 `ROS_DOMAIN_ID` 覆盖。

::: info
具体参数配置需根据硬件和通信方式调整。
:::

部分场景需要自定义命名空间（namespace）：

- [PX4_UXRCE_DDS_NS](#customizing-the-namespace): 用于指定主题命名空间。

例如，以下命令可启动带有自定义 DDS 域、端口和命名空间的 Gazebo 模拟：

```sh
ROS_DOMAIN_ID=3 PX4_UXRCE_DDS_PORT=9999 PX4_UXRCE_DDS_NS=drone make px4_sitl gz_x500
```

## 支持的 uORB 消息

通过客户端暴露的 [PX4 uORB 话题](../msg_docs/index.md) 集合在 [dds_topics.yaml](../middleware/dds_topics.md) 中设置。

话题是特定于发布版本的（支持在构建时编译到 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 模块中）。
虽然大多数版本应支持非常相似的消息集，但要确保这一点需要检查特定发布版本的 yaml 文件。

需要注意的是，ROS 2/DDS 需要具有与 PX4 固件中 uXRCE-DDS 客户端模块创建时使用的 _相同_ 消息定义，才能解析消息。
消息定义存储在 ROS 2 接口包 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 中，并通过 CI 在 `main` 和发布分支上自动同步。
请注意，PX4 源代码中的所有消息都存在于仓库中，但只有 `dds_topics.yaml` 中列出的消息才会作为 ROS 2 话题可用。
因此，

- 如果您使用的是 main 或 release 版本的 PX4，可以通过将接口包 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 克隆到工作区来获取消息定义。
- 如果您正在创建或修改 uORB 消息，必须从 PX4 源代码树手动更新工作区中的消息。
  通常这意味着您需要更新 [dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)，克隆接口包，然后通过将新/修改的消息定义从 [PX4-Autopilot/msg](https://github.com/PX4/PX4-Autopilot/tree/main/msg) 复制到其 `msg` 文件夹来手动同步。
  假设 PX4-Autopilot 在您的主目录 `~` 中，而 `px4_msgs` 在 `~/px4_ros_com/src/` 中，则命令可能是：

  ```sh
  rm ~/px4_ros_com/src/px4_msgs/msg/*.msg
  cp ~/PX4-Autopilot/msg/*.msg ~/px4_ros_com/src/px4_msgs/msg/
  ```

  ::: info
  技术上来说，[dds_topics.yaml](../middleware/dds_topics.md) 完全定义了 PX4 uORB 话题与 ROS 2 消息之间的关系。
  有关更多信息，请参见下文的 [DDS Topics YAML](#dds-topics-yaml)。
  :::

## 自定义命名空间

自定义主题和服务命名空间可以在构建时应用（修改[dds_topics.yaml](../middleware/dds_topics.md)）或运行时应用（这对多机体操作很有用）：

- 一种方法是从命令行启动[uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client)时使用`-n`选项。
  此技术既可用于仿真环境也可用于真实机体。
- 通过在启动仿真前设置环境变量`PX4_UXRCE_DDS_NS`，可以为仿真（仅限）提供自定义命名空间。

::: info
在运行时更改命名空间会将所需的命名空间作为前缀添加到[dds_topics.yaml](../middleware/dds_topics.md)中所有`topic`字段和所有[service servers](#dds-ros-2-services)。
因此，如下的命令：

```sh
uxrce_dds_client start -n uav_1
```

或

```sh
PX4_UXRCE_DDS_NS=uav_1 make px4_sitl gz_x500
```

将在以下命名空间下生成主题：

```sh
/uav_1/fmu/in/  # 用于订阅者
/uav_1/fmu/out/ # 用于发布者
```

:::

## PX4 ROS 2 QoS 设置

PX4 发布者的 QoS 设置与 ROS 2 订阅者的默认 QoS 设置不兼容。因此如果 ROS 2 代码需要订阅 uORB 主题，就需要使用兼容的 QoS 设置。一个示例请参考 [ROS 2 用户指南 > ROS 2 订阅者 QoS 设置](../ros2/user_guide.md#ros-2-subscriber-qos-settings)。

PX4 为发布者使用的 QoS 设置如下：

```cpp
uxrQoS_t qos = {
  .durability = UXR_DURABILITY_TRANSIENT_LOCAL,
  .reliability = UXR_RELIABILITY_BEST_EFFORT,
  .history = UXR_HISTORY_KEEP_LAST,
  .depth = 0,
};
```

PX4 为订阅者使用的 QoS 设置如下：

```cpp
uxrQoS_t qos = {
  .durability = UXR_DURABILITY_VOLATILE,
  .reliability = UXR_RELIABILITY_BEST_EFFORT,
  .history = UXR_HISTORY_KEEP_LAST,
  .depth = queue_depth,
};
```

ROS 2 默认为发布者和订阅者使用的 QoS 设置为：历史记录类型为 "keep last" 且队列大小为 10，可靠性为 "reliable"，持久性为 "volatile"，活跃度为 "system default"。截止时间、生命周期和租约时长也都设置为 "default"。

<!-- 来自 https://github.com/PX4/PX4-user_guide/pull/2259#discussion_r1099788316 -->## DDS主题YAML

PX4的[dds_topics.yaml](../middleware/dds_topics.md)文件定义了固件中构建并发布的PX4 uORB主题集合。更精确地说，它完全定义了PX4 uORB与ROS 2消息之间的关系/配对。

文件结构如下：

```yaml
publications:

  - topic: /fmu/out/collision_constraints
    type: px4_msgs::msg::CollisionConstraints
    rate_limit: 50.  # 限制最大发布速率为50 Hz

  ...

  - topic: /fmu/out/vehicle_odometry
    type: px4_msgs::msg::VehicleOdometry
    # 使用默认的100 Hz最大发布速率限制

  - topic: /fmu/out/vehicle_status
    type: px4_msgs::msg::VehicleStatus
    rate_limit: 5.

  - topic: /fmu/out/vehicle_trajectory_waypoint_desired
    type: px4_msgs::msg::VehicleTrajectoryWaypoint

subscriptions:

  - topic: /fmu/in/offboard_control_mode
    type: px4_msgs::msg::OffboardControlMode

  ...

  - topic: /fmu/in/vehicle_trajectory_waypoint
    type: px4_msgs::msg::VehicleTrajectoryWaypoint

subscriptions_multi:

  - topic: /fmu/in/vehicle_optical_flow_vel
    type: px4_msgs::msg::VehicleOpticalFlowVel

  ...

```

每个(`topic`,`type`)对定义了以下内容：

1. 创建一个新的`publication`、`subscription`或`subscriptions_multi`，具体取决于其添加的列表。
2. 主题的_base name_（基础名称），必须与您要发布/订阅的uORB主题名称完全一致。它由`topic:`中以`/`开头且不含其他`/`的最后一个标记标识。`vehicle_odometry`、`vehicle_status`和`offboard_control_mode`都是基础名称的示例。
3. 主题的[命名空间](https://design.ros2.org/articles/topic_and_service_names.html#namespaces)。默认设置为：
   - `/fmu/out/`用于由PX4_发布_的主题。
   - `/fmu/in/`用于由PX4_订阅_的主题。
4. 消息类型（如`VehicleOdometry`、`VehicleStatus`、`OffboardControlMode`等）以及预期提供消息定义的ROS 2包（如`px4_msgs`）。
5. **（可选）**：额外的`rate_limit`字段（仅适用于发布条目），指定PX4向ROS 2发布此主题消息的最大速率（Hz）。若未指定，默认最大发布速率限制为100 Hz。

`subscriptions`和`subscriptions_multi`允许我们选择ROS 2主题路由到的uORB主题实例：要么是可能同时接收内部PX4 uORB发布者更新的共享实例，要么是专为ROS2发布的独立实例。如果没有这个机制，所有ROS 2消息都会被路由到相同的uORB主题实例（因为ROS 2不支持[uORB多实例](../middleware/uorb.md#multi-instance)），PX4订阅者将无法区分消息来源是ROS 2还是PX4发布者。

将主题添加到`subscriptions`部分：

- 创建从ROS2主题到相关uORB主题的默认实例（实例0）的单向路由。例如，它会创建`/fmu/in/vehicle_odometry`的ROS2订阅者和`vehicle_odometry`的uORB发布者。
- 如果其他内部PX4模块已经在同一uORB主题实例上发布，该实例的订阅者将接收到所有消息流。uORB订阅者将无法判断收到的消息是来自PX4还是ROS2。
- 这是当ROS2发布者预期是该主题实例的唯一发布者（例如在外部控制期间替代内部发布者）或多个发布流的来源不重要的情况下的期望行为。

将主题添加到`subscriptions_multi`部分：

- 创建从ROS2主题到相关uORB主题新实例的单向路由。例如，如果`vehicle_odometry`已有`2`个实例，它会创建`/fmu/in/vehicle_odometry`的ROS2订阅者并在`vehicle_odometry`的实例`3`上创建uORB发布者。
- 这确保了其他内部PX4模块不会在此实例上发布，订阅者可以订阅所需实例并区分发布者。
- 请注意，这仅保证PX4和ROS2发布者之间的隔离，不保证多个ROS2发布者之间的隔离。在这种情况下，它们的消息仍将被路由到同一实例。
- 例如，当您需要PX4记录两个相同传感器的读数时，这是期望行为；它们都会在相同主题上发布，但一个使用实例0，另一个使用实例1。

您可以随意更改配置。例如，可以使用不同的默认命名空间或自定义包存储消息定义。

## DDS (ROS 2) 服务

PX4 uXRCE-DDS 中间件支持 [ROS 2 服务](https://docs.ros.org/en/jazzy/Concepts/Basic/About-Services.html)。
这些是节点之间通过远程过程调用进行通信的方式，会返回结果。
它们通过将请求和响应行为分组，简化了 ROS 2 节点与 PX4 之间的通信，并确保回复仅返回给特定的请求用户。

服务服务器是接收远程过程请求、执行计算并返回结果的实体。
例如，定义在 [`px4_msgs::srv::VehicleCommand`](https://github.com/PX4/px4_msgs/blob/main/srv/VehicleCommand.srv) 中的 `/fmu/vehicle_command` 服务服务器可被 ROS 2 应用调用，用于向 PX4 发送 [VehicleCommand](../msg_docs/VehicleCommand.md) uORB 消息，并接收 PX4 的 [VehicleCommandAck](../msg_docs/VehicleCommandAck.md) uORB 消息作为响应。

有关服务列表、详细说明和示例，请参阅 ROS 2 用户指南中的[服务文档](../ros2/user_guide.md#px4-ros-2-service-servers)。

## Fast-RTPS 到 uXRCE-DDS 迁移指南

本指南解释了如何从使用 PX4 v1.13 [Fast-RTPS](../middleware/micrortps.md) 中间件迁移至 PX4 v1.14 `uXRCE-DDS` 中间件。  
如果您使用了 [为 PX4 v1.13 编写的 ROS 2 应用](https://docs.px4.io/v1.13/en/ros/ros2_comm.html)，或通过 Fast-RTPS 直接与 PX4 交互的 [应用](https://docs.px4.io/v1.13/en/middleware/micrortps.html#agent-in-an-offboard-fast-dds-interface-ros-independent)，这些指南将很有帮助。

::: info
本节包含迁移相关的具体信息。  
要充分理解 uXRCE-DDS，建议阅读本页面的其他内容。
:::

#### 依赖项无需删除

uXRCE-DDS 不需要 Fast-RTPS 所需的依赖项（如通过 [Fast DDS 安装](https://docs.px4.io/v1.13/en/dev_setup/fast-dds-installation.html) 安装的组件）。  
您可以选择保留这些依赖项，不会影响 uXRCE-DDS 应用。

如果确实要删除依赖项，请注意不要删除被其他应用使用的组件（例如 Java）。

#### `_rtps` 目标已被移除

所有之前使用 `_rtps` 后缀的构建目标（如 `px4_fmu-v5_rtps` 或 `px4_sitl_rtps`），现在均可使用对应的默认目标（如 `px4_fmu-v5_default` 和 `px4_sitl_default`）。

带有 `_rtps` 后缀的 make 目标用于构建包含客户端 RTPS 代码的固件。由于 uXRCE-DDS 中间件已默认集成在大多数开发板的构建中，因此不再需要特殊固件来配合 ROS 2 使用。

要确认您的开发板是否包含中间件，请查看开发板 `.px4board` 文件中的 `CONFIG_MODULES_UXRCE_DDS_CLIENT=y` 配置。  
这些文件位于 [PX4-Autopilot/boards](https://github.com/PX4/PX4-Autopilot/tree/main/boards) 目录中。

如果该配置不存在或设置为 `n`，则需要克隆 PX4 仓库，修改开发板配置并手动 [编译](../dev_setup/building_px4.md) 固件。

#### 新的客户端模块及启动参数

由于客户端由新的 PX4 模块实现，现在需要新的启动参数。  
请参考 [客户端启动部分](#starting-the-client) 了解具体操作。

#### 新的配置文件用于设置发布主题

特定固件中发布的订阅主题列表现在由 [dds_topics.yaml](../middleware/dds_topics.md) 配置文件管理，该文件替代了 [urtps_bridge_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/release/1.13/msg/tools/urtps_bridge_topics.yaml)。

更多信息请参见 [支持的 uORB 消息](#supported-uorb-messages) 和 [DDS Topics YAML](#dds-topics-yaml) 部分。

#### 客户端与代理之间的话题无需同步

在 ROS 2 中，代理与客户端之间桥接话题列表不再需要同步，因此 `update_px4_ros2_bridge.sh` 脚本也不再需要。

#### 默认话题命名规则已变更

话题命名格式已变更：

- 发布话题：`/fmu/topic-name/out`（Fast-RTPS） → `/fmu/out/topic-name`（XRCE-DDS）。
- 订阅话题：`/fmu/topic-name/in`（Fast-RTPS） → `/fmu/in/topic-name`（XRCE-DDS）。

您需要将应用程序更新为新规则。

::: info
您也可以编辑 [dds_topic.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 恢复旧命名规则。  
但不建议这么做，因为这意味着您将始终需要使用自定义固件。
:::

#### XRCE-DDS-Agent

XRCE-DDS 代理是"通用"且独立于 PX4 的：[micro-xrce-dds-agent](https://micro-xrce-dds.docs.eprosima.com/en/latest/agent.html)。  
您可以通过多种方式在 PC/伴飞计算机上安装它 - 详见 [专用章节](#micro-xrce-dds-agent-installation)。

#### 应用特定变更

如果您未通过代理使用 ROS 2（[Fast DDS 接口 ROS 独立](https://docs.px4.io/v1.13/en/middleware/micrortps.html#agent-in-an-offboard-fast-dds-interface-ros-independent)），则需要迁移到 [eProsima Fast DDS](https://fast-dds.docs.eprosima.com/en/latest/index.html)。

ROS 2 应用仍需要与 PX4 消息一起编译，您可以通过将 [px4_msgs](https://github.com/PX4/px4_msgs) 包添加到工作区实现。  
可以删除 [px4_ros_com](https://github.com/PX4/px4_ros_com) 包，因为它仅用于示例代码且不再需要。

在您的 ROS 2 节点中，需要：

- 更新 [QoS](#px4-ros-2-qos-settings) 设置，因为 PX4 不使用 ROS 2 默认设置。
- 修改话题名称（除非已编辑 [dds_topic.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)）。
- 移除所有时间同步相关内容，因为 XRCE-DDS 会自动处理代理/客户端时间同步。

在 C++ 应用中，可以这样设置消息的 `timestamp` 字段：

```cpp
msg.timestamp = this->get_clock()->now().nanoseconds() / 1000;
```

在 Python 应用中，可以这样设置消息的 `timestamp` 字段：

```python
msg.timestamp = int(self.get_clock().now().nanoseconds / 1000)
```

## 有用的资源

- [ROS 2在PX4中的应用：无缝过渡到XRCE-DDS的技术细节](https://www.youtube.com/watch?v=F5oelooT67E) - Pablo Garrido & Nuno Marques (youtube)
- [PX4 ROS 2外部控制教程](https://gist.github.com/julianoes/adbf76408663829cd9aed8d14c88fa29) (Github gist - JulianOes)
- [ROS 2 PX4 外部控制教程](https://github.com/Jaeyoung-Lim/px4-offboard/blob/2d784532fd323505ac8a6e53bb70145600d367c4/doc/ROS2_PX4_Offboard_Tutorial.md) (Jaeyoung-Lim)

<!---
Some of this might be useful.
I'd like to see a real example first.
-->## 设置与真实硬件的桥接

本节正在完善中。

## 故障排查

### 客户端报告所选UART端口繁忙

如果所选UART端口繁忙，可能MAVLink应用已经在使用该端口。
如果需要同时使用MAVLink和RTPS连接，您必须将连接移至其他端口，或使用PX4和伴飞计算机可用的协议分割器。

:::tip
开发期间临时测试桥接功能的快速修复方法是通过*NuttShell*停止MAVLink：
```sh
mavlink stop-all
```
:::

### 在伴飞计算机上启用UART

对于Raspberry Pi或任何其他伴飞计算机的UART传输，您需要启用串口：

1. 确保`userid`（Raspberry Pi默认为pi）属于`dialout`组：

   ```sh
   groups pi
   sudo usermod -a -G dialout pi
   ```
1. 对于Raspberry Pi，需要停止使用该端口的GPIO串口控制台：

   ```sh
   sudo raspi-config
   ```

   在显示的菜单中选择 **Interfacing options > Serial**。
   选择 *Would you like a login shell to be accessible over serial?* 时选择 **NO**。确认并重启。
1. 检查内核中的UART：

   ```sh
   sudo vi /boot/config.txt
   ```

   并确保`enable_uart`值设置为1：
   ```
    enable_uart=1
   ```
-->