# 轨道飞行（多旋翼）

<img src="../../assets/site/difficulty_easy.png" title="Easy to fly" width="30px" />&nbsp;<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_轨道_ 指令飞行模式允许你命令多旋翼（或垂直起降飞行器在多旋翼模式下）在特定位置沿圆形轨迹飞行，通过[默认](https://mavlink.io/en/messages/common.html#ORBIT_YAW_BEHAVIOUR)偏航使机体始终朝向圆心。

::: info

- 该模式为自动模式，无需用户干预控制机体。
- 模式需要至少有效的本地位置估计（不需要全球位置）。
  - 无有效本地位置的飞行器无法切换至该模式。
  - 飞行器丢失位置估计时将触发失败保护。
- 模式会阻止解锁（切换至该模式时需机体已解锁）。
- 模式要求风速和飞行时间在允许范围内（通过参数指定）。
- 该模式目前仅支持多旋翼（或垂直起降飞行器在多旋翼模式下）。
- 遥控器操作可控制上升/下降、轨道速度和方向。
- 可通过 [MAV_CMD_DO_ORBIT](https://mavlink.io/en/messages/common.html#MMAV_CMD_DO_ORBIT) MAVLink 命令触发该模式。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 概览

![轨道模式 - 多旋翼](../../assets/flying/orbit.jpg)

_QGroundControl_（或其他兼容的地面站或MAVLink API）_必须_用于启用该模式，并设置轨道的中心位置、初始半径和高度。启用后，机体将尽可能快速飞向指令圆周轨迹的最近点，并以1m/s的慢速顺时针绕行，朝向圆心。

如何启动轨道飞行的说明请参见：[FlyView > 轨道位置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/fly_view/fly_view.html#orbit)（_QGroundControl_ 指南）。

::: info
使用遥控器控制是 _可选的_。
若未连接遥控器，轨道飞行将按上述描述进行。
遥控器无法用于启动该模式（若通过遥控器切换至该模式，机体将保持静止）。
:::

遥控器控制可用于调整轨道高度、半径、速度和方向：

- **左侧摇杆：**
  - _上下：_ 控制升降速，与[位置模式](../flight_modes_mc/position.md)相同。摇杆处于死区时高度锁定。
  - _左右：_ 无效果。
- **右侧摇杆：**
  - _左右：_ 控制顺时针/逆时针方向的加速度。摇杆居中时当前速度锁定。
    - 最大速度为10m/s，并进一步限制以保持向心加速度低于2m/s²。
  - _上下：_ 控制轨道半径（缩小/增大）。摇杆居中时当前半径锁定。
    - 最小半径为1m。最大半径为100m。

下图展示了模式行为（针对[模式2遥控器](../getting_started/rc_transmitter_receiver.md#transmitter_modes)）。

![轨道模式 - 多旋翼](../../assets/flight_modes/orbit_mc.png)

可通过切换至其他飞行模式（使用遥控器或QGC）停止该模式。

## 参数/限制

该模式受以下参数影响：

| 参数                                                                                                   | 描述                                                         |
| ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| <a id="MC_ORBIT_RAD_MAX"></a>[MC_ORBIT_RAD_MAX](../advanced_config/parameter_reference.md#MC_ORBIT_RAD_MAX) | 轨道最大半径。默认：1000m。                            |
| <a id="MC_ORBIT_YAW_MOD"></a>[MC_ORBIT_YAW_MOD](../advanced_config/parameter_reference.md#MC_ORBIT_YAW_MOD) | 轨道飞行期间的偏航行为。默认：朝向圆心。 |

以下限制为硬编码：

- 初始/默认旋转速度为1m/s顺时针方向。
- 最大加速度限制为2m/s²，优先保持指令圆周轨迹而非地面速度（即当加速度超过2m/s²时，机体将减速以保持正确圆周）。

## MAVLink 消息（开发人员）

轨道模式使用以下MAVLink命令：

- [MAV_CMD_DO_ORBIT](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_ORBIT) - 指定中心点、半径、方向、高度、速度和[偏航方向](https://mavlink.io/en/messages/common.html#ORBIT_YAW_BEHAVIOUR)启动轨道飞行（机体默认朝向轨道中心）。
- [ORBIT_EXECUTION_STATUS](https://mavlink.io/en/messages/common.html#ORBIT_EXECUTION_STATUS) - 轨道执行期间发送的状态消息，用于向地面站更新当前轨道参数（这些参数可能通过遥控器控制器修改）。