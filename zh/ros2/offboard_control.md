# ROS 2 飞行器外部控制示例

以下 C++ 示例展示了如何通过 ROS 2 节点在 [外部模式](../flight_modes/offboard.md) 下进行位置控制。

该示例开始发送设定点，进入外部模式，解锁，上升到 5 米高度并等待。
虽然示例较为简单，但展示了使用外部控制的主要原则以及如何发送机体控制指令。

该示例已在 Ubuntu 20.04、ROS 2 Foxy 和 PX4 v1.14 上测试通过。

::: warning
外部控制是危险的。
如果在真实机体上运行，请确保在出现问题时能够恢复手动控制。
:::

::: info
ROS 和 PX4 对多个方面有不同假设，特别是关于 [坐标系约定](../ros/external_position_estimation.md#reference-frames-and-ros)。
在主题发布或订阅时不会自动转换坐标系类型！

本示例以 PX4 期望的 NED 坐标系发布位置数据。
若需订阅来自使用不同坐标系（例如 ENU，ROS/ROS 2 的标准参考系）的节点的数据，请使用 [frame_transforms](https://github.com/PX4/px4_ros_com/blob/main/src/lib/frame_transforms.cpp) 库中的辅助函数。
:::

## 尝试使用

按照[ROS 2 User Guide](../ros2/user_guide.md)中的说明安装PX4并运行模拟器，安装ROS 2并启动XRCE-DDS Agent。

之后我们可以遵循与[ROS 2 User Guide > Build ROS 2 Workspace](../ros2/user_guide.md#build-ros-2-workspace)中类似的步骤来运行示例。

::: tip
确保在运行ROS 2节点之前QGC已连接到PX4。  
这是必需的，因为默认情况下，没有地面站（QGC）连接或建立的遥控器（RC）连接时，无法解锁机体（这确保了始终可以恢复手动控制）。
:::

要构建并运行示例：

1. 打开一个新的终端。
1. 使用以下命令创建并进入新的colcon工作区目录：

   ```sh
   mkdir -p ~/ws_offboard_control/src/
   cd ~/ws_offboard_control/src/
   ```

1. 将[px4_msgs](https://github.com/PX4/px4_msgs)仓库克隆到`/src`目录（此仓库在每个ROS 2 PX4工作区中都是必需的！）：

   ```sh
   git clone https://github.com/PX4/px4_msgs.git
   # 如果不使用PX4 main分支，请切换到对应的发布分支。
   ```

1. 将示例仓库[px4_ros_com](https://github.com/PX4/px4_ros_com)克隆到`/src`目录：

   ```sh
   git clone https://github.com/PX4/px4_ros_com.git
   ```

1. 将ROS 2开发环境引入当前终端，并使用`colcon`编译工作区：

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

1. 引入`local_setup.bash`：

   ```sh
   source install/local_setup.bash
   ```

1. 启动示例程序。

   ```
   ros2 run px4_ros_com offboard_control
   ```

机体应解锁、上升5米，然后进入等待状态（永久等待）。

## 实现

外部控制示例的源代码可以在 [PX4/px4_ros_com](https://github.com/PX4/px4_ros_com) 的目录 [/src/examples/offboard/offboard_control.cpp](https://github.com/PX4/px4_ros_com/blob/main/src/examples/offboard/offboard_control.cpp) 中找到。

::: info
PX4 默认会将示例中使用的所有消息以 ROS 主题发布（详见 [dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml)）。
:::

PX4 要求在机体以外部模式解锁或切换至外部模式飞行前，已接收到 `OffboardControlMode` 消息。此外，若 `OffboardControlMode` 消息的流速低于约 2Hz，PX4 将自动退出外部模式。该行为由 ROS 2 节点的主循环实现，如下所示：

```cpp
auto timer_callback = [this]() -> void {

	if (offboard_setpoint_counter_ == 10) {
		// 在发送 10 个目标点后切换至外部模式
		this->publish_vehicle_command(VehicleCommand::VEHICLE_CMD_DO_SET_MODE, 1, 6);

		// 解锁机体
		this->arm();
	}

	// OffboardControlMode 需与 TrajectorySetpoint 配合使用
	publish_offboard_control_mode();
	publish_trajectory_setpoint();

	// 在达到 11 次后停止计数器
	if (offboard_setpoint_counter_ < 11) {
		offboard_setpoint_counter_++;
	}
};
timer_ = this->create_wall_timer(100ms, timer_callback);
```

循环以 100ms 定时器运行。前 10 次循环会调用 `publish_offboard_control_mode()` 和 `publish_trajectory_setpoint()`，向 PX4 发送 [OffboardControlMode](../msg_docs/OffboardControlMode.md) 和 [TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md) 消息。`OffboardControlMode` 消息的流速确保 PX4 在切换至外部模式后允许解锁，而 `TrajectorySetpoint` 消息在此阶段被忽略（直到机体进入外部模式）。

10 次循环后，`publish_vehicle_command()` 被调用以切换至外部模式，`arm()` 被调用以解锁机体。机体解锁并切换模式后开始跟踪位置目标点。目标点仍在每次循环中发送，以防止机体退出外部模式。

`publish_offboard_control_mode()` 和 `publish_trajectory_setpoint()` 方法的实现如下。它们分别向 PX4 发布 [OffboardControlMode](../msg_docs/OffboardControlMode.md) 和 [TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md) 消息。

`OffboardControlMode` 用于告知 PX4 使用的外部控制类型。此处仅使用 _位置控制_，因此 `position` 字段设为 `true`，其他字段设为 `false`。

```cpp
/**
 * @brief 发布外部控制模式。
 *        此示例中仅激活位置和高度控制。
 */
void OffboardControl::publish_offboard_control_mode()
{
	OffboardControlMode msg{};
	msg.position = true;
	msg.velocity = false;
	msg.acceleration = false;
	msg.attitude = false;
	msg.body_rate = false;
	msg.thrust_and_torque = false;
	msg.direct_actuator = false;
	msg.timestamp = this->get_clock()->now().nanoseconds() / 1000;
	offboard_control_mode_publisher_->publish(msg);
}
```

`TrajectorySetpoint` 提供位置目标点。此处 `x`、`y`、`z` 和 `yaw` 字段被硬编码为特定值，但它们可动态更新，根据算法或通过订阅来自其他节点的消息的回调函数进行调整。

```cpp
/**
 * @brief 发布轨迹目标点
 *        此示例中，它发送一个轨迹目标点，使机体在 5 米高度悬停，偏航角为 180 度。
 */
void OffboardControl::publish_trajectory_setpoint()
{
	TrajectorySetpoint msg{};
	msg.position = {0.0, 0.0, -5.0};
	msg.yaw = -3.14; // [-PI:PI]
	msg.timestamp = this->get_clock()->now().nanoseconds() / 1000;
	trajectory_setpoint_publisher_->publish(msg);
}
```

`publish_vehicle_command()` 向飞控发送 [VehicleCommand](../msg_docs/VehicleCommand.md) 消息。我们通过它切换至外部模式，并在 `arm()` 中用于解锁机体。虽然本示例中未调用 `disarm()`，但它也用于该函数的实现中。

```cpp
/**
 * @brief 发布机体指令
 * @param command   指令代码（对应 VehicleCommand 和 MAVLink MAV_CMD 代码）
 * @param param1    指令参数 1
 * @param param2    指令参数 2
 */
void OffboardControl::publish_vehicle_command(uint16_t command, float param1, float param2)
{
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
	vehicle_command_publisher_->publish(msg);
}
```

::: info
[VehicleCommand](../msg_docs/VehicleCommand.md) 是命令 PX4 的最简单且最强大的方式之一，通过订阅 [VehicleCommandAck](../msg_docs/VehicleCommandAck.md) 还可以确认特定指令的设置是否成功。
`param` 和 `command` 字段映射到 [MAVLink 命令](https://mavlink.io/en/messages/common.html#mav_commands) 及其参数值。
:::

## 另请参阅

- [Python ROS2 offboard examples with PX4](https://github.com/Jaeyoung-Lim/px4-offboard) (Jaeyoung-Lim/px4-offboard).