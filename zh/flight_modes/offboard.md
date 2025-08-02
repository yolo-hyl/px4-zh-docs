# 外部控制模式（通用/所有框架）

<img src="../../assets/site/position_fixed.svg" title="需要位置修正（例如GPS）" width="30px" />

机体遵循由飞行堆栈外部源（例如伴飞计算机）提供的位置、速度、加速度、姿态、姿态速率或推力/扭矩设定点。这些设定点可通过MAVLink（或MAVLink API如[MAVSDK](https://mavsdk.mavlink.io/)）或[ROS 2](../ros2/index.md)提供。

PX4要求外部控制器提供连续的2Hz"存活信号"，通过发送任何支持的MAVLink设定点消息或ROS 2 [OffboardControlMode](../msg_docs/OffboardControlMode.md)消息实现。PX4仅在接收到该信号超过1秒后才启用外部控制，若信号停止则会重新获取控制权。

::: info

- 该模式需要位置或姿态/姿态信息 - 例如GPS、光流、视觉-惯性里程计、运动捕捉系统等。
- 除切换模式外禁用遥控器控制（通过将参数[COM_RC_IN_MODE](../advanced_config/parameter_reference.md#COM_RC_IN_MODE)设为4: 禁用摇杆输入可完全禁用手动控制器）。
- 在外部模式下解锁或飞行时切换到外部模式前，机体必须已接收MAVLink设定点消息或ROS 2 [OffboardControlMode](../msg_docs/OffboardControlMode.md)消息流。
- 若MAVLink设定点消息或`OffboardControlMode`的接收速率低于2Hz，机体将退出外部模式。
- 并非所有MAVLink允许的坐标框架和字段值都适用于所有设定点消息和机体。
  请仔细阅读以下章节以确保仅使用支持的值。

:::

## 描述

外部控制模式通过设置位置、速度、加速度、姿态、姿态角速度或推力/扭矩设定点来控制机体运动和姿态。

PX4 必须以 2Hz 的频率接收 MAVLink 设定点消息或 ROS 2 的 [OffboardControlMode](../msg_docs/OffboardControlMode.md) 消息流，以证明外部控制器处于健康状态。  
在机体以外部控制模式起飞或切换至该模式前，至少需要持续发送 1 秒的消息流。  
当外部控制频率低于 2Hz 时，PX4 会在超时 ([COM_OF_LOSS_T](#COM_OF_LOSS_T)) 后退出外部控制模式，并尝试降落或执行其他安全措施。  
具体操作取决于遥控器控制是否可用，由参数 [COM_OBL_RC_ACT](#COM_OBL_RC_ACT) 定义。

使用 MAVLink 时，设定点消息同时传达外部源“存活”信号和设定点值。  
要保持当前位置，机体必须持续接收当前坐标的消息流。

使用 ROS 2 时，通过 [OffboardControlMode](../msg_docs/OffboardControlMode.md) 消息流证明外部源存活，而实际设定点则通过发布到 uORB 设定点主题（如 [TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md)）提供。  
要保持当前位置，机体只需接收 `OffboardControlMode` 消息流，但 `TrajectorySetpoint` 仅需发送一次。

需要注意的是，外部控制模式仅支持有限的 MAVLink 命令和消息。  
起飞、降落、返航等操作建议使用对应模式处理。  
上传/下载任务等操作可在任意模式下执行。

## ROS 2 消息

以下 ROS 2 消息及其特定字段和字段值可用于指定的帧。

除了提供心跳功能外，`OffboardControlMode` 还具有另外两个主要用途：

1. 控制 [PX4控制架构](../flight_stack/controller_diagrams.md) 中必须注入外部设定值的层级，并禁用被绕过的控制器。
1. 确定需要哪些有效估计值（位置或速度），以及应使用哪些设定值消息。

`OffboardControlMode` 消息的定义如下所示。

```sh
# 机载控制模式

uint64 timestamp		# 系统启动后经过的时间（微秒）

bool position
bool velocity
bool acceleration
bool attitude
bool body_rate
bool thrust_and_torque
bool direct_actuator
```
字段按优先级排序，`position`优先于`velocity`及后续字段，`velocity`优先于`acceleration`，依此类推。第一个非零值字段（从上至下）定义了使用外部控制模式所需的有效估计值和可使用的设定点消息。例如，若`acceleration`是第一个非零值字段，则PX4需要有效的`velocity estimate`，且设定点必须使用`TrajectorySetpoint`消息。

| 目标控制量           | position字段 | velocity字段 | acceleration字段 | attitude字段 | body_rate字段 | thrust_and_torque字段 | direct_actuator字段 | 所需估计值 | 所需消息                                                                                                                |
| ---------------------| ------------ | ------------ | ---------------- | ------------ | ------------- | --------------------- | ------------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------- |
| 位置 (NED)           | ✓            | -            | -                | -            | -             | -                     | -                   | position   | [TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md)                                                                         |
| 速度 (NED)           | ✗            | ✓            | -                | -            | -             | -                     | -                   | velocity   | [TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md)                                                                         |
| 加速度 (NED)         | ✗            | ✗            | ✓                | -            | -             | -                     | -                   | velocity   | [TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md)                                                                         |
| 姿态 (FRD)           | ✗            | ✗            | ✗                | ✓            | -             | -                     | -                   | none       | [VehicleAttitudeSetpoint](../msg_docs/VehicleAttitudeSetpoint.md)                                                               |
| 机体角速度 (FRD)     | ✗            | ✗            | ✗                | ✗            | ✓             | -                     | -                   | none       | [VehicleRatesSetpoint](../msg_docs/VehicleRatesSetpoint.md)                                                                     |
| 推力与扭矩 (FRD)     | ✗            | ✗            | ✗                | ✗            | ✗             | ✓                     | -                   | none       | [VehicleThrustSetpoint](../msg_docs/VehicleThrustSetpoint.md) 和 [VehicleTorqueSetpoint](../msg_docs/VehicleTorqueSetpoint.md) |
| 直接电机与舵机控制   | ✗            | ✗            | ✗                | ✗            | ✗             | ✗                     | ✓                   | none       | [ActuatorMotors](../msg_docs/ActuatorMotors.md) 和 [ActuatorServos](../msg_docs/ActuatorServos.md)                             |

其中 ✓ 表示该位被设置，✘ 表示该位未被设置，- 表示该位值无关。

::: info
在使用ROS 2的外部控制模式前，请花几分钟了解PX4与ROS 2使用的不同[坐标系约定](../ros2/user_guide.md#ros-2-px4-frame-conventions)。
:::

### 多旋翼

- [px4_msgs::msg::TrajectorySetpoint](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TrajectorySetpoint.msg)

  - 支持以下输入组合：

    - 位置设定点（`position`不等于`NaN`）。非`NaN`的`velocity`和`acceleration`值用于内环控制器的前馈项。
    - 速度设定点（`velocity`不等于`NaN`且`position`设为`NaN`）。非`NaN`的`acceleration`值用于内环控制器的前馈项。
    - 加速度设定点（`acceleration`不等于`NaN`且`position`和`velocity`设为`NaN`）

  - 所有值在NED（北、东、下）坐标系中解释，单位分别为[m]、[m/s]和[m/s²]。

- [px4_msgs::msg::VehicleAttitudeSetpoint](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleAttitudeSetpoint.msg)

  - 支持以下输入组合：

    - 四元数`q_d` + 推力设定点`thrust_body`。
      非`NaN`的`yaw_sp_move_rate`值用于表达地理坐标系中的前馈项，单位为[rad/s]。

  - 四元数表示机体FRD（前、右、下）坐标系与NED坐标系之间的旋转。推力在机体FRD坐标系中，以归一化[-1, 1]值表示。

- [px4_msgs::msg::VehicleRatesSetpoint](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleRatesSetpoint.msg)

  - 支持以下输入组合：

    - `roll`、`pitch`、`yaw`和`thrust_body`。

  - 所有值在机体FRD坐标系中。角速度单位为[rad/s]，推力归一化为[-1, 1]。

### 通用机体

以下外部控制模式会绕过所有内部PX4控制环，使用时需格外谨慎。

- [px4_msgs::msg::VehicleThrustSetpoint](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleThrustSetpoint.msg) + [px4_msgs::msg::VehicleTorqueSetpoint](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleTorqueSetpoint.msg)

  - 支持以下输入组合：
    - `xyz`表示推力和`xyz`表示扭矩。
  - 所有值在机体FRD坐标系中，归一化为[-1, 1]。

- [px4_msgs::msg::ActuatorMotors](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActuatorMotors.msg) + [px4_msgs::msg::ActuatorServos](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActuatorServos.msg)
  - 直接控制电机输出和/或舵机输出。
  - 当前在`control_allocator`模块之下工作。非外部控制模式下不要发布这些消息。
  - 所有值归一化为[-1, 1]。不支持负值的输出中，负值映射为`NaN`。
  - `NaN`映射为非激活状态。

## MAVLink Messages

以下MAVLink消息及其特定字段和字段值适用于指定的机体框架。

### 多旋翼/垂直起降飞行器

- [SET_POSITION_TARGET_LOCAL_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED)

  - 支持以下输入组合：<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/FlightTasks/tasks/Offboard/FlightTaskOffboard.cpp#L166-L170 -->

    - 位置设定点（仅 `x`, `y`, `z`）
    - 速度设定点（仅 `vx`, `vy`, `vz`）
    - 加速度设定点（仅 `afx`, `afy`, `afz`）
    - 位置设定点 **和** 速度设定点（速度设定点作为前馈使用；它被加到位置控制器的输出结果中，并作为速度控制器的输入）。
    - 位置设定点 **和** 速度设定点 **和** 加速度（速度和加速度设定点作为前馈使用；速度设定点被加到位置控制器的输出结果中，并作为速度控制器的输入；加速度设定点被加到速度控制器的输出结果中，并用于计算推力矢量）。

  - PX4 仅支持以下 `coordinate_frame` 值：[MAV_FRAME_LOCAL_NED](https://mavlink.io/en/messages/common.html#MAV_FRAME_LOCAL_NED) 和 [MAV_FRAME_BODY_NED](https://mavlink.io/en/messages/common.html#MAV_FRAME_BODY_NED)。

- [SET_POSITION_TARGET_GLOBAL_INT](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_GLOBAL_INT)

  - 支持以下输入组合：<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/FlightTasks/tasks/Offboard/FlightTaskOffboard.cpp#L166-L170 -->

    - 位置设定点（仅 `lat_int`, `lon_int`, `alt`）
    - 速度设定点（仅 `vx`, `vy`, `vz`）
    - _推力_ 设定点（仅 `afx`, `afy`, `afz`）

      ::: info
      加速度设定点值被映射以生成标准化的推力设定点（即加速度设定点未被"完全"支持）。
      :::

    - 位置设定点 **和** 速度设定点（速度设定点作为前馈使用；它被加到位置控制器的输出结果中，并作为速度控制器的输入）。

  - PX4 仅支持以下 `coordinate_frame` 值：[MAV_FRAME_GLOBAL](https://mavlink.io/en/messages/common.html#MAV_FRAME_GLOBAL)。

- [SET_ATTITUDE_TARGET](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET)
  - 支持以下输入组合：
    - 姿态/方向 (`SET_ATTITUDE_TARGET.q`) 和推力设定点 (`SET_ATTITUDE_TARGET.thrust`)。
    - 机体角速度 (`SET_ATTITUDE_TARGET` `.body_roll_rate` ,`.body_pitch_rate`, `.body_yaw_rate`) 和推力设定点 (`SET_ATTITUDE_TARGET.thrust`)。

### 固定翼飞机

- [SET_POSITION_TARGET_LOCAL_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED)

  - 支持以下输入组合（通过 `type_mask`）：<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/FlightTasks/tasks/Offboard/FlightTaskOffboard.cpp#L166-L170 -->

    - 位置设定点（仅 `x`, `y`, `z`；速度和加速度设定点被忽略）。

      - 通过 `type_mask` 指定设定点的 _类型_（如果未设置这些位，机体将呈花状飞行模式）：
        ::: info
        下方部分 _设定点类型_ 值未包含在 MAVLink 的 `type_mask` 字段标准中。
        :::

        值如下：

        - 292：滑翔设定点。
          此配置使 TECS 优先考虑空速而非高度，以便在无推力时使机体滑翔（即通过俯仰角控制调节空速）。
          等效于设置 `type_mask` 为 `POSITION_TARGET_TYPEMASK_Z_IGNORE`、`POSITION_TARGET_TYPEMASK_VZ_IGNORE`、`POSITION_TARGET_TYPEMASK_AZ_IGNORE`。
        - 4096：起飞设定点。
        - 8192：着陆设定点。
        - 12288：盘旋设定点（围绕设定点飞行圆形轨迹）。
        - 16384：空转设定点（油门归零，滚转/俯仰归零）。

  - PX4 支持的坐标系（`coordinate_frame` 字段）：[MAV_FRAME_LOCAL_NED](https://mavlink.io/en/messages/common.html#MAV_FRAME_LOCAL_NED) 和 [MAV_FRAME_BODY_NED](https://mavlink.io/en/messages/common.html#MAV_FRAME_BODY_NED)。

- [SET_POSITION_TARGET_GLOBAL_INT](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_GLOBAL_INT)

  - 支持以下输入组合（通过 `type_mask`）：<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/FlightTasks/tasks/Offboard/FlightTaskOffboard.cpp#L166-L170 -->

    - 位置设定点（仅 `lat_int`, `lon_int`, `alt`）

      - 通过 `type_mask` 指定设定点的 _类型_（如果未设置这些位，机体将呈花状飞行模式）：

        ::: info
        下方 _设定点类型_ 值未包含在 MAVLink 的 `type_mask` 字段标准中。
        :::

        值如下：

        - 4096：起飞设定点。
        - 8192：着陆设定点。
        - 12288：盘旋设定点（机体距离设定点足够近时停止）。

  - PX4 支持的坐标系（`coordinate_frame` 字段）：[MAV_FRAME_GLOBAL](https://mavlink.io/en/messages/common.html#MAV_FRAME_GLOBAL)。

- [SET_ATTITUDE_TARGET](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET)
  - 支持以下输入组合：
    - 姿态/方向 (`SET_ATTITUDE_TARGET.q`) 和推力设定点 (`SET_ATTITUDE_TARGET.thrust`)。
      ::: info
      仅实际使用/提取了偏航设置。
      :::

### 地面车

- [SET_POSITION_TARGET_LOCAL_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED)

  - 支持以下输入组合：<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/FlightTasks/tasks/Offboard/FlightTaskOffboard.cpp#L166-L170 -->
    - 位置设定点（仅 `x`, `y`, `z`）

- [SET_POSITION_TARGET_GLOBAL_INT](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_GLOBAL_INT)

  - 支持以下输入组合：<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/FlightTasks/tasks/Offboard/FlightTaskOffboard.cpp#L166-L170 -->
    - 位置设定点（仅 `lat_int`, `lon_int`, `alt`）
  - 通过 `type_mask` 指定设定点的 _类型_（不属于 MAVLink 标准）。
    值如下：

    - 未设置以下位则为正常行为。
    - 12288：盘旋设定点（机体距离设定点足够近时停止）。

  - PX4 支持的坐标系（`coordinate_frame` 字段）：[MAV_FRAME_GLOBAL](https://mavlink.io/en/messages/common.html#MAV_FRAME_GLOBAL)。

- [SET_ATTITUDE_TARGET](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET)
  - 支持以下输入组合：
    - 姿态/方向 (`SET_ATTITUDE_TARGET.q`) 和推力设定点 (`SET_ATTITUDE_TARGET.thrust`)。
      ::: info
      仅实际使用/提取了偏航设置。
      :::

## 外部控制参数

_外部控制模式_ 受以下参数影响：

| 参数名称                                                                                                                       | 描述                                                                                                                                                                                                 |
|-----------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <a id="COM_OF_LOSS_T"></a>[COM_OF_LOSS_T](../advanced_config/parameter_reference.md#COM_OF_LOSS_T)                             | 外部连接丢失后触发外部控制丢失故障安全措施(`COM_OBL_RC_ACT`)的等待时间(单位:秒)                                                                                                                     |
| <a id="COM_OBL_RC_ACT"></a>[COM_OBL_RC_ACT](../advanced_config/parameter_reference.md#COM_OBL_RC_ACT)                          | 外部控制丢失时切换的飞行模式(取值为 - `0`: _Position_, `1`: _Altitude_, `2`: _Manual_, `3`: *Return, `4`: *Land\*)                                                                                   |
| <a id="COM_RC_OVERRIDE"></a>[COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE)                       | 控制多旋翼(或 VTOL 在 MC mode 模式下)摇杆移动是否会导致切换到[Position mode](../flight_modes_mc/position.md)模式。默认情况下在外部控制模式中未启用此功能。                                            |
| <a id="COM_RC_STICK_OV"></a>[COM_RC_STICK_OV](../advanced_config/parameter_reference.md#COM_RC_STICK_OV)                         | 导致切换到[Position mode](../flight_modes_mc/position.md)模式的摇杆移动幅度(如果[COM_RC_OVERRIDE](#COM_RC_OVERRIDE)已启用)。                                                                                     |
| <a id="COM_RCL_EXCEPT"></a>[COM_RCL_EXCEPT](../advanced_config/parameter_reference.md#COM_RCL_EXCEPT)                            | 指定忽略遥控器信号丢失并不会触发故障安全动作的模式。设置位`2`以在外部控制模式中忽略遥控器信号丢失。                                                                                                   |

## 开发者资源

通常情况下，开发者不会直接在MAVLink层进行开发，而是使用如[MAVSDK](https://mavsdk.mavlink.io/)或[ROS](http://www.ros.org/)等机器人API（这些工具提供了开发者友好的API，并负责管理连接、发送消息和监控响应等底层操作——这些正是在使用_外部模式_和MAVLink时需要处理的细节）。

以下资源对开发者群体可能有帮助：

- [Linux下的外部控制](../ros/offboard_control.md)
- [ROS/MAVROS 外部控制示例 (C++)](../ros/mavros_offboard_cpp.md)
- [ROS/MAVROS 外部控制示例 (Python)](../ros/mavros_offboard_python.md)