# PX4 相机架构/集成

本主题简要概述了 PX4 相机支持的实现方式。

::: info
有关_使用_相机的信息，请参见 [Camera](../camera/index.md)。
:::

## 概述

PX4 与三种类型的相机集成：

- [MAVLink相机](../camera/mavlink_v2_camera.md)，支持[相机协议v2](https://mavlink.io/en/services/camera.html)（**推荐**）。
- [简易MAVLink相机](../camera/mavlink_v1_camera.md)，支持旧版[相机协议v1](https://mavlink.io/en/services/camera.html)。
- [连接至飞控输出的相机](../camera/fc_connected_camera.md)，通过[相机协议v1](https://mavlink.io/en/services/camera.html)进行控制。

所有这些相机都需要响应通过MAVLink接收到的MAVLink命令或任务中的命令（具体协议取决于相机）。

所使用的总体架构如下所述。

## MAVLink 相机（Camera Protocol v2）

PX4 对支持 [Camera Protocol v2](https://mavlink.io/en/services/camera.html) 的 [MAVLink 相机](../camera/mavlink_v2_camera.md) 没有特殊处理，仅在任务中重新发射相机项目作为命令（见[camera-commands-in-missions]部分）。

地面站应直接与这些相机通信以发送命令。  
如需通信，PX4 必须配置为在相机和地面站之间路由 MAVLink 流量。

::: info
该相机不使用 `camera_trigger`、`camera_capture` 和 `camera_feedback` 模块。
:::

## 连接到飞控的相机

[连接到飞控输出的相机](../camera/fc_connected_camera.md)需要PX4激活输出以触发相机，且可能需要PX4检测[相机捕获引脚](../camera/fc_connected_camera.md#camera-capture-configuration)是否被相机热靴触发(用于改善记录的相机捕获时间)。

该工作由三个PX4组件完成：[`camera_trigger`驱动](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_trigger)，[`camera_capture`驱动](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_capture)，[`camera-feedback`模块](../modules/modules_system.md#camera-feedback)。

`camera_trigger`订阅[VehicleCommand](../msg_docs/VehicleCommand.md)主题并监控其[支持的命令](../camera/fc_connected_camera.md#mavlink-command-interface)的更新。这些更新发生在通过MAVLink接收到命令时，或当[任务中达到相机项](#camera-commands-in-missions)时。

这些命令用于启用/禁用触发，并配置按时间/距离间隔的触发。驱动器跟踪这些间隔，并在需要时触发输出。驱动器发布一个[CameraTrigger](../msg_docs/CameraTrigger.md)主题（`feedback`字段设为`false`），这会触发一个[CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER) MAVLink消息的发送。

如果启用，`camera_capture`驱动会监控相机捕获引脚，并在触发时发布一个[CameraTrigger](../msg_docs/CameraTrigger.md)主题（`feedback`字段设为`true`），这同样会触发[CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER) MAVLink消息的发送。

`camera_feedback`模块监控[CameraTrigger](../msg_docs/CameraTrigger.md)主题的更新，并为来自`camera_trigger`或`camera_capture`的CameraTrigger更新发布[CameraCapture](../msg_docs/CameraCapture.md)主题。使用的信息取决于相机捕获引脚是否启用以及`CameraTrigger.feedback`字段的值。此`CameraCapture`主题会被记录，可用于获取捕获时间。

## MAVLink 相机（Camera Protocol v1）

[支持旧版 Camera Protocol v1 协议的 MAVLink 相机](../camera/mavlink_v1_camera.md)的集成方式与[FC连接相机](#fc-connected-cameras)类似。

`camera_trigger` 订阅 [VehicleCommand](../msg_docs/VehicleCommand.md) 主题并监控[其支持的命令](../camera/fc_connected_camera.md#mavlink-command-interface)的更新。
当通过 MAVLink 接收到命令，或[在任务中找到相机指令](#camera-commands-in-missions)时，这一过程就会触发。

这些命令用于启用/禁用触发，并配置在时间与距离间隔内的触发。
驱动程序会跟踪这些间隔，但使用 "MAVLink backend" 时不需要实际触发任何输出（因为命令会转发给相机）。
当相机需要触发时，驱动程序会发布一个 [CameraTrigger](../msg_docs/CameraTrigger.md) 主题（`feedback` 字段设为 `false`），从而生成 [CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER) MAVLink 消息。
`camera_feedback` 模块随后应记录一个对应的 `CameraCapture` 主题。

## 任务中的相机命令

PX4会将任务中包含的相机项目重新发送为MAVLink命令，支持所有[Camera Protocol v2](https://mavlink.io/en/services/camera.html)和[Camera Protocol v1](https://mavlink.io/en/services/camera.html)命令。
发出的命令的系统ID与自动驾驶器的ID相同。
命令的组件ID可能不同，但通常发送到[MAV_COMP_ID_CAMERA (100)](https://mavlink.io/en/messages/common.html#MAV_COMP_ID_CAMERA)或[MAV_COMP_ID_ALL](https://mavlink.io/en/messages/common.html#MAV_COMP_ID_ALL)（具体使用哪个ID请参考各相机文档）。

只要存在MAVLink通道，系统就会发出这些命令，无论是否连接了任何类型的相机。

::: info
更一般地，PX4会重新发送所有可能被外部MAVLink组件（如云台）使用的任务命令。
航点和条件行为的命令不会被发出。
:::

以下部分展示了代码库中值得关注的部分

### 任务中支持的命令

任务中支持的命令（包括相机命令）在以下方法中体现：

- [`bool FeasibilityChecker::checkMissionItemValidity(mission_item_s &mission_item, const int current_index)`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/MissionFeasibility/FeasibilityChecker.cpp#L257-L306)
- [`format_mavlink_mission_item()`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mavlink_mission.cpp#L1672-L1693)

### 任务中相机命令的重新发送流程

- [`void Mission::setActiveMissionItems()`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/mission.cpp#L187-L281)
  - 当设置为活动状态时执行任务项目。
  - 最后调用`issue_command(_mission_item)`发送当前非航点命令
    - [`MissionBlock::issue_command(const mission_item_s &item)`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/mission_block.cpp#L543-L562)
      - 为任务项目创建一个机体命令，然后调用`publish_vehicle_command`发布它(`_navigator->publish_vehicle_command(vehicle_command);`)
        - [`void Navigator::publish_vehicle_command(vehicle_command_s &vehicle_command)`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/navigator_main.cpp#L1395)
          - 对于部分相机命令会将组件ID设置为相机组件ID(`vehicle_command.target_component = 100; // MAV_COMP_ID_CAMERA`)
          - 其他命令则使用默认组件ID发布
          - 发布`VehicleCommand` UORB主题

MAVLink流传输代码会监控`VehicleCommand`主题的变更并通过MAVLink发布。
无论相机是否为MAVLink相机或连接到飞控，MAVLink命令都会被发送。

如果启用了`camera_trigger`驱动，它也会监控`VehicleCommand`的变更。
如果配置了连接到飞控输出的相机后端，它会适当触发这些输出。

## 日志记录

当出现 `CameraTrigger` 更新时，会记录 `CameraCapture` 主题。  
记录的主题取决于相机捕捉引脚是否启用。

注意：使用支持 [MAVLink 相机协议 v2](../camera/mavlink_v2_camera.md) 的相机时，不会记录相机捕捉事件，因为对应的触发事件未在 PX4 内生成。

## 另请参阅

- 相机触发驱动: [源代码](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_trigger) <!-- no module doc -->
- 相机捕捉驱动: [源代码](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_capture) <!-- no module doc -->