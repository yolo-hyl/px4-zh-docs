# 机械手

机械手是可集成到无人机体上的机械装置，用于抓取（固定）和释放载荷。

PX4允许在[载荷投递任务](../flying/package_delivery_mission.md)中自动触发机械手，或通过[遥控器](#QGC遥控器配置)手动触发。

![高负载机械手示例](../../assets/hardware/grippers/highload_gripper_example.jpg)

::: info
机械手也可配置为[通用RC或MAVLink执行器](../payloads/generic_actuator_control.md#generic-actuator-control-with-rc)。
通用执行器不能与遥控器或投递任务配合使用，但可与RC控制器配合使用。
:::

## 支持的机械手

机械手机制有多种类型（"颚""手指""电磁铁"），接口包括PWM、CAN、MAVLink等。

PX4支持具有简单抓取/释放功能的机械手，且需使用以下接口（详见链接文档）：

- [PWM舵机机械手](gripper_servo.md) - 连接到飞控PWM输出的机械手
- **MAVLink机械手**（未测试）- 支持[MAV_CMD_DO_GRIPPER](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_GRIPPER) MAVLink命令的机械手。

## 使用机械手

关于任务中使用机械手的说明，请参见[载荷投递任务](../flying/package_delivery_mission.md)。

如果已在[QGC遥控器配置](#QGC遥控器配置)中映射了`gripper open`和`gripper close`按钮，可通过遥控器按钮手动触发机械手。注意当机械手正在打开时按下**Grab**按钮，会中止释放行为并切换到关闭位置，从而取消释放命令。如果在任务执行释放时这么做，[投递将被取消](../flying/package_delivery_mission.md#manual-control-of-gripper-in-missions)。

不支持通过[遥控器控制](../getting_started/rc_transmitter_receiver.md)开关手动触发机械手。

MAVLink应用程序（如地面站）也可通过[MAV_CMD_DO_GRIPPER](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_GRIPPER) MAVLink命令控制机械手。

## PX4配置

### 载荷投递配置

PX4的机械手支持与载荷投递功能绑定，需启用并配置才能使用机械手。

1. 将[PD_GRIPPER_EN](../advanced_config/parameter_reference.md#PD_GRIPPER_EN)参数设置为1（修改后需重启）。
1. 将[PD_GRIPPER_TYPE](../advanced_config/parameter_reference.md#PD_GRIPPER_TYPE)设置为您的机械手类型。
   例如，[Servo机械手](gripper_servo.md)需设置为`Servo`。

### 机械手执行器映射

直接连接到飞控的机械手（如PWM舵机机械手）必须在[执行器配置](../config/actuators.md#actuator-outputs)中映射到特定输出。

通过将`Gripper`功能分配到机械手连接的输出端口来实现。例如，下图将`Gripper`分配到PWM AUX5输出。

![机械手输出映射](../../assets/config/gripper/qgc_gripper_actuator_output_small.png)

具体执行器映射信息请参见机械手专用文档。例如，参见[舵机机械手 > 执行器映射](../peripherals/gripper_servo.md#actuator-mapping)。

### 启用预上锁模式

通常需要启用[预上锁模式](../advanced_config/prearm_arm_disarm.md)。此模式在禁用电机的同时允许机械手开合以安装载荷（避免螺旋桨旋转带来的潜在危险）。

1. 将[COM_PREARM_MODE](../advanced_config/parameter_reference.md#COM_PREARM_MODE)设置为`Always`。

### 机械手动作超时

在载荷投递时，机械手需在执行后续航点前有足够时间释放。对于大多数不提供状态反馈的机械手，需配置超时时间以确定机械手应完成开合。

设置动作超时：

1. 测量机械手打开和关闭所需时间，记录较长的那个时间。

   有两种简单方式测试机械手开合：
   当无人机放置在工作台且螺旋桨已拆除时：

   - 在QGC的[MAVLink Shell](../debug/mavlink_shell.md)中运行`payload_deliverer`测试：

     ```sh
     > payload_deliverer gripper_test
     ```

     ::: info
     如果收到类似"[payload_deliverer] not running"的错误信息，请重新执行上述设置步骤。
     可能还需要在Nuttx shell中运行`payload_deliverer start`命令。
     :::

   - 使用[遥控器](#QGC遥控器配置)触发机械手开合动作。

1. 将[PD_GRIPPER_TO](../advanced_config/parameter_reference.md#PD_GRIPPER_TO)设置为机械手开合时间中较长的值。

### 任务指令超时

执行[载荷投递任务](../flying/package_delivery_mission.md)时，若机械手未报告开合状态，任务不应中止。这可能发生在机械手无反馈传感器、传感器损坏或UORB消息丢失时。

::: info
传感器反馈的机械手状态尚未实际支持，但未来可能实现。
:::

任务指令超时提供额外保护，在未收到机械手成功动作确认时继续执行任务。此超时也用于在无传感器反馈时为其他指令提供足够延迟，例如绞盘展开/收回和云台移动至任务指定方向。

设置超时：

1. 将[MIS_COMMAND_TOUT](../advanced_config/parameter_reference.md#MIS_COMMAND_TOUT)设置为大于[机械手动作超时](#机械手动作超时)的值。

## QGC遥控器配置

QGroundControl [遥控器](../config/joystick.md)配置允许将机械手动作映射到遥控器按钮，之后可手动开合机械手。

在QGroundControl中映射遥控器按钮：

1. 打开菜单：**QGC Logo（左上角）> 机体设置 > 遥控器 > 按钮分配** 选项卡。

   ![机械手动作映射](../../assets/config/gripper/qgc_gripper_actions_joystick.png)

1. 为选定的遥控器按钮分配`Gripper Open`和`Gripper Close`动作，如上图所示。

可通过点击映射按钮测试动作，并检查机械手移动。如果机械手未按预期移动，请检查载荷投递配置和执行器映射是否正确设置。