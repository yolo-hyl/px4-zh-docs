# 模拟器

模拟器允许PX4飞行代码在模拟的"世界"中控制计算机建模的机体。您可以像操作真实机体一样通过_QGroundControl_、离板API或遥控器/游戏手柄与该机体进行交互。

:::tip
模拟是测试PX4代码修改的一种快速、简便且最重要的是_安全_的方式，这比在现实世界中飞行更为稳妥。对于尚未获得实验机体的用户来说，这也是开始使用PX4飞行的良好方式。
:::

PX4支持两种模拟方式：_软件在环（SITL）_（飞行栈在计算机上运行，该计算机可以是同一台设备或同一网络中的另一台设备），以及使用真实飞行控制器板的_硬件在环（HITL）_模拟。

下一节将提供可用模拟器及其设置方法的信息。其他章节则提供关于模拟器工作原理的通用信息，但这些内容并非使用模拟器的必要条件。

## 支持的模拟器

PX4核心开发团队支持以下模拟器：

| 模拟器 | 描述 |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Gazebo](../sim_gazebo_gz/index.md) | Gazebo取代了[Gazebo Classic](../sim_gazebo_classic/index.md)，具有更先进的渲染、物理和传感器模型。它是Ubuntu Linux 22.04中唯一可用的Gazebo版本<br><br>功能强大的3D模拟环境，特别适合测试避障和计算机视觉。它也可用于[多机体模拟](../simulation/multi-vehicle-simulation.md)，并常与[ROS](../simulation/ros_interface.md)配合使用（ROS是一组用于自动化机体控制的工具集合）<br><br><strong>支持的机体：</strong>四旋翼、VTOL（标准型、尾座型、倾转旋翼）、固定翼、Rovers |
| [Gazebo Classic](../sim_gazebo_classic/index.md) | 功能强大的3D模拟环境，特别适合测试避障和计算机视觉。它也可用于[多机体模拟](../simulation/multi-vehicle-simulation.md)，并常与[ROS](../simulation/ros_interface.md)配合使用（ROS是一组用于自动化机体控制的工具集合）<br><br>**支持的机体：** 四旋翼（[Iris](../airframes/airframe_reference.md#copter_quadrotor_x_generic_quadcopter)）、六旋翼（Typhoon H480）、[通用标准VTOL（QuadPlane）](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)、尾座型、固定翼、Rover、潜水器 |

此外还有一系列[社区支持的模拟器](../simulation/community_supported_simulators.md)。

---

本主题的其余部分是对模拟器基础设施的“相对通用”的描述。  
使用这些模拟器并不是必须的。

## 模拟器 MAVLink API

所有模拟器（除 Gazebo 外）均通过 Simulator MAVLink API 与 PX4 通信。该 API 定义了一组 MAVLink 消息，用于将模拟世界的传感器数据提供给 PX4，并将飞行代码中生成的电机和执行器值返回给模拟的机体。

![Simulator MAVLink API](../../assets/simulation/px4_simulator_messages.svg)

::: info
PX4 的 SITL 构建使用 [SimulatorMavlink.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/simulator_mavlink/SimulatorMavlink.cpp) 处理这些消息，而 HIL 模式下的硬件构建使用 [mavlink_receiver.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mavlink_receiver.cpp)。模拟器的传感器数据写入 PX4 的 uORB 主题。所有电机/执行器被阻断，但内部软件完全运行。
:::

以下列出相关消息（详见链接）。

| 消息                                                         | 方向  | 描述                                                                                                                                                                                                                                   |
| --------------------------------------------------------------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [MAV_MODE:MAV_MODE_FLAG_HIL_ENABLED][mav_mode_flag_hil_enabled] | NA         | 使用模拟器时的模式标志。所有电机/执行器被阻断，但内部软件完全运行。                                                                                                                                |
| [HIL_ACTUATOR_CONTROLS][hil_actuator_controls]                  | PX4 到 Sim | PX4 控制输出（到电机、执行器）。                                                                                                                                                                                                   |
| [HIL_SENSOR][hil_sensor]                                        | Sim 到 PX4 | 模拟 IMU 读数，以 NED 机体坐标系的 SI 单位表示。                                                                                                                                                                                         |
| [HIL_GPS][hil_gps]                                              | Sim 到 PX4 | 模拟 GPS RAW 传感器值。                                                                                                                                                                                                           |
| [HIL_OPTICAL_FLOW][hil_optical_flow]                            | Sim 到 PX4 | 模拟的光流传感器数据（例如 PX4FLOW 或光学鼠标传感器）                                                                                                                                                              |
| [HIL_STATE_QUATERNION][hil_state_quaternion]                    | Sim 到 PX4 | 包含实际"模拟"机体的位置、姿态、速度等信息。可用于记录并对比 PX4 的估计值，进行分析和调试（例如检查估计器在噪声（模拟）传感器输入下的表现）。 |
| [HIL_RC_INPUTS_RAW][hil_rc_inputs_raw]                          | Sim 到 PX4 | 接收到的 RC 通道原始值。                                                                                                                                                                                                   |

<!-- links for table above -->

[mav_mode_flag_hil_enabled]: https://mavlink.io/en/messages/common.html#MAV_MODE_FLAG_HIL_ENABLED
[hil_actuator_controls]: https://mavlink.io/en/messages/common.html#HIL_ACTUATOR_CONTROLS
[hil_sensor]: https://mavlink.io/en/messages/common.html#HIL_SENSOR
[hil_gps]: https://mavlink.io/en/messages/common.html#HIL_GPS
[hil_optical_flow]: https://mavlink.io/en/messages/common.html#HIL_OPTICAL_FLOW
[hil_state_quaternion]: https://mavlink.io/en/messages/common.html#HIL_STATE_QUATERNION
[hil_rc_inputs_raw]: https://mavlink.io/en/messages/common.html#HIL_RC_INPUTS_RAW

<!-- above ^^^ links for table -->

PX4 直接使用 [Gazebo API](https://gazebosim.org/docs) 与 [Gazebo](../sim_gazebo_gz/index.md) 接口，无需使用 MAVLink。

## 默认 PX4 MAVLink UDP 端口

默认情况下，PX4 使用通用的 UDP 端口通过 MAVLink 与地面控制站（如 _QGroundControl_）、外部 API（如 MAVSDK、MAVROS）和模拟器 API（如 Gazebo）进行通信。这些端口包括：

- PX4 的远程 UDP 端口 **14550** 用于与地面控制站通信。地面控制站应监听此端口的连接。_QGroundControl_ 默认监听此端口。
- PX4 的远程 UDP 端口 **14540** 用于与外部 API 通信。外部 API 应监听此端口的连接。
  ::: info
  多机体模拟使用独立的远程端口，按序分配从 `14540` 到 `14549`（额外实例均使用端口 `14549`）。
  :::
- 模拟器的本地 TCP 端口 **4560** 用于与 PX4 通信。模拟器监听此端口，PX4 向其发起 TCP 连接。

::: info
地面控制站、外部 API 和模拟器的端口由启动脚本指定。详见 [系统启动](../concept/system_startup.md) 了解更多信息。
:::

<!-- 关于 UDP 端口的有用讨论在这里：https://github.com/PX4/PX4-user_guide/issues/1035#issuecomment-777243106 -->

## SITL 仿真环境

下图展示了一个典型的 SITL 仿真环境，适用于所有使用 MAVLink 的支持仿真器（即除了 Gazebo 之外的所有仿真器）。

![PX4 SITL overview](../../assets/simulation/px4_sitl_overview.svg)

系统各部分通过 UDP 连接，可在同一台计算机或同一网络中的另一台计算机上运行。

- PX4 使用一个仿真专用模块连接到仿真器的本地 TCP 端口 4560。
  仿真器随后通过上述 [Simulator MAVLink API](#模拟器 MAVLink API) 与 PX4 交换信息。
  SITL 上的 PX4 与仿真器可在同一台计算机或同一网络中的不同计算机上运行。

  ::: info
  仿真器也可以使用 _uxrce-dds bridge_ ([XRCE-DDS](../middleware/uxrce_dds.md)) 直接与 PX4 交互（即通过 [UORB topics](../middleware/uorb.md) 而非 MAVLink）。
  此方法可能被 Gazebo Classic 用于 [多机体仿真](../sim_gazebo_classic/multi_vehicle_simulation.md#build-and-test-xrce-dds)。
  :::

- PX4 使用标准 MAVLink 模块连接地面站和外部开发者 API（如 MAVSDK 或 ROS）
  - 地面站监听 PX4 的远程 UDP 端口：`14550`
  - 外部开发者 API 监听 PX4 的远程 UDP 端口：`14540`。
    对于多机体仿真，PX4 会依次为每个实例分配一个独立的远程端口，从 `14540` 到 `14549`（额外的实例均使用端口 `14549`）。
- PX4 定义了多个 _本地_ UDP 端口（`14580`、`18570`），当 PX4 在容器或虚拟机中运行时有时会用到这些端口。
  这些端口不推荐用于“通用”场景，且未来可能会变更。
- 可通过串口连接 [Joystick/Gamepad](../config/joystick.md) 硬件，使用 _QGroundControl_ 进行连接。

如果使用标准构建系统的 SITL `make` 配置目标（见下一节），则 SITL 与仿真器将在同一台计算机上启动，且上述端口将自动配置。
您可以通过构建配置和初始化文件配置额外的 MAVLink UDP 连接并修改仿真环境。

### 启动/构建 SITL 仿真

构建系统使得在 SITL 上构建并启动 PX4、启动仿真器并连接它们变得非常简单。
语法（简化）如下：

```sh
make px4_sitl simulator[_vehicle-model]
```

其中 `simulator` 可为 `gz`（Gazebo）、`gazebo-classic`、`jmavsim` 或其他仿真器，`vehicle-model` 是该仿真器支持的特定机体类型（截至撰写时，[Gazebo](../sim_gazebo_gz/index.md) 和 [jMAVSim](../sim_jmavsim/index.md) 仅支持多旋翼，而 [Gazebo Classic](../sim_gazebo_classic/index.md) 支持多种类型）。

以下是一些示例，更多内容可参考各仿真器的独立页面：

```sh

# 使用x500多旋翼启动Gazebo
make px4_sitl gz_x500

# 使用plane启动Gazebo Classic
make px4_sitl gazebo-classic_plane

# 使用iris和光流启动Gazebo Classic
make px4_sitl gazebo-classic_iris_opt_flow

# 使用 iris 启动 JMavSim（默认机体模型）
make px4_sitl jmavsim

# 无需模拟器启动 PX4（即使用您自己的“自定义”模拟器）  
make px4_sitl none_iris  
```  

可通过环境变量进一步配置仿真：  

- [PX4 参数](../advanced_config/parameter_reference.md) 中的任何参数均可通过 `export PX4_PARAM_{name}={value}` 覆盖。  
  例如更改估计算法：`export PX4_PARAM_EKF2_EN=0; export PX4_PARAM_ATT_EN=1`。  

此处描述的语法为简化版本，通过 _make_ 可配置更多选项，例如设置连接到 IDE 或调试器。  
更多信息请参见：[构建代码 > PX4 Make 构建目标](../dev_setup/building_px4.md#px4-make-build-targets)。  

### 以实时以上速度运行仿真 {#simulation_speed}  

在使用 Gazebo、Gazebo Classic 或 jMAVSim 时，SITL 可以以实时速度以上或以下运行。  

速度因子通过环境变量 `PX4_SIM_SPEED_FACTOR` 设置。  

::: info  
PX4 SITL 与仿真器以 _lockstep_ 方式运行，这意味着它们锁定在相同速度下运行，因此能够适当响应传感器和执行器消息。  
这使得仿真可以在不同速度下运行，也可以暂停仿真以逐步执行代码。  
:::  

更多信息请参见：  

- Gazebo: [更改仿真速度](../sim_gazebo_gz/index.md#change-simulation-speed)  
- Gazebo Classic: [更改仿真速度](../sim_gazebo_classic/index.md#change-simulation-speed) 和 [Lockstep](../sim_gazebo_classic/index.md#lockstep)  
- jMAVSim: [更改仿真速度](../sim_jmavsim/index.md#change-simulation-speed) 和 [Lockstep](../sim_jmavsim/index.md#lockstep)  

### 启动脚本  

脚本用于控制使用哪些参数设置或启动哪些模块。  
它们位于 [ROMFS/px4fmu_common/init.d-posix](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d-posix) 目录，`rcS` 文件是主要入口点。  
更多信息请参见 [系统启动](../concept/system_startup.md)。  

### 模拟安全机制和传感器/硬件故障  

[模拟安全机制](../simulation/failsafes.md) 解释了如何触发 GPS 故障和电池耗尽等安全机制。

## HITL仿真环境

在硬件在环（HITL）仿真中，正常的PX4固件在真实硬件上运行。
HITL仿真环境的文档请参考：[HITL Simulation](../simulation/hitl.md)。

## 摇杆/游戏手柄集成

_QGroundControl_ 桌面版可通过 USB 连接摇杆/游戏手柄，并通过 MAVLink 将其移动指令和按键操作发送到 PX4。  
该功能在 SITL 和 HITL 模拟中均有效，允许您直接控制模拟机体。  
如果没有物理摇杆，也可以通过 QGroundControl 的屏幕虚拟摇杆控制机体。

有关设置信息，请参阅 _QGroundControl 用户指南_：  

- [摇杆设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/joystick.html)  
- [虚拟摇杆](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/virtual_joystick.html)  

<!-- FYI Airsim info on this setting up remote controls: https://github.com/Microsoft/AirSim/blob/master/docs/remote_controls.md -->

## 相机模拟

PX4支持从[Gazebo Classic](../sim_gazebo_classic/index.md)模拟环境中捕获静态图像和视频。
这可以通过如下方式启用/设置：[Gazebo Glassic > 视频流](../sim_gazebo_classic/index.md#video-streaming)。

模拟相机是Gazebo Classic插件，实现了[MAVLink相机协议](https://mavlink.io/en/protocol/camera.html) <!-- **PX4-Autopilot/Tools/simulation/gazebo-classic/sitl_gazebo-classic/src/gazebo_geotagged_images_plugin.cpp -->。
PX4与此相机的连接/集成方式与任何其他MAVLink相机完全相同：

1. [TRIG_INTERFACE](../advanced_config/parameter_reference.md#TRIG_INTERFACE) 必须设置为 `3` 以配置相机触发驱动以配合MAVLink相机使用
   :::tip
   在此模式下，驱动程序会在请求图像捕获时发送[CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER)消息。
   更多信息请参见[连接到飞控输出的相机](../camera/fc_connected_camera.md)。
   :::
1. PX4必须在GCS和（模拟器）MAVLink相机之间转发所有相机命令。
   可通过如示例所示，使用`-f`标志启动[MAVLink](../modules/modules_communication.md#mavlink)，并指定新连接的UDP端口来实现：

   ```sh
   mavlink start -u 14558 -o 14530 -r 4000 -f -m camera
   ```

   ::: info
   不仅相机MAVLink消息会被转发，但相机将忽略其认为不相关的消息。
   :::

其他模拟器可以采用相同方法实现相机支持。

## 在远程服务器上运行模拟

可以在一台计算机上运行模拟器，并通过网络中的另一台计算机访问它（或通过适当路由的其他网络）。
例如，当你想测试运行在真实伴飞计算机硬件上的无人机应用程序与模拟机体的交互时，这可能会很有用。

这并不能"开箱即用"，因为PX4默认情况下不会将数据包路由到外部接口（为了避免网络垃圾信息和不同模拟之间的相互干扰）。
相反，它默认将流量路由到本地主机（localhost）。

有多种方法可以将UDP数据包发布到外部接口，如下所述。

### 使用MAVLink Router

[mavlink-router](https://github.com/mavlink-router/mavlink-router) 可以用于将数据包从本地主机路由到外部接口。

要在运行SITL的计算机（将MAVLink流量发送到本地主机的UDP端口14550）和运行在另一台计算机（例如地址`10.73.41.30`）的QGC之间路由数据包，可以：

- 使用以下命令启动_mavlink-router_：

  ```sh
  mavlink-routerd -e 10.73.41.30:14550 127.0.0.1:14550
  ```

- 使用_mavlink-router_配置文件。

  ```ini
  [UdpEndpoint QGC]
  Mode = Normal
  Address = 10.73.41.30
  Port = 14550

  [UdpEndpoint SIM]
  Mode = Eavesdropping
  Address = 127.0.0.1
  Port = 14550
  ```

::: info
有关_mavlink-router_配置的更多信息请见[此处](https://github.com/mavlink-router/mavlink-router#running)。
:::

### 启用UDP广播

[MAVLink模块](../modules/modules_communication.md#mavlink_usage) 默认路由到_localhost_，但可以使用其`-p`选项启用心跳的UDP广播。
网络中的任何远程计算机都可以通过监听相应端口（即_14550_用于_QGroundControl_）连接到模拟器。

::: info
当网络中只有一个模拟器运行时，UDP广播提供了一种简单的连接设置方式。
如果有多个模拟器在运行，请不要使用此方法（你可能需要[发布到特定地址](#启用到特定地址的流传输)）。
:::

这应该在适当的配置文件中完成，其中调用了`mavlink start`。
例如：[/ROMFS/px4fmu_common/init.d-posix/px4-rc.mavlink](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/px4-rc.mavlink)。

### 启用到特定地址的流传输

[MAVLink模块](../modules/modules_communication.md#mavlink_usage) 默认路由到_localhost_，但可以使用其`-t`选项指定外部IP地址进行流传输。
指定的远程计算机可以通过监听相应端口（即_14550_用于_QGroundControl_）连接到模拟器。

这应该在各种配置文件中完成，其中调用了`mavlink start`。
例如：[/ROMFS/px4fmu_common/init.d-posix/px4-rc.mavlink](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/px4-rc.mavlink)。

### SSH隧道

SSH隧道提供了一个灵活的选项，因为模拟计算机和使用它的系统不需要处于同一网络。

::: info
你也可以使用VPN来提供到外部接口的隧道（在相同网络或另一个网络中）。
:::

创建隧道的一种方法是使用SSH隧道选项。
通过在_localhost_上运行以下命令可以创建隧道，其中`remote.local`是远程计算机的名称：

```sh
ssh -C -fR 14551:localhost:14551 remote.local
```

UDP数据包需要转换为TCP数据包以便通过SSH路由。
可以在隧道的两端使用[netcat](https://en.wikipedia.org/wiki/Netcat)工具 - 首先将数据包从UDP转换为TCP，然后在另一端再转换回UDP。

:::tip
执行_netcat_之前必须运行QGC。
:::

在_QGroundControl_计算机上，可以通过运行以下命令实现UDP数据包转换：

```sh
mkfifo /tmp/tcp2udp
netcat -lvp 14551 < /tmp/tcp2udp | netcat -u localhost 14550 > /tmp/tcp2udp
```

在SSH隧道的模拟器端，命令是：

```sh
mkfifo /tmp/udp2tcp
netcat -lvup 14550 < /tmp/udp2tcp | netcat localhost 14551 > /tmp/udp2tcp
```

端口号`14550`适用于连接到QGroundControl或其他GCS，但应根据其他端点（如开发者API等）进行调整。

理论上隧道可以无限运行，但遇到问题时可能需要重新启动_netcat_连接。

可以在QGC计算机上运行[QGC_remote_connect.bash](https://raw.githubusercontent.com/ThunderFly-aerospace/sitl_gazebo/autogyro-sitl/scripts/QGC_remote_connect.bash)脚本来自动设置/运行上述指令。
模拟必须已经在远程服务器上运行，且你必须能够SSH登录到该服务器。