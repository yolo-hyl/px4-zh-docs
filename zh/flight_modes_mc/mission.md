# 任务模式（多旋翼）

<img src="../../assets/site/position_fixed.svg" title="Global position fix required (e.g. GPS)" width="30px" />

_任务模式_ 会令机体执行预设的自主[任务](../flying/missions.md)（飞行计划），该计划需上传至飞控。
任务通常通过地面控制站（GCS）应用创建并上传，例如 [QGroundControl](https://docs.qgroundcontrol.com/master/en/) (QGC)。

::: info

- 该模式需要全局三维位置估算（通过GPS或基于[局部位置](../ros/external_position_estimation.md#enabling-auto-modes-with-a-local-position)推算）。
- 机体必须解锁后才能进入此模式。
- 该模式为自动模式 - 控制机体无需用户干预。
- 通过遥控器模式切换开关可改变任何机体的飞行模式。
- 默认情况下，遥控器摇杆操作会将机体切换至[Position mode](../flight_modes_mc/position.md)模式（除非处理电池紧急情况）。
  这适用于多旋翼和处于MC模式的VTOL。

:::

## 描述

任务通常在地面控制站（例如 [QGroundControl](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/plan_view/plan_view.html)）中创建并上传。
它们也可以通过 MAVLink API（如 [MAVSDK](../robotics/mavsdk.md)）创建，和/或在飞行中上传。

单个 [任务指令](#mission-commands) 的处理方式会根据多旋翼飞行特性进行调整（例如盘旋操作实现为_悬停_）。

::: info
任务需上传至 SD 卡，且需在启动自动驾驶仪**之前**插入 SD 卡。
:::

当启用 MISSION 模式时，所有机体类型在高层面上的行为一致：

1. 如果未存储任务，或 PX4 已完成所有任务指令的执行，或 [任务不可行](#mission-feasibility-checks)：

   - 如果正在飞行，机体将悬停。
   - 如果已着陆，机体将"等待"。

1. 如果已存储任务且 PX4 正在飞行：

   - PX4 将从当前步骤执行 [任务/飞行计划](../flying/missions.md)。
   - `TAKEOFF` 指令被视为普通航点。
1. 如果已存储任务且 PX4 已着陆：
   - PX4 将执行 [任务/飞行计划](../flying/missions.md)。
   - 如果任务中没有 `TAKEOFF` 指令，PX4 将先将机体升至最低高度，再从当前步骤执行剩余飞行计划。
1. 如果未存储任务，或 PX4 已完成所有任务指令的执行：
   - 如果正在飞行，机体将悬停。
   - 如果已着陆，机体将"等待"。
1. 可通过在 _QGroundControl_ 中选择目标指令手动更改当前任务指令。

   ::: info
   如果任务中包含 _Jump to item_ 指令，切换至其他指令时**不会**重置循环计数器。
   一个影响是，如果将当前任务指令改为 1，任务不会"完全重新开始"。
   :::

1. 任务仅在机体解除武装或上传新任务时重置。

   :::tip
   要在机体着陆后自动解除武装，在 _QGroundControl_ 中进入 [Vehicle Setup > Safety](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/safety.html)，导航至 _Land Mode Settings_ 并勾选标记为 _Disarm after_ 的选项。
   输入着陆后解除武装前等待的时间。
   :::

任务可通过切换至其他模式（如 [Hold mode](../flight_modes_mc/hold.md) 或 [Position mode](../flight_modes_mc/position.md)）暂停，再切换回任务模式可恢复执行。
如果在暂停时尚未拍摄图像，恢复后机体将从当前位点朝向原目标航点飞行。
如果在暂停时正在拍摄图像（包含相机触发指令），恢复后机体将从当前位点朝向暂停前的最后一个航点飞行，然后以相同速度和相机触发行为重新执行路径。
这确保了在测绘/相机任务中路径规划的完整性。
任务可在机体暂停时上传，在此情况下当前活动任务指令将重置为 1。

::: info
当相机正在触发时任务被暂停，PX4 会将当前活动任务指令设置为前一个航点，因此任务恢复时机体将重新执行上一段任务路径。
此外，PX4 会存储已执行任务指令的速度设置和相机触发设置（来自已完成的任务计划），并在任务恢复时重新应用这些设置。
:::

:::warning
切换至任何遥控模式前请确保油门杆非零（否则机体将坠毁）。
建议在切换至其他模式前将控制杆居中。
:::

有关任务规划的更多信息，请参见：

- [任务规划](../flying/missions.md)
- [规划视图](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/plan_view/plan_view.html) (_QGroundControl_ 用户指南)

## 任务可行性检查

PX4 在任务上传时以及执行任务前会运行一些基础的可行性检查。  
如果任何检查失败，用户将收到通知且无法启动任务（机体将切换至 [Hold mode](../flight_modes_mc/hold.md) 而非任务模式）。

以下列出最重要的检查项：

- 任何任务项与任务计划或安全地理围栏冲突  
- 缺少起飞和/或降落项（当这些项被配置为必填项时 [MIS_TKO_LAND_REQ](#MIS_TKO_LAND_REQ)）

此外还会检查第一个航点是否离家点过远 ([MIS_DIST_1WP](#MIS_DIST_1WP))。  
若检查失败用户将收到通知，但不会影响任务计划的有效性，即任务仍可启动，即使距离超出限制。

## QGroundControl 支持

_QGroundControl_ 提供了额外的地面控制站级别任务处理支持（除了飞控器提供的支持之外）。

更多相关信息请参见：

- [在机体着陆后移除任务](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/releases/stable_v3.2_long.html#remove-mission-after-vehicle-lands)
- [从返航模式后恢复任务](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/releases/stable_v3.2_long.html#resume-mission)

## 任务参数

任务行为受多个参数影响，其中大部分参数在[参数参考 > 任务](../advanced_config/parameter_reference.md#mission)中进行了说明。  
以下列出了一小部分相关参数。

通用参数：

| 参数                                                                                                         | 描述                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="NAV_RCL_ACT"></a>[NAV_RCL_ACT](../advanced_config/parameter_reference.md#NAV_RCL_ACT)                | 遥控丢失故障安全模式（当机体失去遥控连接时的行为）- 例如进入悬停模式、返航模式、终止等。                                                                                                                                                                                 |
| <a id="COM_RC_OVERRIDE"></a>[COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE)  | 控制多旋翼机（或垂直起降在多旋翼模式下）的手动杆动作是否在[位置模式](../flight_modes_mc/position.md)中将控制权交还给飞行员。此功能可分别针对自动模式和外部模式启用，默认在自动模式中启用。                                                                                          |
| <a id="COM_RC_STICK_OV"></a>[COM_RC_STICK_OV](../advanced_config/parameter_reference.md#COM_RC_STICK_OV)  | 触发切换到[位置模式](../flight_modes_mc/position.md)的摇杆移动量（如果[COM_RC_OVERRIDE](#COM_RC_OVERRIDE)已启用）。                                                                                                                                     |

与[任务可行性检查](#mission-feasibility-checks)相关的参数：

| 参数                                                                                                           | 描述                                                                                                                         |
| ------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| <a id="MIS_DIST_1WP"></a>[MIS_DIST_1WP](../advanced_config/parameter_reference.md#MIS_DIST_1WP)               | 如果第一个航点与Home点的距离超过此值，将发出警告信息。如果值为0或更小，则禁用。                                                  |
| <a id="FW_LND_ANG"></a>[FW_LND_ANG](../advanced_config/parameter_reference.md#FW_LND_ANG)                     | 最大降落坡度角。                                                                                                              |
| <a id="MIS_TKO_LAND_REQ"></a>[MIS_TKO_LAND_REQ](../advanced_config/parameter_reference.md#MIS_TKO_LAND_REQ) | 设置任务是否需要起飞和/或降落项。默认情况下多旋翼机无此要求。                                                                 |## 任务命令 {#mission_commands}

PX4 在任务模式下"接受"以下 MAVLink 任务命令（部分命令有特殊说明，在列表后给出）。  
除非另有说明，其具体实现均遵循 MAVLink 规范定义。

任务项：

- [MAV_CMD_NAV_WAYPOINT](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_WAYPOINT)  
  - _参数3_（飞越模式）将被忽略。当 _参数1_（停留时间）> 0 时，飞越模式始终启用。
- [MAV_CMD_NAV_LOITER_UNLIM](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LOITER_UNLIM)
- [MAV_CMD_NAV_LOITER_TIME](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LOITER_TIME)
- [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND)
- [MAV_CMD_NAV_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_TAKEOFF)
- [MAV_CMD_NAV_LOITER_TO_ALT](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LOITER_TO_ALT)
- [MAV_CMD_DO_JUMP](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_JUMP)
- [MAV_CMD_NAV_ROI](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_ROI)
- [MAV_CMD_DO_SET_ROI](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ROI)
- [MAV_CMD_DO_SET_ROI_LOCATION](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ROI_LOCATION)
- [MAV_CMD_DO_SET_ROI_WPNEXT_OFFSET](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ROI_WPNEXT_OFFSET)
- [MAV_CMD_DO_SET_ROI_NONE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ROI_NONE)
- [MAV_CMD_DO_CHANGE_SPEED](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_CHANGE_SPEED)
- [MAV_CMD_DO_SET_HOME](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_HOME)
- [MAV_CMD_DO_SET_SERVO](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_SERVO)
- [MAV_CMD_DO_LAND_START](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_LAND_START)
- [MAV_CMD_DO_TRIGGER_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_TRIGGER_CONTROL)
- [MAV_CMD_DO_DIGICAM_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_DIGICAM_CONTROL)
- [MAV_CMD_DO_MOUNT_CONFIGURE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_MOUNT_CONFIGURE)
- [MAV_CMD_DO_MOUNT_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_MOUNT_CONTROL)
- [MAV_CMD_IMAGE_START_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_IMAGE_START_CAPTURE)
- [MAV_CMD_IMAGE_STOP_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_IMAGE_STOP_CAPTURE)
- [MAV_CMD_VIDEO_START_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_VIDEO_START_CAPTURE)
- [MAV_CMD_VIDEO_STOP_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_VIDEO_STOP_CAPTURE)
- [MAV_CMD_DO_SET_CAM_TRIGG_DIST](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_DIST)
- [MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL)
- [MAV_CMD_SET_CAMERA_MODE](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_MODE)
- [MAV_CMD_NAV_DELAY](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_DELAY)
- [MAV_CMD_NAV_RETURN_TO_LAUNCH](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_RETURN_TO_LAUNCH)
- [MAV_CMD_DO_CONTROL_VIDEO](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_CONTROL_VIDEO)
- [MAV_CMD_DO_GIMBAL_MANAGER_PITCHYAW](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_GIMBAL_MANAGER_PITCHYAW)
- [MAV_CMD_DO_GIMBAL_MANAGER_CONFIGURE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_GIMBAL_MANAGER_CONFIGURE)
- [MAV_CMD_OBLIQUE_SURVEY](https://mavlink.io/en/messages/common.html#MAV_CMD_OBLIQUE_SURVEY)
- [MAV_CMD_SET_CAMERA_ZOOM](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_ZOOM)
- [MAV_CMD_SET_CAMERA_FOCUS](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_FOCUS)
- [MAV_CMD_NAV_VTOL_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_TAKEOFF)  
  - `MAV_CMD_NAV_VTOL_TAKEOFF.param2`（转换航向）将被忽略。  
    转换航向将使用下一个航点的航向。 <!-- 至少在 PX4 v1.13 之前是这样：https://github.com/PX4/PX4-Autopilot/issues/12660 -->

地理围栏定义：

- [MAV_CMD_NAV_FENCE_RETURN_POINT](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_FENCE_RETURN_POINT)
- [MAV_CMD_NAV_FENCE_POLYGON_VERTEX_INCLUSION](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_FENCE_POLYGON_VERTEX_INCLUSION)
- [MAV_CMD_NAV_FENCE_POLYGON_VERTEX_EXCLUSION](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_FENCE_POLYGON_VERTEX_EXCLUSION)
- [MAV_CMD_NAV_FENCE_CIRCLE_INCLUSION](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_FENCE_CIRCLE_INCLUSION)
- [MAV_CMD_NAV_FENCE_CIRCLE_EXCLUSION](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_FENCE_CIRCLE_EXCLUSION)

集合点：

- [MAV_CMD_NAV_RALLY_POINT](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_RALLY_POINT)

::: info
如果发现缺少或不正确的消息，请添加问题报告或PR。

- PX4 会解析上述消息，但不一定会实际执行。例如，部分消息特定于机体类型。
- PX4 不支持任务命令的本地坐标系（例如 [MAV_FRAME_LOCAL_NED](https://mavlink.io/en/messages/common.html#MAV_FRAME_LOCAL_NED)）。
- 并非所有消息/命令都通过 _QGroundControl_ 暴露。
- 随着新消息的加入，列表可能会过时。  
  可通过检查代码获取最新支持情况。  
  实现位于 `MavlinkMissionManager::parseCommand()`，路径为 [src/modules/mavlink/mission/mavlink_mission.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mission/mavlink_mission.cpp)。  
  命令解析器位于 `MavlinkMission::handleCommand()`。

:::

## 任务命令超时

某些任务命令/项可能需要较长时间才能完成，例如夹爪的开合、绞盘的展开或收回，或云台移动以对准感兴趣的区域。

当硬件提供传感器反馈时，PX4 可能会利用这些反馈来判断动作是否完成，然后继续执行下一个任务项。
如果没有提供反馈或反馈丢失时，可以使用任务命令超时机制来确保这些动作能够顺利进入下一个任务项，而不是阻塞任务进度。

超时时间通过参数 [MIS_COMMAND_TOUT](../advanced_config/parameter_reference.md#MIS_COMMAND_TOUT) 进行设置。
该参数值应略大于任务中最长持续动作的完成所需时间。

## 圆角转弯：航点间轨迹

PX4 期望遵循从上一个航点到当前目标的直线路径（它不会在航点之间规划其他类型的路径 - 如果需要其他路径，可通过添加额外航点来模拟实现）。

多旋翼机体在接近或离开航点时，其速度变化将基于 [jerk-limited](../config_mc/mc_jerk_limited_type_trajectory.md#auto-mode) 参数调整。
机体将沿着由接受半径（[NAV_ACC_RAD](../advanced_config/parameter_reference.md#NAV_ACC_RAD)）定义的平滑圆角曲线朝向下一个航点移动（如果已定义）。
下图展示了您可能预期的路径类型。

![acc-rad](../../assets/flying/acceptance_radius_mission.png)

当机体进入接受半径（[NAV_ACC_RAD](../advanced_config/parameter_reference.md#NAV_ACC_RAD)）范围时，将立即切换到下一个航点。

## 任务起飞

通过在地图上添加一个 `TAKEOFF` 任务项（对应 [MAV_CMD_NAV_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_TAKEOFF) MAVLink 命令）来规划多旋翼任务起飞。

任务执行期间，机体将垂直上升至 [MIS_TAKEOFF_ALT](../advanced_config/parameter_reference.md#MIS_TAKEOFF_ALT) 参数定义的最小起飞高度，然后前往任务项中定义的三维位置。

如果启动的任务中没有包含起飞任务项，机体将先上升至最小起飞高度，然后前往第一个 `航点` 任务项。

如果机体在任务启动时已处于飞行状态，则起飞任务项将被视为普通航点。

## 另请参见

- [任务](../flying/missions.md)
  - [包裹投递任务](../flying/package_delivery_mission.md)
- [任务模式（FW）](../flight_modes_fw/mission.md)