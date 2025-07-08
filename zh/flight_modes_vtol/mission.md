# 任务模式（VTOL）

<img src="../../assets/site/position_fixed.svg" title="需要全球定位（例如GPS）" width="30px" />

_任务模式_ 会促使机体执行一个预设的自主[任务](../flying/missions.md)（飞行计划），该任务已上传到飞控器。
任务通常通过地面控制站（GCS）应用如 [QGroundControl](https://docs.qgroundcontrol.com/master/en/) (QGC) 创建和上传。

VTOL机体在FW模式下遵循固定翼的行为和参数，在MC模式下则遵循多旋翼的行为。
更多信息请参阅各模式的特定文档：

- [任务模式（MC）](../flight_modes_mc/mission.md)
- [任务模式（FW）](../flight_modes_fw/mission.md)

以下章节概述了VTOL特定的任务模式行为。

## 任务指令

以下VTOL特定指令按MAVLink规范定义。

- [MAV_CMD_NAV_VTOL_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_TAKEOFF)
  - `MAV_CMD_NAV_VTOL_TAKEOFF.param2`（转换航向）被忽略。
    实际使用下一个航点的航向作为转换航向。<!-- 至少在PX4 v1.13之前：https://github.com/PX4/PX4-Autopilot/issues/12660 -->
- [MAV_CMD_NAV_VTOL_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_LAND)
- [MAV_CMD_DO_VTOL_TRANSITION](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_VTOL_TRANSITION)

在相应模式下，PX4通常会"接受"固定翼或多旋翼的任务指令。

在固定翼模式下有以下例外情况：

- [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND) 会被转换为 [MAV_CMD_NAV_VTOL_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_LAND)，除非 [NAV_FORCE_VT](../advanced_config/parameter_reference.md#NAV_FORCE_VT) 设置为 `0`（禁用）。
- [MAV_CMD_NAV_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_TAKEOFF) 不被支持。

## 任务指令超时

某些任务指令/项目可能需要较长时间完成，比如机械爪的开合、绞盘的展开/收回或云台对感兴趣区域的指向。

当提供时，PX4会通过硬件的传感器反馈来判断动作是否完成，然后进入下一个任务项目。
若未提供反馈或反馈丢失，则可通过任务指令超时机制确保这些动作不会阻塞任务流程，而是继续执行下一个项目。

超时时间通过 [MIS_COMMAND_TOUT](../advanced_config/parameter_reference.md#MIS_COMMAND_TOUT) 参数设置。
该参数应略大于任务中最长持续动作所需的时间。

## 任务起飞

通过在地图中添加 `VTOL Takeoff` 任务项目来规划VTOL任务起飞。

执行任务时，机体将垂直上升至 [MIS_TAKEOFF_ALT](../advanced_config/parameter_reference.md#MIS_TAKEOFF_ALT) 参数定义的最小起飞高度，然后按照任务项目中定义的航向转换为固定翼模式。
转换完成后，机体将朝向任务项目中定义的三维位置飞行。

VTOL任务起飞需要 `VTOL Takeoff` 任务项目（`MAV_CMD_NAV_VTOL_TAKEOFF`）（或当机体处于MC模式时使用 `MAV_CMD_NAV_TAKEOFF`）；如果机体在任务开始时已处于飞行状态，则起飞项目将被视为普通航点。

## 参见

- [任务模式（MC）](../flight_modes_mc/mission.md)
- [任务模式（FW）](../flight_modes_fw/mission.md)
- [任务](../flying/missions.md)
  - [包裹投送任务](../flying/package_delivery_mission.md)