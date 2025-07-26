

# uXRCE-DDS（PX4-ROS 2/DDS 桥接器）

<Badge type="tip" text="PX4 v1.14" />

::: info
uXRCE-DDS 替代了 PX4 v1.13 中使用的 [Fast-RTPS 桥接器](https://docs.px4.io/v1.13/en/middleware/micrortps.html#rtps-dds-interface-px4-fast-rtps-dds-bridge)。  
如果您之前使用的是 Fast-RTPS 桥接器，请参考 [迁移指南](#Fast-RTPS 到 uXRCE-DDS 迁移指南)。
:::

PX4 使用 uXRCE-DDS 中间件，使 [uORB 消息](../middleware/uorb.md) 可以在伴飞计算机上如同 [ROS 2](../ros2/user_guide.md) 主题一样进行发布和订阅。  
这为 PX4 与 ROS 2 提供了快速且可靠的集成，使 ROS 2 应用更容易获取机体信息并发送指令。

PX4 使用的 XRCE-DDS 实现基于 [eProsima Micro XRCE-DDS](https://micro-xrce-dds.docs.eprosima.com/en/stable/introduction.html)。

以下指南描述了架构及设置客户端和代理的各种选项。  
特别涵盖了对 PX4 用户最重要的选项。

## 架构

uXRCE-DDS 中间件由运行在 PX4 上的客户端和运行在伴飞计算机上的代理组成，它们通过串口或 UDP 链接进行双向数据交换。代理作为客户端的代理，使其能够向全局 DDS 数据空间发布和订阅主题。

![uXRCE-DDS 与 ROS 2 架构图](../../assets/middleware/xrce_dds/architecture_xrce-dds_ros2.svg)

为了让 PX4 uORB 主题在 DDS 网络上共享，需要在 PX4 上运行 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client)，并连接到伴飞计算机上的 [eProsima micro XRCE-DDS _agent_](https://github.com/eProsima/Micro-XRCE-DDS-Agent)。

PX4 的 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 会将一组定义的 uORB 主题发布到/从全局 DDS 数据空间中。

[eProsima micro XRCE-DDS _agent_](https://github.com/eProsima/Micro-XRCE-DDS-Agent) 运行在伴飞计算机上，作为客户端在 DDS/ROS 2 网络中的代理。

代理本身不依赖于客户端代码，可以独立于 PX4 或 ROS 构建和/或安装。

想要向 PX4 订阅/发布代码确实需要依赖客户端代码；它需要与创建 PX4 uXRCE-DDS 客户端所使用的 uORB 消息定义相匹配，以便能够解释这些消息。

## 代码生成

PX4 的 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 在构建时自动生成并默认包含在 PX4 固件中。
代理对客户端代码没有依赖关系。
它可以在独立环境中构建，也可以在 ROS 2 工作空间中构建，或作为 snap 包安装在 Ubuntu 上。

当构建 PX4 时，代码生成器会使用源代码树中的 uORB 消息定义 ([PX4-Autopilot/msg](https://github.com/PX4/PX4-Autopilot/tree/main/msg))，将 [/src/modules/uxrce_dds_client/dds_topics.yaml](../middleware/dds_topics.md) 中指定的 uORB 主题子集编译到 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 中。

PX4 的主版本或发布版本构建会自动将构建中的一组 uORB 消息定义导出到 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 中的关联分支。

ROS 2 应用需要在包含与 PX4 固件中 uXRCE-DDS 客户端模块相同消息定义的工作空间中构建。
这些可以通过将接口包 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 克隆到 ROS 2 工作空间中并切换到适当分支来实现。
请注意，所有与消息相关的代码生成均由 ROS 2 处理。

## Micro XRCE-DDS 代理安装

Micro XRCE-DDS 代理可以通过二进制包安装到伴飞计算机上，也可以从源代码构建并安装，或者在 ROS 2 工作空间内构建并运行。所有这些方法都会获取与客户端通信所需的所有依赖项（例如 FastCDR）。

::: info  
官方（且更完整）的安装指南是 Eprosima 提供的[micro XRCE-DDS 安装指南](https://micro-xrce-dds.docs.eprosima.com/en/latest/installation.html)。本节总结了在编写这些文档时与 PX4 测试过的选项。  
:::  

::: warning  
PX4 Micro XRCE-DDS 客户端基于版本 `v2.x`，该版本与最新的 `v3.x` 代理版本不兼容。  
:::

### 从源码安装独立版本

在Ubuntu系统上，你可以通过以下命令从源码构建并安装独立版的Agent：

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
在[官方指南](https://micro-xrce-dds.docs.eprosima.com/en/latest/installation.html#installing-the-agent-standalone)中对应主题有各种构建配置选项，但这些选项尚未经过测试。
:::

要使用设置连接到模拟器上运行的uXRCE-DDS客户端启动代理：

```sh
MicroXRCEAgent udp4 -p 8888
```

### 从Snap包安装

在Ubuntu上使用以下命令从snap包安装：

```sh
sudo snap install micro-xrce-dds-agent --edge
```

要使用设置启动代理以连接到模拟器上运行的uXRCE-DDS客户端（注意命令名称与本地构建代理时不同）：

```sh
micro-xrce-dds-agent udp4 -p 8888
```

::: info
写作时通过snap安装的稳定版可连接到PX4但会报告创建话题的错误。
使用上面`--edge`获取的开发版本可以正常工作。
:::

### 在 ROS 2 工作空间中构建/运行

代理可以在 ROS 2 工作空间内构建和启动（或独立构建后从工作空间启动）。  
您必须已按照以下指南安装 ROS 2：[ROS 2 用户指南 > 安装 ROS 2](../ros2/user_guide.md#install-ros-2)。

::: warning
此方法将使用 ROS 2 中已有的代理依赖项版本，例如 `fastcdr` 和 `fastdds`。  
这会显著加快构建过程，但要求代理依赖项版本与 ROS 2 中的版本一致。  
:::

在 ROS 中构建代理的步骤：

1. 创建代理的工作空间目录：

   ```sh
   mkdir -p ~/px4_ros_uxrce_dds_ws/src
   ```

1. 将 eProsima [Micro-XRCE-DDS-Agent](https://github.com/eProsima/Micro-XRCE-DDS-Agent) 的源代码克隆到 `/src` 目录（默认克隆 `main` 分支）：

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

   这会使用已源入的工具链编译 `/src` 目录下的所有文件夹。

在工作空间中运行 micro XRCE-DDS 代理的步骤：

1. 源入 `local_setup.bash` 以在终端中使用可执行文件（如果使用新终端，还需源入 `setup.bash`）。

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

1) 使用连接到模拟器上运行的 uXRCE-DDS 客户端的设置启动代理：

   ```sh
   MicroXRCEAgent udp4 -p 8888
   ```

## 启动代理和客户端

### 启动代理

代理用于通过特定通道（如UDP或串行连接）连接到客户端。
通道设置在启动代理时通过命令行选项指定。
这些内容在eProsima用户指南中有详细说明：[Micro XRCE-DDS Agent > Agent CLI](https://micro-xrce-dds.docs.eprosima.com/en/latest/agent.html#agent-cli)。
请注意，代理支持许多通道选项，但PX4仅支持UDP和串行连接。

::: info
您需要为每个需要连接的通道创建一个单独的代理实例。
:::

例如，PX4模拟器通过UDP的8888端口运行uXRCE-DDS客户端，因此要连接到模拟器，您会使用以下命令启动代理：

```sh
MicroXRCEAgent udp4 -p 8888
```

在使用真实硬件时，设置取决于硬件、操作系统和通道。
例如，如果您使用的是RPi的`UART0`串行端口，可以基于[Raspberry Pi Documentation > Configuring UARTS](https://www.raspberrypi.com/documentation/computers/configuration.html#configuring-uarts)中的信息使用此命令连接：

```sh
sudo MicroXRCEAgent serial --dev /dev/AMA0 -b 921600
```

::: info
有关设置通信通道的更多信息，请参见 [Pixhawk + Companion Setup > Serial Port setup](../companion_computer/pixhawk_companion.md#serial-port-setup) 及其子文档。
:::

### 启动客户端

uXRCE-DDS 客户端模块([uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client))默认包含在所有固件和模拟器中。
必须使用适当的通信通道设置来启动客户端，以便与代理进行通信。

::: info
模拟器会自动在本地主机的 UDP 端口 `8888` 上启动客户端，使用默认的 uxrce-dds 命名空间。
:::

配置可以通过[UXRCE-DDS 参数](../advanced_config/parameter_reference.md#uxrce-dds-client)完成：

- [UXRCE_DDS_CFG](../advanced_config/parameter_reference.md#UXRCE_DDS_CFG)：设置连接端口，例如 `TELEM2`、`Ethernet` 或 `Wifi`。
- 如果使用以太网连接：

  - [UXRCE_DDS_PRT](../advanced_config/parameter_reference.md#UXRCE_DDS_PRT)：
    用于指定代理的 UDP 监听端口。
    默认值为 `8888`。
  - [UXRCE_DDS_AG_IP](../advanced_config/parameter_reference.md#UXRCE_DDS_AG_IP)：
    用于指定代理的 IP 地址。
    IP 地址必须以 `int32` 格式提供，因为 PX4 不支持字符串参数。
    默认值为 `2130706433`，对应本地主机 `127.0.0.1`。

    可以使用 [Tools/convert_ip.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/convert_ip.py) 进行格式转换：

    - 要将点分十进制表示的 IP 转换为 `int32` 格式，请使用：

      ```sh
      python3 ./PX4-Autopilot/Tools/convert_ip.py <the IP address in decimal dot notation>
      ```

    - 要将 `int32` 格式的 IP 转换为点分十进制表示，请使用：

      ```sh
      python3 ./PX4-Autopilot/Tools/convert_ip.py -r <the IP address in int32 notation>
      ```

- 如果使用串口连接：

  - [SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD)，[SER_URT6_BAUD](../advanced_config/parameter_reference.md#SER_URT6_BAUD)（依此类推）：
    使用与串口关联的 `_BAUD` 参数设置波特率。
    例如，如果通过 `TELEM2` 连接到伴侣计算机，则需要设置 `SER_TEL2_BAUD` 的值。
    更多信息请参见[串口配置](../peripherals/serial_configuration.md#serial-port-configuration)。

- 某些配置可能还需要设置以下参数：

  - [UXRCE_DDS_KEY](../advanced_config/parameter_reference.md#UXRCE_DDS_KEY)：uXRCE-DDS 密钥。
    在多客户端、单代理的配置中，每个客户端应具有唯一的非零密钥。
    这在多机体模拟时尤为重要，所有客户端通过 UDP 连接到同一个代理。
    （参见[官方 eprosima 文档](https://micro-xrce-dds.docs.eprosima.com/en/stable/client_api.html#session) ，`uxr_init_session`。）
  - [UXRCE_DDS_DOM_ID](../advanced_config/parameter_reference.md#UXRCE_DDS_DOM_ID)：DDS 域 ID。
    用于在 DDS 网络之间提供逻辑隔离，并可用于区分不同网络上的客户端。
    默认情况下，ROS 2 使用 ID 0。
  - [UXRCE_DDS_PTCFG](../advanced_config/parameter_reference.md#UXRCE_DDS_PTCFG)：uXRCE-DDS 参与者配置。
    允许限制 DDS 主题的可见性仅限本地主机，并使用代理端存储的用户自定义参与者配置文件。
  - [UXRCE_DDS_SYNCT](../advanced_config/parameter_reference.md#UXRCE_DDS_SYNCT)：桥接时间同步启用。
    uXRCE-DDS 客户端模块可以同步通过桥接传输的消息时间戳。
    这是默认配置。在某些情况下，例如在[模拟](../ros2/user_guide.md#ros-gazebo-and-px4-time-synchronization)中，此功能可能会被禁用。

::: info
许多端口已经具有默认配置。
要使用这些端口，您需要首先禁用现有配置：

- `TELEM1` 和 `TELEM2` 默认配置为通过 MAVLink 连接到 GCS 和伴侣计算机（分别）。
  通过设置 [MAV_0_CONFIG=0](../advanced_config/parameter_reference.md#MAV_0_CONFIG) 或 [MAV_1_CONFIG=0](../advanced_config/parameter_reference.md#MAV_1_CONFIG) 为零来禁用。
  更多信息请参见[MAVLink 外设](../peripherals/mavlink_peripherals.md)。
- 其他端口可以类似配置。
  请参见[串口配置](../peripherals/serial_configuration.md#serial-port-configuration)。
  :::

设置完成后，可能需要重新启动 PX4 以使参数生效。
这些参数将通过后续重启持续生效。

您也可以通过命令行启动 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client)。
这可以作为[系统启动](../concept/system_startup.md)的一部分，或通过[MAVLink Shell](../debug/mavlink_shell.md)（或系统控制台）调用。
当需要自定义客户端命名空间时，此方法非常有用，因为没有为此目的提供参数。
例如，以下命令可用于通过以太网连接到远程主机 `192.168.0.100:8888`，并将客户端命名空间设置为 `/drone/`。

```sh
uxrce_dds_client start -t udp -p 8888 -h 192.168.0.100 -n drone
```

选项 `-p` 或 `-h` 用于绕过 `UXRCE_DDS_PRT` 和 `UXRCE_DDS_AG_IP`。

#### 在模拟中启动客户端

模拟器的[启动逻辑](../concept/system_startup.md) ([init.d-posix/rcS](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/rcS)) 使用客户端启动命令来支持单机和[多机体模拟](../ros2/multi_vehicle.md)，从而允许设置合适的实例ID和DDS命名空间。
默认情况下，客户端在本地主机UDP端口`8888`上启动，且没有附加的命名空间。

提供了环境变量来覆盖部分[UXRCE-DDS参数](../advanced_config/parameter_reference.md#uxrce-dds-client)。
这些变量允许用户为他们的模拟创建自定义启动文件：

- `PX4_UXRCE_DDS_NS`: 用于指定主题[命名空间](#自定义命名空间)。
- `ROS_DOMAIN_ID`: 用于替代[UXRCE_DDS_DOM_ID](../advanced_config/parameter_reference.md#UXRCE_DDS_DOM_ID)。
- `PX4_UXRCE_DDS_PORT`: 用于替代[UXRCE_DDS_PRT](../advanced_config/parameter_reference.md#UXRCE_DDS_PRT)。

例如，以下命令可用于在Gazebo模拟中启动客户端，使用DDS域`3`、端口`9999`和主题命名空间`drone`。

```sh
ROS_DOMAIN_ID=3 PX4_UXRCE_DDS_PORT=9999 PX4_UXRCE_DDS_NS=drone make px4_sitl gz_x500
```

## 支持的 uORB 消息

通过客户端暴露的[PX4 uORB主题](../msg_docs/index.md)集合在[dds_topics.yaml](../middleware/dds_topics.md)中设置。

这些主题是特定于版本的（在构建时编译到[uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client)中）。
虽然大多数版本应该支持非常相似的消息集合，但要确保这一点需要检查你特定版本的yaml文件。

请注意，ROS 2/DDS 需要与用于创建 PX4 固件中 uXRCE-DDS 客户端模块时使用的 _相同_ 消息定义才能解析消息。
消息定义存储在 ROS 2 接口包[PX4/px4_msgs](https://github.com/PX4/px4_msgs)中，并且它们会在 CI 上自动同步到 `main` 和发布分支。
请注意，PX4 源代码中的所有消息都存在于存储库中，但只有在 `dds_topics.yaml` 中列出的消息才会作为 ROS 2 主题可用。
因此，

- 如果你使用的是 main 或发布版本的 PX4，可以通过克隆接口包[PX4/px4_msgs](https://github.com/PX4/px4_msgs)到你的工作空间来获取消息定义。
- 如果你正在创建或修改 uORB 消息，必须手动从你的 PX4 源代码树中更新工作空间中的消息。
  通常这意味着你需要更新[dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)，克隆接口包，然后通过将新的/修改的消息定义从[PX4-Autopilot/msg](https://github.com/PX4/PX4-Autopilot/tree/main/msg)复制到其 `msg` 文件夹来手动同步。
  假设 PX4-Autopilot 在你的主目录 `~` 中，而 `px4_msgs` 在 `~/px4_ros_com/src/` 中，那么命令可能是：

  ```sh
  rm ~/px4_ros_com/src/px4_msgs/msg/*.msg
  cp ~/PX4-Autopilot/msg/*.msg ~/px4_ros_com/src/px4_msgs/msg/
  ```

  ::: info
  从技术上讲，[dds_topics.yaml](../middleware/dds_topics.md)完全定义了 PX4 uORB 主题和 ROS 2 消息之间的关系。
  有关更多信息，请参见下面的[DDS Topics YAML](#DDS主题YAML)。
  :::

## 自定义命名空间

可以在构建时([dds_topics.yaml](../middleware/dds_topics.md))或运行时（适用于多机体操作）应用自定义主题和服务命名空间：

- 一种方法是在命令行启动[uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client)时使用`-n`选项。该方法既适用于仿真也适用于真实机体。
- 可通过设置环境变量`PX4_UXRCE_DDS_NS`为仿真（仅限）提供自定义命名空间，需在启动仿真前设置该环境变量。

::: info
在运行时更改命名空间会将所需的命名空间作为前缀添加到[dds_topics.yaml](../middleware/dds_topics.md)中所有`topic`字段以及所有[服务服务器](#dds-ros-2-services)中。因此：

```sh
uxrce_dds_client start -n uav_1
```

或

```sh
PX4_UXRCE_DDS_NS=uav_1 make px4_sitl gz_x500
```

将生成以下命名空间下的主题：

```sh
/uav_1/fmu/in/  # 用于订阅者
/uav_1/fmu/out/ # 用于发布者
```

:::

## PX4 ROS 2 QoS设置

PX4的发布者QoS设置与ROS 2订阅者的默认QoS设置不兼容。因此，如果ROS 2代码需要订阅一个uORB主题，则需要使用兼容的QoS设置。其中一个示例请参见[ROS 2用户指南 > ROS 2订阅者QoS设置](../ros2/user_guide.md#ros-2-subscriber-qos-settings)。

PX4的发布者使用以下QoS设置：

```cpp
uxrQoS_t qos = {
  .durability = UXR_DURABILITY_TRANSIENT_LOCAL,
  .reliability = UXR_RELIABILITY_BEST_EFFORT,
  .history = UXR_HISTORY_KEEP_LAST,
  .depth = 0,
};
```

PX4的订阅者使用以下QoS设置：

```cpp
uxrQoS_t qos = {
  .durability = UXR_DURABILITY_VOLATILE,
  .reliability = UXR_RELIABILITY_BEST_EFFORT,
  .history = UXR_HISTORY_KEEP_LAST,
  .depth = queue_depth,
};
```

ROS 2默认对发布者和订阅者使用以下QoS设置：历史记录为"keep last"且队列大小为10，可靠性为"reliable"，持久性为"volatile"，活跃性为"system default"。截止时间、生命周期和租约持续时间也全部设置为"default"。

<!-- From https://github.com/PX4/PX4-user_guide/pull/2259#discussion_r1099788316 -->

## DDS主题YAML

PX4的[dds_topics.yaml](../middleware/dds_topics.md)文件定义了固件中构建并发布的PX4 uORB主题集合。更准确地说，它完全定义了PX4 uORB与ROS 2消息之间的关系/配对。

该文件的结构如下：

```yaml
publications:

  - topic: /fmu/out/collision_constraints
    type: px4_msgs::msg::CollisionConstraints
    rate_limit: 50.  # 限制最大发布速率为50 Hz

  ...

  - topic: /fmu/out/vehicle_odometry
    type: px4_msgs::msg::VehicleOdometry
    # 使用默认的最大发布速率限制100 Hz

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

1. 一个新的`publication`、`subscription`或`subscriptions_multi`，具体取决于其添加的列表。
2. 主题的_基础名称_，**必须**与您希望发布/订阅的所需uORB主题名称一致。它由`topic:`中以`/`开头且不包含其他`/`的最后一个标记确定。`vehicle_odometry`、`vehicle_status`和`offboard_control_mode`是基础名称的示例。
3. 主题的[命名空间](https://design.ros2.org/articles/topic_and_service_names.html#namespaces)。默认情况下设置为：
   - `/fmu/out/`用于PX4_发布_的主题。
   - `/fmu/in/`用于PX4_订阅_的主题。
4. 消息类型（如`VehicleOdometry`、`VehicleStatus`、`OffboardControlMode`等）以及预期提供消息定义的ROS 2包（如`px4_msgs`）。
5. **（可选）**：额外的`rate_limit`字段（仅适用于发布条目），指定PX4向ROS 2发布此主题消息的最大速率（Hz）。若未指定，默认最大发布速率限制为100 Hz。

`subscriptions`和`subscriptions_multi`允许我们选择ROS 2主题路由到的uORB主题实例：可以是与内部PX4 uORB发布者共享的实例，或者是专门保留给ROS2发布的独立实例。如果没有此机制，所有ROS 2消息将被路由到_相同_的uORB主题实例（因为ROS 2没有[uORB多实例](../middleware/uorb.md#multi-instance)的概念），PX4订阅者将无法区分来自ROS 2还是PX4发布者的消息流。

将主题添加到`subscriptions`部分以：

- 创建从ROS2主题到关联uORB主题_默认实例_（实例0）的单向路由。例如，它会创建ROS2订阅者`/fmu/in/vehicle_odometry`和uORB发布者`vehicle_odometry`。
- 如果其他（内部）PX4模块已经在同一uORB主题实例上发布，则该实例的订阅者将接收所有消息流。uORB订阅者将无法确定传入消息是由PX4还是ROS2发布的。
- 这是当预期ROS2发布者是该主题实例的唯一发布者（例如在外部控制期间替换内部发布者）或多个发布源不重要的情况下的期望行为。

将主题添加到`subscriptions_multi`部分以：

- 创建从ROS2主题到关联uORB主题_新实例_的单向路由。例如，如果`vehicle_odometry`已有`2`个实例，则会创建ROS2订阅者`/fmu/in/vehicle_odometry`和uORB发布者在`vehicle_odometry`的实例`3`上。
- 这确保了没有其他内部PX4模块会发布到uXRCE-DDS使用的相同实例。订阅者可以订阅所需的实例并区分发布者。
- 请注意，这仅保证PX4和ROS2发布者之间的隔离，不保证多个ROS2发布者之间的隔离。在这种情况下，它们的消息仍将被路由到相同实例。
- 例如，当您希望PX4记录两个相同传感器的读数时，这是期望行为；它们将在同一主题上发布，但一个使用实例0，另一个使用实例1。

您可以随意更改此配置。例如，您可以使用不同的默认命名空间，或使用自定义包来存储消息定义。

## DDS (ROS 2)服务

PX4 uXRCE-DDS中间件支持[ROS 2服务](https://docs.ros.org/en/jazzy/Concepts/Basic/About-Services.html)。
这些是节点间相互调用的远程过程调用，会返回结果。
它们通过将请求和响应行为分组，并确保回复仅返回给特定请求用户，从而简化ROS 2节点与PX4之间的通信。

服务服务器是接收远程过程请求、执行相关计算并返回结果的实体。
例如，定义在 [`px4_msgs::srv::VehicleCommand`](https://github.com/PX4/px4_msgs/blob/main/srv/VehicleCommand.srv) 中的`/fmu/vehicle_command`服务服务器，可以被ROS 2应用调用以向PX4发送[VehicleCommand](../msg_docs/VehicleCommand.md) uORB消息，并接收PX4的[VehicleCommandAck](../msg_docs/VehicleCommandAck.md) uORB消息作为响应。

关于服务列表、详细说明和示例，请参见ROS 2用户指南中的[服务文档](../ros2/user_guide.md#px4-ros-2-service-servers)。

## Fast-RTPS 到 uXRCE-DDS 迁移指南

本指南说明如何从 PX4 v1.13 [Fast-RTPS](../middleware/micrortps.md) 中间件迁移至 PX4 v1.14 `uXRCE-DDS` 中间件。  
如果你使用了 [为 PX4 v1.13 编写的 ROS 2 应用程序](https://docs.px4.io/v1.13/en/ros/ros2_comm.html)，或通过 Fast-RTPS 直接将应用程序与 PX4 接口连接 [直接方式](https://docs.px4.io/v1.13/en/middleware/micrortps.html#agent-in-an-offboard-fast-dds-interface-ros-independent)，这些内容将对你有所帮助。

::: info
本节包含特定于迁移的信息。  
你应阅读本页面的其余部分以充分理解 uXRCE-DDS。
:::

#### 依赖项无需移除

uXRCE-DDS 无需使用 Fast-RTPS 所需的依赖项（例如按照主题 [Fast DDS 安装](https://docs.px4.io/v1.13/en/dev_setup/fast-dds-installation.html) 安装的那些）。
如果你想保留它们也不会影响 uXRCE-DDS 应用程序的运行。

如果选择移除依赖项，请注意不要删除应用程序正在使用的组件（例如 Java）。

#### 已移除 `_rtps` 目标

任何之前使用带有 `_rtps` 扩展名的构建目标的地方（例如 `px4_fmu-v5_rtps` 或 `px4_sitl_rtps`），现在都可以使用对应的默认目标（在这些情况下为 `px4_fmu-v5_default` 和 `px4_sitl_default`）。

带有 `_rtps` 扩展名的 make 目标曾用于构建包含客户端 RTPS 代码的固件。  
uXRCE-DDS 中间件已默认包含在大多数开发板的构建中，因此您不再需要特殊固件来与 ROS 2 配合使用。

要检查您的开发板是否包含中间件，请在开发板的 `.px4board` 文件中查找 `CONFIG_MODULES_UXRCE_DDS_CLIENT=y`。  
这些文件位于 [PX4-Autopilot/boards](https://github.com/PX4/PX4-Autopilot/tree/main/boards) 目录下。

如果该配置项不存在，或者被设置为 `n`，则需要克隆 PX4 仓库，修改开发板配置并手动 [编译](../dev_setup/building_px4.md) 固件。

#### 新客户端模块和新启动参数

由于客户端由新的PX4模块实现，现在有了新的参数来启动它。  
请查看[客户端启动部分](#启动客户端)以了解具体操作方式。

#### 用于设置发布主题的新文件

特定固件中发布的和订阅的主题列表现在由[dds_topics.yaml](../middleware/dds_topics.md)配置文件管理，该文件取代了[urtps_bridge_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/release/1.13/msg/tools/urtps_bridge_topics.yaml)

有关更多信息，请参阅[Supported uORB Messages](#支持的 uORB 消息)和[DDS Topics YAML](#DDS主题YAML)部分。

#### 客户端和代理之间的话题无需再同步

在ROS 2中，代理和客户端之间的桥接话题列表无需再同步，因此`update_px4_ros2_bridge.sh`脚本不再需要。

#### 默认主题命名约定已更改

主题命名格式已更改：

- 发布主题：`/fmu/topic-name/out`（Fast-RTPS）更改为`/fmu/out/topic-name`（XRCE-DDS）。
- 订阅主题：`/fmu/topic-name/in`（Fast-RTPS）更改为`/fmu/in/topic-name`（XRCE-DDS）。

您应将应用程序更新为新约定。

::: info
您还可以编辑 [dds_topic.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 以恢复到旧的约定。
不建议这样做，因为这意味着您将不得不始终使用自定义固件。
:::

#### XRCE-DDS代理

XRCE-DDS代理是"通用的"且与PX4无关：[micro-xrce-dds-agent](https://micro-xrce-dds.docs.eprosima.com/en/latest/agent.html)。
在您的PC/机载计算机上有多种安装方式 - 有关更多信息请参见[专用部分](#Micro XRCE-DDS 代理安装)。

#### 应用特定更改

如果您没有将ROS 2与代理([快速DDS接口（ROS独立）](https://docs.px4.io/v1.13/en/middleware/micrortps.html#agent-in-an-offboard-fast-dds-interface-ros-independent))一起使用，则需要迁移到[eProsima Fast DDS](https://fast-dds.docs.eprosima.com/en/latest/index.html)。

ROS 2应用程序仍需要与PX4消息一起编译，您可以通过将[px4_msgs](https://github.com/PX4/px4_msgs)包添加到工作区来实现。
您可以移除[px4_ros_com](https://github.com/PX4/px4_ros_com)包，因为它不再需要，除非用于示例代码。

在您的ROS 2节点中，您将需要：

- 更新您的发布者和订阅者的[QoS](#PX4 ROS 2 QoS设置)，因为PX4不使用ROS 2的默认设置。
- 更改您的主题名称，除非您编辑过[dds_topic.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)。
- 移除与时间同步相关的一切内容，因为XRCE-DDS会自动处理代理/客户端的时间同步。

  在C++应用程序中，您可以按如下方式设置消息的`timestamp`字段：

  ```cpp
  msg.timestamp = this->get_clock()->now().nanoseconds() / 1000;
  ```

  在Python应用程序中，您可以按如下方式设置消息的`timestamp`字段：

  ```python
  msg.timestamp = int(self.get_clock().now().nanoseconds / 1000)
  ```

## 有用的资源

- [ROS 2 在 PX4 中的应用：无缝过渡到 XRCE-DDS 的技术细节](https://www.youtube.com/watch?v=F5oelooT67E) - Pablo Garrido & Nuno Marques (youtube)
- [PX4 ROS 2 机外控制教程](https://gist.github.com/julianoes/adbf76408663829cd9aed8d14c88fa29) (Github gist - JulianOes)
- [ROS 2 PX4 机外控制教程](https://github.com/Jaeyoung-Lim/px4-offboard/blob/2d784532fd323505ac8a6e53bb70145600d367c4/doc/ROS2_PX4_Offboard_Tutorial.md) (Jaeyoung-Lim)

<!---
Some of this might be useful.
I'd like to see a real example first.
-->

## 使用真实硬件设置桥接

本节内容正在进行中。

## 故障排除

### 客户端报告所选UART端口繁忙

如果所选UART端口繁忙，可能是因为MAVLink应用程序已经在使用中。  
如果同时需要MAVLink和RTPS连接，您必须将连接迁移到其他端口，或使用为PX4和协处理器提供的可用协议分割器。

:::tip
在开发期间快速/临时的解决方法是通过*NuttShell*停止MAVLink：  
```sh
mavlink stop-all
```
:::

### 在伴飞计算机上启用UART

对于Raspberry Pi或其他伴飞计算机的UART传输，您需要启用串口：

1. 确保用户ID（默认在Raspberry Pi上为pi）是dialout组的成员：

   ```sh
   groups pi
   sudo usermod -a -G dialout pi
   ```
1. 特别对于Raspberry Pi，您需要停止使用该端口的GPIO串口控制台：

   ```sh
   sudo raspi-config
   ```

   在显示的菜单中选择 **Interfacing options > Serial**。
   对于 *Would you like a login shell to be accessible over serial?* 选择 **NO**。确认后重启。
1. 检查内核中的UART配置：

   ```sh
   sudo vi /boot/config.txt
   ```

   并确保`enable_uart`的值设置为1：
   ```
    enable_uart=1
   ```
-->