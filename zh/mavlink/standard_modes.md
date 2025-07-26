# 标准模式协议（Standard Modes Protocol）

<Badge type="tip" text="PX4 v1.15" />

PX4 从 v1.15 版本开始实现 MAVLink 的 [标准模式协议](https://mavlink.io/en/services/standard_modes.md)，QGroundControl 每日构建版本（及未来发布版本）中也包含对应的实现。该协议允许您发现机体支持的所有飞行模式（包括通过 [PX4 ROS 2 Control Interface](../ros2/px4_ros2_control_interface.md) 创建的 PX4 External modes），并获取或设置当前模式。

## 消息与命令概述

[标准模式](https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE)是大多数飞控栈通用的模式，具有基本一致的行为，而自定义模式则是特定于飞控栈的。

该协议允许：

- 通过PX4和ROS 2发现系统支持的所有模式([MAV_CMD_REQUEST_MESSAGE](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_MESSAGE)和[AVAILABLE_MODES](https://mavlink.io/en/messages/common.html#AVAILABLE_MODES))。
- 发现当前模式([CURRENT_MODE](https://mavlink.io/en/messages/common.html#CURRENT_MODE))。
- 使用[MAV_CMD_DO_SET_STANDARD_MODE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_STANDARD_MODE)设置标准模式([MAV_STANDARD_MODE](https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE))（推荐）。
- 当模式集合变化时的通知([AVAILABLE_MODES_MONITOR](https://mavlink.io/en/messages/common.html#AVAILABLE_MODES_MONITOR))。

你也可以通过[SET_MODE](https://mavlink.io/en/messages/common.html#SET_MODE)和来自`AVAILABLE_MODES`的自定义模式信息来设置自定义模式。
截至撰写时[MAV_CMD_DO_SET_MODE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_MODE)尚未被支持。

## 支持的标准模式

PX4 支持以下标准飞行模式，这意味着你可以通过调用 [MAV_CMD_DO_SET_STANDARD_MODE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_STANDARD_MODE) 并使用适当的 [MAV_STANDARD_MODE](https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE) 枚举值来启动这些模式。

### 多旋翼标准模式

多旋翼机体支持以下标准模式（其中部分模式由所有机型支持）：

| 标准模式                                                                 | PX4模式                                                  | 内部模式 |
|--------------------------------------------------------------------------|----------------------------------------------------------|----------|
| [MAV_STANDARD_MODE_POSITION_HOLD][MAV_STANDARD_MODE_POSITION_HOLD]     | [多旋翼位置模式](../flight_modes_mc/position.md)         | POSCTL   |
| [MAV_STANDARD_MODE_ALTITUDE_HOLD][MAV_STANDARD_MODE_ALTITUDE_HOLD]     | [多旋翼高度模式](../flight_modes_mc/altitude.md)         | ALTCTL   |
| [MAV_STANDARD_MODE_ORBIT][MAV_STANDARD_MODE_ORBIT]                     | [固定翼环绕模式](../flight_modes_mc/orbit.md)            | POSCTL   |
| [MAV_STANDARD_MODE_SAFE_RECOVERY][MAV_STANDARD_MODE_SAFE_RECOVERY]     | [返回模式](../flight_modes/return.md)                    | AUTO_RTL |
| [MAV_STANDARD_MODE_MISSION][MAV_STANDARD_MODE_MISSION]                 | [任务模式](../flight_modes_mc/mission.md)                | AUTO_MISSION |
| [MAV_STANDARD_MODE_LAND][MAV_STANDARD_MODE_LAND]                       | [降落模式](../flight_modes_mc/land.md)                   | AUTO_LAND |
| [MAV_STANDARD_MODE_TAKEOFF][MAV_STANDARD_MODE_TAKEOFF]                 | [起飞模式](../flight_modes_mc/takeoff.md)                | AUTO_TAKEOFF |

[MAV_STANDARD_MODE_POSITION_HOLD]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_POSITION_HOLD
[MAV_STANDARD_MODE_ORBIT]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_ORBIT
[MAV_STANDARD_MODE_ALTITUDE_HOLD]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_ALTITUDE_HOLD

### 固定翼标准模式

固定翼机体支持以下标准模式（其中部分模式由所有机型支持）：

| 标准模式                                                                 | PX4模式                                                  | 内部模式 |
|--------------------------------------------------------------------------|----------------------------------------------------------|----------|
| [MAV_STANDARD_MODE_CRUISE][MAV_STANDARD_MODE_CRUISE]                   | [固定翼位置模式](../flight_modes_fw/position.md)         | POSCTL   |
| [MAV_STANDARD_MODE_ALTITUDE_HOLD][MAV_STANDARD_MODE_ALTITUDE_HOLD]     | [固定翼高度模式](../flight_modes_fw/altitude.md)         | ALTCTL   |
| [MAV_STANDARD_MODE_ORBIT][MAV_STANDARD_MODE_ORBIT]                     | [固定翼悬停模式](../flight_modes_fw/hold.md)             | AUTO_LOITER |
| [MAV_STANDARD_MODE_SAFE_RECOVERY][MA- STANDARD_MODE_SAFE_RECOVERY]     | [返回模式](../flight_modes/return.md)                    | AUTO_RTL |
| [MAV_STANDARD_MODE_MISSION][MAV_STANDARD_MODE_MISSION]                 | [任务模式](../flight_modes_fw/mission.md)                | AUTO_MISSION |
| [MAV_STANDARD_MODE_LAND][MAV_STANDARD_MODE_LAND]                       | [降落模式](../flight_modes_fw/land.md)                   | AUTO_LAND |
| [MAV_STANDARD_MODE_TAKEOFF][MAV_STANDARD_MODE_TAKEOFF]                 | [起飞模式](../flight_modes_fw/takeoff.md)                | AUTO_TAKEOFF |

[MAV_STANDARD_MODE_CRUISE]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_CRUISE

### 垂直起降标准模式

垂直起降机体支持以下标准模式（其中部分模式由所有机型支持）。请注意机体的行为会根据其以多旋翼模式还是固定翼模式飞行而有所不同。

| 标准模式                                                                 | PX4模式                                                  | 内部模式 |
|--------------------------------------------------------------------------|----------------------------------------------------------|----------|
| [MAV_STANDARD_MODE_ALTITUDE_HOLD][MAV_STANDARD_MODE_ALTITUDE_HOLD]     | [固定翼高度模式](../flight_modes_fw/altitude.md)         | ALTCTL   |
| [MAV_STANDARD_MODE_SAFE_RECOVERY][MAV_STANDARD_MODE_SAFE_RECOVERY]     | [返回模式](../flight_modes/return.md)                    | AUTO_RTL |
| [MAV_STANDARD_MODE_MISSION][MAV_STANDARD_MODE_MISSION]                 | [任务模式](../flight_modes_mc/mission.md)                | AUTO_MISSION |
| [MAV_STANDARD_MODE_LAND][MAV_STANDARD_MODE_LAND]                       | [降落模式](../flight_modes_mc/land.md)                   | AUTO_LAND |
| [MAV_STANDARD_MODE_TAKEOFF][MAV_STANDARD_MODE_TAKEOFF]                 | [起飞模式](../flight_modes_mc/takeoff.md)                | AUTO_TAKEOFF  |

::: info
请注意VTOL机体也可能支持`MAV_STANDARD_MODE_CRUISE`（固定翼）或`MAV_STANDARD_MODE_POSITION_HOLD`（多旋翼）及`MAV_STANDARD_MODE_ORBIT`，但尚未实现。
:::

### 所有机型模式

这些标准模式映射适用于所有机体类型。

| 标准模式                                                                 | PX4模式                                      | 内部模式 |
|--------------------------------------------------------------------------|---------------------------------------------|----------|
| [MAV_STANDARD_MODE_SAFE_RECOVERY][MAV_STANDARD_MODE_SAFE_RECOVERY]     | [返回模式](../flight_modes/return.md)        | AUTO_RTL |
| [MAV_STANDARD_MODE_MISSION][MAV_STANDARD_MODE_MISSION]                 | [任务模式](../flight_modes_mc/mission.md)    | AUTO_MISSION |
| [MAV_STANDARD_MODE_LAND][MAV_STANDARD_MODE_LAND]                       | [降落模式](../flight_modes_mc/land.md)       | AUTO_LAND |
| [MAV_STANDARD_MODE_TAKEOFF][MAV_STANDARD_MODE_TAKEOFF]                 | [起飞模式](../flight_modes_mc/takeoff.md)    | AUTO_TAKEOFF |

[MAV_STANDARD_MODE_SAFE_RECOVERY]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_SAFE_RECOVERY
[MAV_STANDARD_MODE_MISSION]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_MISSION
[MAV_STANDARD_MODE_LAND]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_LAND
[MAV_STANDARD_MODE_TAKEOFF]: https://mavlink.io/en/messages/common.html#MAV_STANDARD_MODE_TAKEOFF

## 标准模式实现

PX4会在适当情况下将其自定义模式以标准模式形式呈现。

实现标准模式映射时，请参见[src/lib/modes/standard_modes.hpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/modes/standard_modes.hpp)，特别是`getNavStateFromStandardMode()`的实现。

注意通过[PX4 ROS 2 Control Interface](../ros2/px4_ros2_control_interface.md)创建的PX4外部模式也会通过此接口暴露。

<!--
- 模式如何添加到可用模式集合中 - 开发者在定义新模式时是否需要执行特殊操作？
- 其特性是如何设置的？
- 当模式集合发生变化时如何通知？创建新模式时是否需要执行操作？

- [PX4-Autopilot#24011: standard_modes: add vehicle-type specific standard modes](https://github.com/PX4/PX4-Autopilot/pull/24011)

-->

## 其他 MAVLink 模式更改命令

部分模式（包括标准模式和自定义模式）也可以通过特定命令和消息进行设置。  
这种方式比直接启动模式更便捷，尤其是在消息允许配置额外设置时。

PX4 支持以下命令对部分机体类型进行模式更改：

- [MAV_CMD_NAV_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_TAKEOFF) — 起飞模式  
- [MAV_CMD_NAV_RETURN_TO_LAUNCH](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_RETURN_TO_LAUNCH) — 返回模式  
- [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND) — 着陆模式  
- [MAV_CMD_DO_FOLLOW](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_FOLLOW) — 多旋翼跟随模式（MC Follow mode）  
- [MAV_CMD_DO_FOLLOW_REPOSITION](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_FOLLOW_REPOSITION) — 跟随模式（从新位置）  
- [MAV_CMD_DO_ORBIT](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_ORBIT) — 多旋翼环绕模式（MC Orbit mode）  
- [MAV_CMD_NAV_VTOL_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_TAKEOFF) — 垂直起降专用起飞模式  
- [MAV_CMD_DO_REPOSITION](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_REPOSITION)  
- [MAV_CMD_DO_PAUSE_CONTINUE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_PAUSE_CONTINUE) — 通过进入悬停/盘旋模式暂停任务  
- [MAV_CMD_MISSION_START](https://mavlink.io/en/messages/common.html#MAV_CMD_MISSION_START) — 启动任务  

请注意，这些命令早于“标准模式”出现，并映射到机体特定的自定义模式。这意味着即使某个标准模式适用于多种机体类型，该命令可能仅对特定机体有效。例如，Orbit 标准模式在多旋翼上映射为 Orbit 模式，在固定翼上映射为悬停/盘旋模式；但目前 `MAV_CMD_DO_ORBIT` 命令会触发多旋翼的 Orbit 模式，在固定翼上则被忽略。