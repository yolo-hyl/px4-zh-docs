# MAVLink相机（相机协议v2）

本节内容将说明如何使用PX4与实现[MAVLink相机协议v2](https://mavlink.io/en/services/camera.html)的MAVLink[相机](../camera/index.md)，以及它们在PX4和地面站中的集成方式。

::: tip
这是将相机与PX4集成的推荐方法！
:::

## 概述

[MAVLink相机协议v2](https://mavlink.io/en/services/camera.html)允许查询相机支持的功能，并提供控制图像和视频采集、视频流传输、设置变焦和对焦、在红外和可见光图像流之间切换、设置采集数据存储位置等命令。

相机可以原生实现该协议，但大多数MAVLink相机配置涉及PX4与运行在伴随计算机上的[camera manager](#相机管理器)进行通信，该管理器在MAVLink和相机原生协议之间进行接口转换。

通常来说，PX4对相机的"集成"方式是通过命令协议重新发送任务中发现的相机命令。
否则它可能充当桥梁，在地面站和相机之间转发命令（当没有直接MAVLink通道时）。

::: info
PX4不支持使用[MAVLink相机协议v2](https://mavlink.io/en/services/camera.html)命令控制连接到飞控输出的相机。
虽然这在技术上是可能的，但需要PX4实现相机管理器接口。
:::

## 控制相机

### MAVLink 命令与消息

相机通过MAVLink [连接协议](https://mavlink.io/en/services/heartbeat.html)进行发现，其[HEARTBEAT.type](https://mavlink.io/en/messages/common.html#HEARTBEAT)字段设置为[MAV_TYPE_CAMERA](https://mavlink.io/en/messages/common.html#MAV_TYPE_CAMERA)。

::: tip
相机应使用推荐范围内的组件ID，例如[MAV_COMP_ID_CAMERA](https://mavlink.io/en/messages/common.html#MAV_COMP_ID_CAMERA)，但通常无法依赖此来验证MAVLink组件是否为相机。
:::

一旦发现相机，可以通过[MAV_CMD_REQUEST_MESSAGE](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_MESSAGE)请求[CAMERA_INFORMATION](https://mavlink.io/en/messages/common.html#CAMERA_INFORMATION)消息，并检查`flags`字段来确定相机支持的[CAMERA_CAP_FLAGS](https://mavlink.io/en/messages/common.html#CAMERA_CAP_FLAGS)标准功能。

根据这些标志，可以确定相机支持的其他命令和消息。
完整的消息、命令和枚举集[在此汇总](https://mavlink.io/en/services/camera.html#messagecommandenum-summary)。

相机的附加参数可能通过[相机定义文件](https://mavlink.io/en/services/camera_def.html)暴露，该文件链接自`CAMERA_INFORMATION.cam_definition_uri`。
地面站或SDK可以通过通用UI暴露这些设置，而无需理解任何上下文。
这些参数不能直接在任务中设置，也没有特定的设置命令。

[MAVLink相机协议v2](https://mavlink.io/en/services/camera.html)详细描述了所有交互。

### 地面站 & MAVLink SDK

地面站和MAVLink SDK按照上一节所述发现相机及其功能。

地面站可以使用相机暴露的任何功能。
PX4在此交互中除了在需要时在相机和地面站或SDK之间转发MAVLink流量外，不承担其他角色。

### 任务中的相机命令

PX4允许在任务中使用以下[相机协议v2](https://mavlink.io/en/services/camera.html)命令的子集：

- [MAV_CMD_IMAGE_START_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_IMAGE_START_CAPTURE)
- [MAV_CMD_IMAGE_STOP_CAPTURE](https://mavlink.io/en/messages/common.html#MMAV_CMD_IMAGE_STOP_CAPTURE)
- [MAV_CMD_VIDEO_START_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_VIDEO_START_CAPTURE)
- [MAV_CMD_VIDEO_STOP_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_VIDEO_STOP_CAPTURE)
- [MAV_CMD_SET_CAMERA_MODE](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_MODE)
- [MAV_CMD_SET_CAMERA_ZOOM](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_ZOOM)
- [MAV_CMD_SET_CAMERA_FOCUS](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_FOCUS)

PX4将任务中的相机命令重新发送为MAVLink命令。
发出的命令的系统ID与自动驾驶仪的ID相同。
命令的组件ID可能不同。
前四个命令发送给[MAV_COMP_ID_CAMERA (100)](https://mavlink.io/en/messages/common.html#MAV_COMP_ID_CAMERA)（如果相机具有此组件ID，它将执行指示的命令）。
相机模式、变焦和对焦命令发送给组件ID为[MAV_COMP_ID_ALL](https://mavlink.io/en/messages/common.html#MAV_COMP_ID_ALL)的组件。

:::info
PX4目前忽略[MAV_CMD_IMAGE_START_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_IMAGE_START_CAPTURE)和其他相机消息中的目标相机`id`。
参见[PX4-Autopilot#23083](https://github.com/PX4/PX4-Autopilot/issues/23083)。
:::

<!--
任务中支持的命令列表在：
format_mavlink_mission_item() => https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mavlink_mission.cpp#L1672-L1693

当设置为活动状态时执行任务项。
void Mission::setActiveMissionItems() => https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/mission.cpp#L187-L281
  最后会发出当前的非航点命令：
  note at end => issue_command(_mission_item);

发出命令：
MissionBlock::issue_command(const mission_item_s &item) =>  https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/mission_block.cpp#L543-L562
  最后会发布当前机体命令
  _navigator.publish_vehicle_command(vehicle_command);

发布命令：
void Navigator::publish_vehicle_command(vehicle_command_s &vehicle_command)  => https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/navigator_main.cpp#L1395
  对于相机命令，设置vehicle_command.target_component = 100; // MAV_COMP_ID_CAMERA
  其他命令直接发布
-->

### 手动控制器

操纵杆按钮可以配置为触发图像捕获或切换视频捕获。

当关联的操纵杆按钮被按下时，PX4会发出[MAVLink相机协议v2](https://mavlink.io/en/services/camera.html)命令，如`MAV_CMD_IMAGE_START_CAPTURE`。
此功能仅适用于此类相机和操纵杆，不支持RC控制器。

## PX4 配置

### MAVLink端口与转发

需要为连接的相机提供一个MAVLink通道，以便PX4可以发送任务中的相机指令。
如果MAVLink网络结构中PX4位于相机与地面站之间，还需要转发通信以实现它们之间的交互。

首先将相机连接到飞控上未使用的串口（例如 `TELEM2`，如果飞控和相机都支持以太网接口，也可以使用以太网口）。
然后将选定端口配置为 [MAVLink Peripheral](../peripherals/mavlink_peripherals.md)。

关联文档解释了具体操作，简要步骤如下：

1. 修改一个未使用的 `MAV_n_CONFIG` 参数（例如 [MAV_2_CONFIG](../advanced_config/parameter_reference.md#MAV_2_CONFIG)），将其分配到连接相机/计算模块的端口。
1. 将对应的 [MAV_2_MODE](../advanced_config/parameter_reference.md#MAV_2_MODE) 设为 `2`（Onboard）。
   这将确保发送正确的MAVLink消息给计算模块（或相机）。
1. 启用 [MAV_2_FORWARD](../advanced_config/parameter_reference.md#MAV_2_FORWARD) 参数，以实现该端口与连接地面站的其他端口之间的通信转发。
1. 根据连接类型和相机的特殊需求（如期望的波特率等），可能需要设置其他参数。

### 手动控制

摇杆按键可以映射为拍照功能，以及切换视频录制的开/关状态。

- [摇杆](../config/joystick.md#enabling-px4-joystick-support) 解释了如何在PX4上启用摇杆功能。
- [QGroundControl > 摇杆设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/joystick.html) 解释了如何将按键映射到飞行栈功能

<!-- 相机似乎无法通过遥控器进行控制 -->

## 相机管理器

如果要使用不原生支持MAVLink相机协议的相机，你需要一个MAVLink相机管理器。  
相机管理器在伴随计算机上运行，作为MAVLink相机协议接口和相机原生接口之间的桥梁。

存在可用于多种相机的"可扩展"相机管理器、专为特定相机设计的管理器，你也可以自己编写（例如，使用MAVSDK服务器插件）。

通用/可扩展相机管理器：

- [MAVLink相机管理器](https://github.com/mavlink/mavlink-camera-manager) - 基于GStreamer和Rust-MAVLink构建的可扩展跨平台MAVLink相机服务器。  
- [Dronecode相机管理器](https://camera-manager.dronecode.org/en/) - 为连接到Linux计算机的相机添加相机协议接口。

特定相机管理器：

- [SIYI A8 mini相机管理器](https://github.com/julianoes/siyi-a8-mini-camera-manager) - 基于MAVSDK插件的相机管理器，用于[SIYI A8 mini](https://shop.siyi.biz/products/siyi-a8-mini)（包含教程）。

  ::: tip
  这是展示如何使用MAVSDK为特定相机创建MAVLink相机协议接口的很好示例。
  :::

使用相机管理器时，需要将伴随计算机连接到飞控器（而非直接连接相机），并且需要在计算机上安装额外软件将MAVLink流量路由到伴随计算机上的相机管理器，例如[mavlink-router](https://github.com/mavlink-router/mavlink-router)。

关于相机管理器和伴随计算机配置的更多信息请参考：

- [SIYI A8 mini相机管理器](https://github.com/julieanoes/siyi-a8-mini-camera-manager) - 使用运行在Raspberry Pi伴随计算机上的MAVSDK相机管理器集成[SIYI A8 mini](https://shop.siyi.biz/products/siyi-a8-mini)的教程。  
- [使用伴随计算机与Pixhawk控制器](../companion_computer/pixhawk_companion.md)  
- [伴随计算机 > 伴随计算机软件](../companion_computer/index.md#companion-computer-software)：特别注意[MAVLink-Router](https://github.com/mavlink-router/mavlink-router)，可以配置其在串口和IP链路（或其他相机管理器接口）之间路由MAVLink流量。