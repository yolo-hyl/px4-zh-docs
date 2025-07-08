# 任务中的包裹投递

<Badge type="tip" text="PX4 v1.14" />

包裹投递任务是航点任务的扩展功能，用户可将投递包裹作为航点进行规划。

本主题解释包裹投递功能的架构，主要面向需要扩展架构的开发者，例如支持新的载荷投递机制。

::: info
目前仅支持通过[Grippers（夹爪）](../peripherals/gripper.md)进行包裹投递。
绞车（Winch）功能尚未支持。
:::

::: info
关于如何设置包裹投递任务计划的详细文档请参见[此处](../flying/package_delivery_mission.md)。
`payload_deliverer`模块的设置请参考对应投递机制的文档，例如[Gripper](../peripherals/gripper.md#px4-configuration)。
:::

## 包裹投递架构图

![包裹投递架构概览](../../assets/advanced_config/payload_delivery_mission_architecture.png)

包裹投递功能围绕[VehicleCommand](../msg_docs/VehicleCommand.md) & [VehicleCommandAck](../msg_docs/VehicleCommandAck.md)消息构建。

核心理念是通过实体处理`DO_GRIPPER`或`DO_WINCH`机体命令，执行操作并在确认成功投递后发送确认信息。

由于PX4会自动将`VehicleCommand` uORB消息通过配置为MAVLink通信的UART端口广播为[`COMMAND_LONG`](https://mavlink.io/en/messages/common.html#COMMAND_LONG)消息，外部载荷可接收并执行该命令。

同样，PX4会自动将通过MAVLink配置的UART端口接收到的外部源发送的[`COMMAND_ACK`](https://mavlink.io/en/messages/common.html#COMMAND_ACK)消息转换为`vehicle_command_ack` uORB消息，因此外部载荷的成功投递确认信息可被PX4的`navigator`模块接收。

下文将解释包裹投递架构中涉及的各个实体。

## 导航器（Navigator）

导航器负责接收机体命令确认（ACK）（见下文描述）。
接收到成功投递的ACK消息后，它会在任务模块层面设置标志位，表明载荷投递已成功。

这使得任务可以安全地进入下一个项目（例如航点），因为通过确认信息我们确定投递已成功完成。

## 机体命令确认（Vehicle Command ACK）

我们等待来自内部（通过`payload_deliverer`模块）或外部（外部实体发送的MAVLink `COMMAND_ACK`消息）的ACK，以确认包裹投递动作是否成功（`DO_GRIPPER`或`DO_WINCH`）。

## 任务（Mission）

夹爪/绞车命令作为`任务项目`存在。
由于所有任务项目都包含可执行的`MAV_CMD`（例如降落、起飞、航点等），因此可以将其设置为`DO_GRIPPER`或`DO_WINCH`。

在任务逻辑（上图绿色框）中，当遇到夹爪/绞车任务项目时，会为旋翼机（如多旋翼）实现`brake_for_hold`功能（将下一个任务航点的`valid`标志设为`false`），使机体在执行投递时保持位置。

对于固定翼和其他机型，不考虑特殊制动条件。
因此如果固定翼有盘旋任务项目，它会在盘旋过程中执行投递，而不会停止（因其无法停止）。

## 任务模块（Mission Block）

`MissionBlock`是`Mission`的父类，负责处理"任务是否完成？"的部分。

所有操作在`is_mission_item_reached_or_completed`函数中进行，用于处理时间延迟/任务项目推进。

它还实现了实际的`issue_command`函数，该函数将根据任务项目的`MAV_CMD`发出对应的机体命令，随后将被外部载荷或内部的`payload_deliverer`模块接收。

## 载荷投递模块（Payload Deliverer）

这是专门处理夹爪/绞车支持的模块，用于标准[包裹投递任务计划](../flying/package_delivery_mission.md)。

`payload_deliverer`模块的设置请参见实际载荷释放机制的设置文档，例如[Gripper](../peripherals/gripper.md#px4-configuration)。