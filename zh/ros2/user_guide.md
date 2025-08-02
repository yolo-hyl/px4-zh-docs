

# ROS 2 用户指南

ROS 2-PX4架构在ROS 2和PX4之间提供了深度集成，允许ROS 2的订阅者或发布者节点直接与PX4的uORB主题进行接口。

本主题概述了该架构和应用流程，并说明如何在PX4中设置和使用ROS 2。

::: info
从PX4 v1.14开始，ROS 2使用[uXRCE-DDS](../middleware/uxrce_dds.md)中间件，取代了1.13版本中使用的_FastRTPS_中间件（v1.13不支持uXRCE-DDS）。

[migration guide](../middleware/uxrce_dds.md#fast-rtps-to-uxrce-dds-migration-guidelines)说明了如何将ROS 2应用从PX4 v1.13迁移到v1.14所需的操作。

如果仍在使用PX4 v1.13，请参考[PX4 v1.13文档](https://docs.px4.io/v1.13/en/ros/ros2_comm.html)中的说明。

<!-- remove this when there are PX4 v1.14 docs for some months -->

:::

## 概览

由于[uXRCE-DDS](../middleware/uxrce_dds.md)通信中间件的使用，ROS 2在PX4上的应用流程非常直接。

![uXRCE-DDS与ROS 2的架构图](../../assets/middleware/xrce_dds/architecture_xrce-dds_ros2.svg)

<!-- doc source: https://docs.google.com/drawings/d/1WcJOU-EcVOZRPQwNzMEKJecShii2G4U3yhA3U6C4EhE/edit?usp=sharing -->

uXRCE-DDS中间件由运行在PX4上的客户端和运行在伴随计算机上的代理组成，它们通过串口、UDP、TCP或自定义链路进行双向数据交换。代理作为客户端的代理，用于在全局DDS数据空间中发布和订阅主题。

PX4的[uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client)在构建时生成，默认包含在PX4固件中。它既包含通用的micro XRCE-DDS客户端代码，也包含PX4特有的转换代码，用于与uORB主题进行数据收发。生成到客户端的uORB消息子集列在[PX4-Autopilot/src/modules/uxrce_dds_client/dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)中。生成器使用源码树中的uORB消息定义：[PX4-Autopilot/msg](https://github.com/PX4/PX4-Autopilot/tree/main/msg)来创建发送ROS 2消息的代码。

ROS 2应用程序需要在具有与PX4固件中uXRCE-DDS客户端模块相同消息定义的工作空间中构建。可以通过将接口包[PX4/px4_msgs](https://github.com/PX4/px4_msgs)克隆到ROS 2工作空间中来包含这些定义（仓库中的分支对应不同PX4版本的消息）。

从引入[消息版本控制](../middleware/uorb.md#message-versioning)的PX4 v1.16（main）开始，ROS 2应用程序可能会使用与构建PX4时不同的消息定义版本。这需要运行[ROS 2消息转换节点](../ros2/px4_ros2_msg_translation_node.md)以确保消息能正确转换和交换。

请注意，micro XRCE-DDS _代理_本身不依赖于客户端代码。它可以从[源代码](https://github.com/eProsima/Micro-XRCE-DDS-Agent)单独构建或作为ROS构建的一部分构建，或以snap包形式安装。

使用ROS 2时通常需要同时启动客户端和代理。注意uXRCE-DDS客户端默认已构建到固件中，但除模拟器构建外不会自动启动。

::: info
在PX4v1.13及更早版本中，ROS 2依赖于[px4_ros_com](https://github.com/PX4/px4_ros_com)中的定义。该仓库已不再需要，但包含有用的示例。
:::

## 安装与设置

PX4 配合 ROS 2 使用时，官方支持并推荐的平台是运行在 Ubuntu 22.04 上的 ROS 2 "Humble" LTS 版本。

::: 提示
如果您使用的是 Ubuntu 20.04，我们建议您升级到 Ubuntu 22.04。  
在此期间您可以在 Ubuntu 20.04 上使用 ROS 2 "Foxy" 配合 [Gazebo Classic](../sim_gazebo_classic/index.md)。  
请注意，ROS 2 "Foxy" 于 2023 年 5 月已结束支持，但（截至本文撰写时）仍保持稳定且可与 PX4 配合使用。  
:::

要设置用于 PX4 的 ROS 2，请执行以下步骤：  
- [安装 PX4](#安装 PX4)（用于运行 PX4 仿真器）  
- [安装 ROS 2](#安装 ROS 2)  
- [配置 Micro XRCE-DDS Agent & 客户端](#setup-micro-xrce-dds-agent-client)  
- [构建并运行 ROS 2 工作空间](#构建 ROS 2 工作空间)  

架构中自动安装的其他依赖项（如 _Fast DDS_）不在本文讨论范围内。

### 安装 PX4

为了使用模拟器，您需要安装PX4开发工具链。

::: info
ROS 2对PX4的唯一依赖是消息定义集，它从[px4_msgs](https://github.com/PX4/px4_msgs)获取。
只有在需要模拟器（如本指南所述）或构建发布自定义uORB话题的版本时，才需要安装PX4。
:::

在Ubuntu上以常规方式设置PX4开发环境：

```sh
cd
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
cd PX4-Autopilot/
make px4_sitl
```

请注意，上述命令将为您的Ubuntu版本安装推荐的模拟器。
如果您想安装PX4但保留现有模拟器安装，运行上述`ubuntu.sh`命令时添加`--no-sim-tools`标志。

更多信息和故障排查请参见：[Ubuntu开发环境](../dev_setup/dev_env_linux_ubuntu.md)和[下载PX4源代码](../dev_setup/building_px4.md)。

### 安装 ROS 2

要安装 ROS 2 及其依赖项：

1. 安装 ROS 2。

   :::: tabs

   ::: tab humble
   要在 Ubuntu 22.04 上安装 ROS 2 "Humble" 版本：

   ```sh
   sudo apt update && sudo apt install locales
   sudo locale-gen en_US en_US.UTF-8
   sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
   export LANG=en_US.UTF-8
   sudo apt install software-properties-common
   sudo add-apt-repository universe
   sudo apt update && sudo apt install curl -y
   sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
   sudo apt update && sudo apt upgrade -y
   sudo apt install ros-humble-desktop
   sudo apt install ros-dev-tools
   source /opt/ros/humble/setup.bash && echo "source /opt/ros/humble/setup.bash" >> .bashrc
   ```

   上述指令摘自官方安装指南：[Install ROS 2 Humble](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html)。
   您可以选择安装桌面版 (`ros-humble-desktop`) 或精简版 (`ros-humble-ros-base`)，以及开发工具 (`ros-dev-tools`)。
   :::

   ::: tab foxy
   要在 Ubuntu 20.04 上安装 ROS 2 "Foxy" 版本：

   - 参照官方安装指南：[Install ROS 2 Foxy](https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html)。

   您可以选择安装桌面版 (`ros-foxy-desktop`) 或精简版 (`ros-foxy-ros-base`)，以及开发工具 (`ros-dev-tools`)。
   :::

   ::::

1. 还需要安装一些 Python 依赖项（使用 **`pip`** 或 **`apt`**）：

   ```sh
   pip install --user -U empy==3.3.4 pyros-genmsg setuptools
   ```

### 设置 Micro XRCE-DDS 代理和客户端

为了让 ROS 2 与 PX4 通信，[uXRCE-DDS client](../modules/modules_system.md#uxrce-dds-client) 必须在 PX4 上运行，并连接到运行在协处理器上的 micro XRCE-DDS 代理。

#### 设置代理

代理可以通过多种方式安装到协处理器上，详见[multiple ways](../middleware/uxrce_dds.md#micro-xrce-dds-agent-installation)。  
以下展示如何从源代码构建独立的代理，并连接到运行在 PX4 模拟器上的客户端。

要设置并启动代理：

1. 打开终端。
1. 输入以下命令从源代码获取并构建代理：

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

1. 使用以下设置启动代理以连接到模拟器上的 uXRCE-DDS 客户端：

   ```sh
   MicroXRCEAgent udp4 -p 8888
   ```

代理现在正在运行，但直到我们启动 PX4（下一步）之前不会看到明显输出。

::: info
你可以让代理在该终端中持续运行！  
注意每个连接通道只允许一个代理。
:::

#### 启动客户端

PX4 模拟器会自动启动 uXRCE-DDS 客户端，并连接到本地主机的 UDP 8888 端口。

要启动模拟器（和客户端）：

1. 在上方安装的 **PX4 Autopilot** 仓库根目录打开新终端。

   :::: tabs

   ::: tab humble

   - 使用以下命令启动 PX4 [Gazebo](../sim_gazebo_gz/index.md) 模拟：

     ```sh
     make px4_sitl gz_x500
     ```

     :::

   ::: tab foxy

   - 使用以下命令启动 PX4 [Gazebo Classic](../sim_gazebo_classic/index.md) 模拟：

     ```sh
     make px4_sitl gazebo-classic
     ```

     :::

   ::::

代理和客户端现在正在运行，它们应该会建立连接。

PX4 终端会显示 [NuttShell/PX4 System Console](../debug/system_console.md) 的启动输出。  
一旦代理连接，输出应包含显示数据写入器创建成功的 `INFO` 消息：

```sh
...
INFO  [uxrce_dds_client] synchronized with time offset 1675929429203524us
INFO  [uxrce_dds_client] successfully created rt/fmu/out/failsafe_flags data writer, topic id: 83
INFO  [uxrce_dds_client] successfully created rt/fmu/out/sensor_combined data writer, topic id: 168
INFO  [uxrce_dds_client] successfully created rt/fmu/out/timesync_status data writer, topic id: 188
...
```

micro XRCE-DDS 代理终端也会开始显示输出，当在 DDS 网络中创建等效主题时：

```sh
...
[1675929445.268957] info     | ProxyClient.cpp    | create_publisher         | publisher created      | client_key: 0x00000001, publisher_id: 0x0DA(3), participant_id: 0x001(1)
[1675929445.269521] info     | ProxyClient.cpp    | create_datawriter        | datawriter created     | client_key: 0x00000001, datawriter_id: 0x0DA(5), publisher_id: 0x0DA(3)
[1675929445.270412] info     | ProxyClient.cpp    | create_topic             | topic created          | client_key: 0x00000001, topic_id: 0x0DF(2), participant_id: 0x001(1)
...
```

### 构建 ROS 2 工作空间

本节展示如何在您的主目录中创建一个 ROS 2 工作空间（根据需要修改命令以将源代码放置在其他位置）。

[px4_ros_com](https://github.com/PX4/px4_ros_com) 和 [px4_msgs](https://github.com/PX4/px4_msgs) 包被克隆到工作空间文件夹中，然后使用 `colcon` 工具构建工作空间。示例通过 `ros2 launch` 运行。

您应该使用与上一步中安装的 PX4 固件具有相同消息定义的 px4*msgs 包版本。  
px4_msgs 仓库的分支名称与不同 PX4 版本的消息定义对应。  
如果由于任何原因无法确保 PX4 固件与 ROS 2 px4_msgs 包之间的消息定义一致，您还需要在设置过程中[启动消息转换节点](#optional-starting-the-translation-node)。

::: info
示例构建了位于 [px4_ros_com](https://github.com/PX4/px4_ros_com) 的 [ROS 2 监听器](#ROS 2 监听器) 示例应用程序。  
[px4_msgs](https://github.com/PX4/px4_msgs) 也是必需的，以便示例能够解析 PX4 ROS 2 话题。
:::

#### 构建工作空间

要创建和构建工作空间：

1. 打开一个新终端。
1. 使用以下命令创建并进入一个新的工作空间目录：

   ```sh
   mkdir -p ~/ws_sensor_combined/src/
   cd ~/ws_sensor_combined/src/
   ```

   ::: info
   为工作空间文件夹命名可以更方便地管理工作空间。
   :::

1. 将示例仓库和 [px4_msgs](https://github.com/PX4/px4_msgs) 克隆到 `/src` 目录（默认克隆 `main` 分支，对应当前运行的 PX4 版本）：

   ```sh
   git clone https://github.com/PX4/px4_msgs.git
   git clone https://github.com/PX4/px4_ros_com.git
   ```

1. 将 ROS 2 开发环境源代码引入当前终端，并使用 `colcon` 编译工作空间：

   :::: tabs

   ::: tab humble

   ```sh
   cd ..
   source /opt/ros/humble/setup.bash
   colcon build
   ```

   :::

   ::: tab foxy

   ```sh
   cd ..
   source /opt/ros/foxy/setup.bash
   colcon build
   ```

   :::

   ::::

   这将使用源工具链编译 `/src` 下的所有文件夹。

#### 运行示例

要运行刚刚构建的可执行文件，需要源代码 `local_setup.bash`。  
这会提供当前工作空间的"环境钩子"访问权限。换句话说，它使刚刚构建的可执行文件在当前终端中可用。

::: info
[ROS2 入门教程](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Creating-A-Workspace/Creating-A-Workspace.html#source-the-overlay) 建议您为运行可执行文件_打开一个新终端_。
:::

在新终端中：

1. 进入工作空间目录的顶层并源代码 ROS 2 环境（此处为 "Humble"）：

   :::: tabs

   ::: tab humble

   ```sh
   cd ~/ws_sensor_combined/
   source /opt/ros/humble/setup.bash
   ```

   :::

   ::: tab foxy

   ```sh
   cd ~/ws_sensor_combined/
   source /opt/ros/foxy/setup.bash
   ```

   :::

   ::::

1. 源代码 `local_setup.bash`：

   ```sh
   source install/local_setup.bash
   ```

1. 现在启动示例。注意此处使用了 `ros2 launch`，如下所述：

   ```sh
   ros2 launch px4_ros_com sensor_combined_listener.launch.py
   ```

如果运行正常，您应该在启动 ROS 监听器的终端/控制台中看到数据输出：

```sh
RECEIVED DATA FROM SENSOR COMBINED
================================
ts: 870938190
gyro_rad[0]: 0.00341645
gyro_rad[1]: 0.00626475
gyro_rad[2]: -0.000515705
gyro_integral_dt: 4739
accelerometer_timestamp_relative: 0
accelerometer_m_s2[0]: -0.273381
accelerometer_m_s2[1]: 0.0949186
accelerometer_m_s2[2]: -9.76044
accelerometer_integral_dt: 4739
```

#### (可选) 启动转换节点

<Badge type="tip" text="main (planned for: PX4 v1.16+)" /> <Badge type="tip" />  <Badge type="warning" text="Experimental" />

此示例使用与 PX4 和 ROS2 版本相同的定义构建。  
如果使用不兼容的 [消息版本](../middleware/uorb.md#message-versioning)，则需要先安装和运行 [消息转换节点](./px4_ros2_msg_translation_node.md)，然后再运行示例：

1. 通过运行以下脚本将 [消息转换节点](../ros2/px4_ros2_msg_translation_node.md) 包含到示例工作空间或独立工作空间中：

   ```sh
   cd /path/to/ros_ws
   /path/to/PX4-Autopilot/Tools/copy_to_ros_ws.sh .
   ```

1. 构建并运行转换节点：

   ```sh
   colcon build
   source install/local_setup.bash
   ros2 run translation_node translation_node_bin
   ```

## 控制机体

要控制应用，ROS 2 应用程序：

- 订阅（监听）由 PX4 发布的遥测主题  
- 发布主题以触发 PX4 执行特定操作  

可用的主题在 [dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 中定义，更多数据信息可参考 [uORB 消息参考](../msg_docs/index.md)。  
例如，[VehicleGlobalPosition](../msg_docs/VehicleGlobalPosition.md) 可用于获取机体全局位置，[VehicleCommand](../msg_docs/VehicleCommand.md) 可用于发送起飞/降落等指令。  

下方的 [ROS 2 示例应用](#ROS 2 示例应用) 提供了使用这些主题的具体示例。

## 兼容性问题

本节包含可能会影响您编写ROS代码的信息。

### ROS 2 订阅者 QoS 设置

订阅 PX4 发布主题的 ROS 2 代码 _必须_ 指定合适的（兼容的）QoS 设置才能监听主题。  
具体而言，节点应使用 ROS 2 预定义的 QoS 传感器数据配置文件（来自 [监听器示例源代码](#ROS 2 监听器)）进行订阅：

```cpp
...
rmw_qos_profile_t qos_profile = rmw_qos_profile_sensor_data;
auto qos = rclcpp::QoS(rclcpp::QoSInitialization(qos_profile.history, 5), qos_profile);

subscription_ = this->create_subscription<px4_msgs::msg::SensorCombined>("/fmu/out/sensor_combined", qos,
...
```

这是必要的，因为 ROS 2 默认的 [服务质量（QoS）设置](https://docs.ros.org/en/humble/Concepts/About-Quality-of-Service-Settings.html#qos-profiles) 与 PX4 使用的设置不同。并非所有发布-订阅的 QoS 配置组合都可行（见 [QoS 兼容性](https://docs.ros.org/en/humble/Concepts/About-Quality-of-Service-Settings.html#qos-compatibilities)），而 ROS 2 的默认订阅设置恰好不可行！  
注意，ROS 代码在发布时无需设置 QoS（此时 PX4 设置与 ROS 默认值兼容）。

<!-- From https://github.com/PX4/PX4-user_guide/pull/2259#discussion_r1099788316 -->

### ROS 2 & PX4 坐标系约定

ROS和PX4使用的局部/世界坐标系与机体坐标系存在差异。

| 坐标系 | PX4                                              | ROS                                            |
| ----- | ------------------------------------------------ | ---------------------------------------------- |
| 机体  | FRD (X **前向**，Y **右**，Z **下**)     | FLU (X **前向**，Y **左**，Z **上**)      |
| 世界  | FRD 或 NED (X **北**，Y **东**，Z **下**) | FLU 或 ENU (X **东**，Y **北**，Z **上**) |

:::tip
更多信息请参见 [REP105: Coordinate Frames for Mobile Platforms](http://www.ros.org/reps/rep-0105.html)
:::

下图显示了两种坐标系（左侧为FRD/右侧为FLU）。

![参考坐标系](../../assets/lpe/ref_frames.png)

除非在相关消息定义中明确说明，**所有** PX4主题都采用FRD (NED) 约定。因此，想要与PX4交互的ROS 2节点必须注意这些坐标系约定。

- 将向量从ENU转换到NED需要执行两次基础旋转：
  - 首先绕`Z`轴（向上）旋转π/2弧度，
  - 然后绕`X`轴（旧东轴/新北轴）旋转π弧度。

- 将向量从NED转换到ENU需要执行两次基础旋转：
  - 首先绕`Z`轴（向下）旋转π/2弧度，
  - 然后绕`X`轴（旧北轴/新东轴）旋转π弧度。注意这两个操作在数学上是等效的。

- 将向量从FLU转换到FRD只需绕`X`轴（前向）旋转π弧度。
- 将向量从FRD转换到FLU只需绕`X`轴（前向）旋转π弧度。

需要旋转的向量示例包括：

- [TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md) 消息中的所有字段；发送前需要执行从ENU到NED的转换。
- [VehicleThrustSetpoint](../msg_docs/VehicleThrustSetpoint.md) 消息中的所有字段；发送前需要执行从FLU到FRD的转换。

与向量类似，表示机体相对于世界坐标系姿态的四元数也需要转换。

[PX4/px4_ros_com](https://github.com/PX4/px4_ros_com) 提供共享库 [frame_transforms](https://github.com/PX4/px4_ros_com/blob/main/include/px4_ros_com/frame_transforms.h) 以方便执行此类转换。

### ROS、Gazebo 和 PX4 时间同步

默认情况下，ROS 2 与 PX4 之间的时间同步由 [uXRCE-DDS middleware](https://micro-xrce-dds.docs.eprosima.com/en/latest/time_sync.html) 自动管理，时间同步统计信息可通过监听桥接主题 `/fmu/out/timesync_status` 获取。当 uXRCE-DDS 客户端运行在飞控器上，而代理运行在伴飞计算机上时，这种行为是预期的，因为时间偏移量、时间漂移和通信延迟都会被计算并自动补偿。

在 Gazebo 仿真中，GZBridge 会在每次仿真步进时设置 PX4 时间（参见 [Change simulation speed](../sim_gazebo_gz/index.md#change-simulation-speed)）。这与 Gazebo Classic 采用的 [仿真锁步](../sim_gazebo_classic/index.md#lockstep) 流程不同。

ROS2 用户在节点的时间源选择上有两种可能（参见 [time source](https://design.ros2.org/articles/clock_and_time.html)）。

#### ROS2 节点使用操作系统时钟作为时间源

此场景是本文和 [offboard_control](./offboard_control.md) 指南中讨论的场景，也是 ROS2 节点的标准行为。操作系统时钟作为时间源，因此仅当仿真实时因子接近 1 时才能使用。uXRCE-DDS 客户端的时间同步器会在 ROS2 侧的操作系统时钟与 PX4 侧的 Gazebo 时钟之间建立桥接。用户无需进一步操作。

#### ROS2 节点使用 Gazebo 时钟作为时间源

在此场景中，ROS2 也使用 Gazebo 的 `/clock` 主题作为时间源。如果 Gazebo 仿真以非 1 的实时因子运行，或者 ROS2 需要直接与 Gazebo 交互时，这种方案是有意义的。在 ROS2 侧，通过 [ros_gz_bridge](https://github.com/gazebosim/ros_gz) 包实现与 Gazebo 的直接交互，该包位于 [ros_gz](https://github.com/gazebosim/ros_gz) 仓库中。

请使用以下命令为 PX4 支持的 ROS2 和 Gazebo 版本安装正确的 ROS2/gz 接口包（不仅仅是桥接）。

:::: tabs

::: tab humble
要安装适用于 ROS 2 "Humble" 和 Gazebo Harmonic（Ubuntu 22.04）的桥接包：

```sh
sudo apt install ros-humble-ros-gzharmonic
```

:::

::: tab foxy
首先需要 [安装 Gazebo Garden](../sim_gazebo_gz/index.md#installation-ubuntu-linux)，因为 Foxy 默认附带 Gazebo Classic 11。（注意：Garden 于 2024 年 11 月停止支持）

然后安装适用于 ROS 2 "Foxy" 和 Gazebo（Ubuntu 20.04）的接口包：

```sh
sudo apt install ros-foxy-ros-gzgarden
```

:::

::::

::: info
[仓库](https://github.com/gazebosim/ros_gz#readme) 和 [包](https://github.com/gazebosim/ros_gz/tree/ros2/ros_gz_bridge#readme) 的 README 会显示根据您的 ROS2 和 Gazebo 版本需要安装的包版本。
:::

安装并配置好包后，节点 `parameter_bridge` 提供桥接功能，可用于创建单向 `/clock` 桥接：

```sh
ros2 run ros_gz_bridge parameter_bridge /clock@rosgraph_msgs/msg/Clock[gz.msgs.Clock
```

此时，所有 ROS2 节点必须被指示使用新桥接的 `/clock` 主题作为时间源而非操作系统时钟，这通过将每个节点的参数 `use_sim_time` 设置为 `true` 来实现（参见 [ROS 时钟和时间设计](https://design.ros2.org/articles/clock_and_time.html)）。

ROS2 侧的修改到此完成。在 PX4 侧，只需通过将参数 [UXRCE_DDS_SYNCT](../advanced_config/parameter_reference.md#UXRCE_DDS_SYNCT) 设置为 `false` 来停止 uXRCE-DDS 时间同步。通过此操作，Gazebo 将作为 ROS2 和 PX4 的主要且唯一时间源。

## ROS 2 示例应用

### ROS 2 监听器

[PX4/px4_ros_com](https://github.com/PX4/px4_ros_com) 仓库中的 [ROS 2 监听器示例](https://github.com/PX4/px4_ros_com/tree/main/src/examples/listeners) 展示了如何编写 ROS 节点来监听 PX4 发布的主题。

这里以 `px4_ros_com/src/examples/listeners` 下的 [sensor_combined_listener.cpp](https://github.com/PX4/px4_ros_com/blob/main/src/examples/listeners/sensor_combined_listener.cpp) 节点为例，它订阅了 [SensorCombined](../msg_docs/SensorCombined.md) 消息。

::: info
[构建ROS 2工作空间](#构建 ROS 2 工作空间) 展示了如何构建并运行该示例
:::

代码首先导入与 ROS 2 中间件交互所需的 C++ 库，以及订阅 `SensorCombined` 消息的头文件：

```cpp
#include <rclcpp/rclcpp.hpp>
#include <px4_msgs/msg/sensor_combined.hpp>
```

然后创建继承自通用 `rclcpp::Node` 基类的 `SensorCombinedListener` 类：

```cpp
/**
 * @brief SensorCombined uORB 主题数据回调
 */
class SensorCombinedListener : public rclcpp::Node
{
```

该代码创建了当接收到 `SensorCombined` uORB 消息（现为 micro XRCE-DDS 消息）时的回调函数，并在每次接收到消息时输出消息字段内容。

```cpp
public:
  explicit SensorCombinedListener() : Node("sensor_combined_listener")
  {
    rmw_qos_profile_t qos_profile = rmw_qos_profile_sensor_data;
    auto qos = rclcpp::QoS(rclcpp::QoSInitialization(qos_profile.history, 5), qos_profile);

    subscription_ = this->create_subscription<px4_msgs::msg::SensorCombined>("/fmu/out/sensor_combined", qos,
    [this](const px4_msgs::msg::SensorCombined::UniquePtr msg) {
      std::cout << "\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n\n";
      std::cout << "RECEIVED SENSOR COMBINED DATA"   << std::endl;
      std::cout << "============================="   << std::endl;
      std::cout << "ts: "          << msg->timestamp    << std::endl;
      std::cout << "gyro_rad[0]: " << msg->gyro_rad[0]  << std::endl;
      std::cout << "gyro_rad[1]: " << msg->gyro_rad[1]  << std::endl;
      std::cout << "gyro_rad[2]: " << msg->gyro_rad[2]  << std::endl;
      std::cout << "gyro_integral_dt: " << msg->gyro_integral_dt << std::endl;
      std::cout << "accelerometer_timestamp_relative: " << msg->accelerometer_timestamp_relative << std::endl;
      std::cout << "accelerometer_m_s2[0]: " << msg->accelerometer_m_s2[0] << std::endl;
      std::cout << "accelerometer_m_s2[1]: " << msg->accelerometer_m_s2[1] << std::endl;
      std::cout << "accelerometer_m_s2[2]: " << msg->accelerometer_m_s2[2] << std::endl;
      std::cout << "accelerometer_integral_dt: " << msg->accelerometer_integral_dt << std::endl;
    });
  }
```

::: info
订阅设置了基于 `rmw_qos_profile_sensor_data` 的 QoS 配置文件。  
这是必需的，因为 ROS 2 订阅者的默认 QoS 配置文件与 PX4 发布者的配置文件不兼容。  
更多信息请参见：[ROS 2 订阅者 QoS 设置](#ROS 2 订阅者 QoS 设置)
:::

下方代码创建了对 `SensorCombined` uORB 主题的发布器，可以与一个或多个订阅 `fmu/sensor_combined/out` ROS 2 主题的兼容订阅者匹配。

````cpp
private:
 rclcpp::Subscription<px4_msgs::msg::SensorCombined>::SharedPtr subscription_;
};
```s

通过 `main` 函数将 `SensorCombinedListener` 类实例化为 ROS 节点：

```cpp
int main(int argc, char *argv[])
{
  std::cout << "Starting sensor_combined listener node..." << std::endl;
  setvbuf(stdout, NULL, _IONBF, BUFSIZ);
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<SensorCombinedListener>());

  rclcpp::shutdown();
  return 0;
}
````

该示例在 [launch/sensor_combined_listener.launch.py](https://github.com/PX4/px4_ros_com/blob/main/launch/sensor_combined_listener.launch.py) 中有对应的启动文件，允许通过 [`ros2 launch`](#ros2 launch) 命令启动。

### ROS 2 发布者

ROS 2 发布者节点将数据发布到 DDS/RTPS 网络（因此也发布到 PX4 飞行控制器）。

以 `px4_ros_com/src/advertisers` 下的 `debug_vect_advertiser.cpp` 为例，首先我们导入所需的头文件，包括 `debug_vect` 消息头文件。

```cpp
#include <chrono>
#include <rclcpp/rclcpp.hpp>
#include <px4_msgs/msg/debug_vect.hpp>

using namespace std::chrono_literals;
```

然后代码创建了一个继承自通用 `rclcpp::Node` 基类的 `DebugVectAdvertiser` 类。

```cpp
class DebugVectAdvertiser : public rclcpp::Node
{
```

以下代码为消息发送时的函数。
消息通过定时回调发送，该定时器每秒发送两次消息。

```cpp
public:
  DebugVectAdvertiser() : Node("debug_vect_advertiser") {
    publisher_ = this->create_publisher<px4_msgs::msg::DebugVect>("fmu/debug_vect/in", 10);
    auto timer_callback =
    [this]()->void {
      auto debug_vect = px4_msgs::msg::DebugVect();
      debug_vect.timestamp = std::chrono::time_point_cast<std::chrono::microseconds>(std::chrono::steady_clock::now()).time_since_epoch().count();
      std::string name = "test";
      std::copy(name.begin(), name.end(), debug_vect.name.begin());
      debug_vect.x = 1.0;
      debug_vect.y = 2.0;
      debug_vect.z = 3.0;
      RCLCPP_INFO(this->get_logger(), "\033[97m Publishing debug_vect: time: %llu x: %f y: %f z: %f \033[0m",
                                    debug_vect.timestamp, debug_vect.x, debug_vect.y, debug_vect.z);
      this->publisher_->publish(debug_vect);
    };
    timer_ = this->create_wall_timer(500ms, timer_callback);
  }

private:
  rclcpp::TimerBase::SharedPtr timer_;
  rclcpp::Publisher<px4_msgs::msg::DebugVect>::SharedPtr publisher_;
};
```

`DebugVectAdvertiser` 类作为 ROS 节点的实例化在 `main` 函数中完成。

```cpp
int main(int argc, char *argv[])
{
  std::cout << "Starting debug_vect advertiser node..." << std::endl;
  setvbuf(stdout, NULL, _IONBF, BUFSIZ);
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<DebugVectAdvertiser>());

  rclcpp::shutdown();
  return 0;
}
```

### 外部控制

[ROS 2 Offboard control example](../ros2/offboard_control.md) 提供了一个完整的 C++ 参考示例，演示如何通过 ROS2 使用 PX4 的 [offboard control](../flight_modes/offboard.md)。

[Python ROS2 offboard examples with PX4](https://github.com/Jaeyoung-Lim/px4-offboard) (Jaeyoung-Lim/px4-offboard) 为 Python 提供了类似示例，包含以下脚本：

- `offboard_control.py`：使用位置设定点的外部位置控制示例
- `visualizer.py`：用于在 Rviz 中可视化机体状态

## 使用飞行控制器硬件

在飞行控制器上运行的ROS 2和PX4与在模拟器上使用PX4几乎相同。唯一的区别是需要根据通信通道的设置同时启动代理和客户端。

更多信息请参见 [Starting uXRCE-DDS](../middleware/uxrce_dds.md#starting-agent-and-client)。

## 自定义 uORB 主题

ROS 2 必须具有与 PX4 固件中创建的 uXRCE-DDS 客户端模块使用的 _相同_ 消息定义，才能解析消息。
这些定义存储在 ROS 2 接口包 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 中，并通过 CI 在 `main` 和 release 分支上自动同步。
请注意，所有 PX4 源代码中的消息都存在于仓库中，但只有在 `dds_topics.yaml` 中列出的消息才会作为 ROS 2 主题可用。
因此，

- 如果您使用的是 main 或 release 版本的 PX4，可以通过将接口包 [PX4/px4_msgs](https://github.com/PX4/px4_msgs) 克隆到您的工作区中获取消息定义。
- 如果您要创建或修改 uORB 消息，必须从 PX4 源代码树手动更新工作区中的消息。
  通常这意味着您需要更新 [dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)，克隆接口包，然后通过将新/修改的消息定义从 [PX4-Autopilot/msg](https://github.com/PX4/PX4-Autopilot/tree/main/msg) 复制到其 `msg` 文件夹来手动同步。
  假设 PX4-Autopilot 在您的主目录 `~` 中，而 `px4_msgs` 在 `~/ros2_ws/src/` 中，则命令可能是：

  ```sh
  rm ~/ros2_ws/src/px4_msgs/msg/*.msg
  cp ~/PX4-Autopilot/mgs/*.msg ~/ros2_ws/src/px4_msgs/msg/
  ```

  ::: info
  从技术角度看，[dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 完全定义了 PX4 uORB 主题与 ROS 2 消息之间的关系。
  详情请参见 [uXRCE-DDS > DDS Topics YAML](../middleware/uxrce_dds.md#dds-topics-yaml)。
  :::

## 自定义命名空间

自定义主题和服务命名空间可以在构建时（修改 [dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)）或运行时（适用于多机体操作）应用：

- 一种方法是在通过命令行启动 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 时使用`-n`选项。  
  该技术既可用于仿真环境，也可用于真实机体。
- 对于仿真环境（仅限），可以通过在启动仿真前设置环境变量`PX4_UXRCE_DDS_NS`来提供自定义命名空间。

::: info
在运行时更改命名空间会将所需命名空间作为前缀追加到 [dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 中所有`topic`字段和所有 [服务服务器](#PX4 ROS 2 服务服务器)。  
因此，命令如：

```sh
uxr_dds_client start -n uav_1
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

## PX4 ROS 2 服务服务器

<Badge type="tip" text="PX4 v1.15" />

PX4 uXRCE-DDS 中间件支持 [ROS 2 服务](https://docs.ros.org/en/jazzy/Concepts/Basic/About-Services.html)。
服务是节点间通过远程过程调用实现的通信方式，能够返回执行结果。

服务服务器是接收远程过程请求的实体，执行计算后返回结果。
它们通过将请求和响应行为分组，并确保回复只发送给特定请求用户，简化了 ROS 2 节点与 PX4 之间的通信。
这比发布请求、订阅回复并过滤无关响应要简单得多。

集成在 PX4 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 模块中的服务服务器包括：

- `/fmu/vehicle_command` (定义: [`px4_msgs::srv::VehicleCommand`](https://github.com/PX4/px4_msgs/blob/main/srv/VehicleCommand.srv).)

  该服务可由 ROS 2 应用程序调用，用于发送 PX4 [VehicleCommand](../msg_docs/VehicleCommand.md) uORB 消息，并接收 PX4 [VehicleCommandAck](../msg_docs/VehicleCommandAck.md) uORB 消息作为响应。

所有 PX4 服务名称遵循 `{extra_namespace}/fmu/{server_specific_name}` 的命名规范，其中 `{extra_namespace}` 是与 PX4 主题相同的 [自定义命名空间](#自定义命名空间)。

具体细节和示例将在后续章节中提供。

### VehicleCommand 服务

该服务可用于向机体发送命令，例如"起飞"、"降落"、切换模式和"环绕飞行"，并接收响应。

服务类型在 [`px4_msgs::srv::VehicleCommand`](https://github.com/PX4/px4_msgs/blob/main/srv/VehicleCommand.srv) 中定义为：

```txt
VehicleCommand 请求
---
VehicleCommandAck 回复
```

用户可通过发送 [VehicleCommand](../msg_docs/VehicleCommand.md) 消息发起服务请求，并接收 [VehicleCommandAck](../msg_docs/VehicleCommandAck.md) 消息作为响应。该服务确保仅将用户发起的特定请求所对应的 `VehicleCommandAck` 回复返回。

#### VehicleCommand 服务离线控制示例

`px4_ros_com` 包中提供的 [offboard_control_srv](https://github.com/PX4/px4_ros_com/blob/main/src/examples/offboard/offboard_control_srv.cpp) 节点包含完整的 _离线控制_ 示例，使用 VehicleCommand 服务请求模式切换、机体上锁和解除上锁。

该示例与 [ROS 2 离线控制示例](../ros2/offboard_control.md) 中描述的 _离线控制_ 示例基本一致，但使用 `VehicleCommand` 服务来请求模式切换、机体上锁和解除上锁。

首先，ROS 2 应用程序通过 `rclcpp::Client()` 声明一个类型为 `px4_msgs::srv::VehicleCommand` 的服务客户端（这与所有 ROS2 服务客户端的实现方式相同）：

```cpp
rclcpp::Client<px4_msgs::srv::VehicleCommand>::SharedPtr vehicle_command_client_;
```

然后将客户端初始化到正确的 ROS 2 服务 (`/fmu/vehicle_command`)。由于应用程序假设使用了标准 PX4 命名空间，相关代码如下：

```cpp
vehicle_command_client_{this->create_client<px4_msgs::srv::VehicleCommand>("/fmu/vehicle_command")}
```

之后，客户端可用于发送任意机体命令请求。例如，`arm()` 函数用于请求机体上锁：

```cpp
void OffboardControl::arm()
{
  RCLCPP_INFO(this->get_logger(), "requesting arm");
  request_vehicle_command(VehicleCommand::VEHICLE_CMD_COMPONENT_ARM_DISARM, 1.0);
}
```

其中 `request_vehicle_command` 负责格式化请求并以 _异步_ [模式](https://docs.ros.org/en/humble/How-To-Guides/Sync-Vs-Async.html#asynchronous-calls) 发送：

```cpp
void OffboardControl::request_vehicle_command(uint16_t command, float param1, float param2)
{
  auto request = std::make_shared<px4_msgs::srv::VehicleCommand::Request>();

  VehicleCommand msg{};
  msg.param1 = param1;
  msg.param2 = param2;
  msg.command = command;
  msg.target_system = 1;
  msg.target_component = 1;
  msg.source_system = 1;
  msg.source_component = 1;
  msg.from_external = true;
  msg.timestamp = this->get_clock()->now().nanoseconds() / 1000;
  request->request = msg;

  service_done_ = false;
  auto result = vehicle_command_client_->async_send_request(request, std::bind(&OffboardControl::response_callback, this,
                           std::placeholders::_1));
  RCLCPP_INFO(this->get_logger(), "Command send");
}
```

最终通过 `response_callback` 方法异步捕获响应，并检查请求结果：

```cpp
void OffboardControl::response_callback(
      rclcpp::Client<px4_msgs::srv::VehicleCommand>::SharedFuture future) {
    auto status = future.wait_for(1s);
    if (status == std::future_status::ready) {
      auto reply = future.get()->reply;
      service_result_ = reply.result;
      // make decision based on service_result_
      service_done_ = true;
    } else {
      RCLCPP_INFO(this->get_logger(), "Service In-Progress...");
    }
  }
```

## ros2 CLI

[ros2 CLI](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools.html) 是一个用于ROS开发的实用工具。  
你可以用它快速检查主题是否正在发布，如果工作区中包含 `px4_msg`，还可以详细检查这些主题。  
该命令还允许通过启动文件启动更复杂的ROS系统。  
以下展示了一些可能性。

### ros2 topic list

使用 `ros2 topic list` 列出ROS 2可访问的主题：

```sh
ros2 topic list
```

如果PX4已连接到代理，结果将显示主题类型列表：

```sh
/fmu/in/obstacle_distance
/fmu/in/offboard_control_mode
/fmu/in/onboard_computer_status
...
```

注意工作区无需通过`px4_msgs`构建即可成功执行；主题类型信息是消息负载的一部分。

### ros2 topic echo

使用 `ros2 topic echo` 显示特定主题的详细信息。

与 `ros2 topic list` 不同，要使此命令正常工作，你必须在已构建 `px4_msgs` 并源了 `local_setup.bash` 的工作空间中，以便 ROS 可以解析消息。

```sh
ros2 topic echo /fmu/out/vehicle_status
```

该命令将随时间戳更新显示主题详情。

```sh
---
timestamp: 1675931593364359
armed_time: 0
takeoff_time: 0
arming_state: 1
latest_arming_reason: 0
latest_disarming_reason: 0
nav_state_timestamp: 3296000
nav_state_user_intention: 4
nav_state: 4
failure_detector_status: 0
hil_state: 0
...
---
```

### ros2 topic hz

您可以使用 `ros2 topic hz` 获取消息速率的统计信息。  
例如，要获取 `SensorCombined` 的速率：  

```sh
ros2 topic hz /fmu/out/sensor_combined
```

输出结果类似如下内容：

```sh
平均速率: 248.187
  最小: 0.000s 最大: 0.012s 标准差: 0.00147s 窗口: 2724
平均速率: 248.006
  最小: 0.000s 最大: 0.012s 标准差: 0.00147s 窗口: 2972
平均速率: 247.330
  最小: 0.000s 最大: 0.012s 标准差: 0.00148s 窗口: 3212
平均速率: 247.497
  最小: 0.000s 最大: 0.012s 标准差: 0.00149s 窗口: 3464
平均速率: 247.458
  最小: 0.000s 最大: 0.012s 标准差: 0.00149s 窗口: 3712
平均速率: 247.485
  最小: 0.000s 最大: 0.012s 标准差: 0.00148s 窗口: 3960
```

### ros2 launch

`ros2 launch` 命令用于启动一个 ROS 2 启动文件。  
例如，上面我们使用 `ros2 launch px4_ros_com sensor_combined_listener.launch.py` 来启动监听器示例。

你并不需要拥有启动文件，但如果有一个需要启动多个组件的复杂 ROS 2 系统，它们会非常有用。

关于启动文件的更多信息，请参见 [ROS 2 Tutorials > Creating launch files](https://docs.ros.org/en/humble/Tutorials/Intermediate/Launch/Creating-Launch-Files.html)

## 故障排查

### 缺失的依赖项

标准安装应包含ROS 2所需的所有工具。

如果缺少任何组件，可单独添加：

- **`colcon`** 构建工具应包含在开发工具中。
  可通过以下方式安装：

  ```sh
  sudo apt install python3-colcon-common-extensions
  ```

- 变换库使用的Eigen3库应在桌面版和基础版包中。
  安装方式如下：

  :::: tabs

  ::: tab humble

  ```sh
  sudo apt install ros-humble-eigen3-cmake-module
  ```

  :::

  ::: tab foxy

  ```sh
  sudo apt install ros-foxy-eigen3-cmake-module
  ```

  :::

  ::::

### ros_gz_bridge 未在 /clock 话题上发布信息

如果您的 [ROS2节点使用Gazebo时钟作为时间源](../ros2/user_guide.md#ros2-nodes-use-the-gazebo-clock-as-time-source)，但 `ros_gz_bridge` 节点未在 `/clock` 话题上发布任何内容，可能是您安装的版本不正确。  
这种情况可能发生在您安装了ROS 2 Humble默认的 "Ignition Fortress" 软件包，而非适用于PX4的软件包（PX4使用的是 "Gazebo Harmonic"）。

以下命令将卸载默认的Ignition Fortress话题，并安装适用于 **Gazebo Harmonic** 和 ROS2 **Humble** 的正确桥接器及其他接口话题：  

```bash

```

# 移除错误版本（适用于Ignition Fortress）
sudo apt remove ros-humble-ros-gz

# 安装 Gazebo Garden 的版本
sudo apt install ros-humble-ros-gzharmonic

## 附加信息

- [ROS 2在PX4中的应用：无缝迁移到XRCE-DDS的技术细节](https://www.youtube.com/watch?v=F5oelooT67E) - Pablo Garrido & Nuno Marques (youtube)
- [DDS和ROS中间件实现](https://github.com/ros2/ros2/wiki/DDS-and-ROS-middleware-implementations)