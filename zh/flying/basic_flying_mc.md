# 基础飞行（多旋翼）

本主题介绍了如何在手动或自动飞行辅助模式下（对于自主飞行，请参见[任务](../flying/missions.md)），使用[遥控器](../getting_started/rc_transmitter_receiver.md)或[操纵杆](../config/joystick.md)控制机体的基础操作。

::: info
首次飞行前，请务必阅读我们的[首飞指南](../flying/first_flight_guidelines.md)。
:::

## 启动机体

在飞行前，机体必须被[启动](../getting_started/px4_basic_concepts.md#arming-and-disarming)。这将为所有电机和执行器供电。除非您正在执行[投掷起飞](../flight_modes_mc/throw_launch.md)，否则螺旋桨也会开始旋转。

启动步骤如下：

- 首先解除[安全开关](../getting_started/px4_basic_concepts.md#safety-switch)（如果存在）。
- 使用您的启动命令：
  - 将油门杆置于右下角（使用[启动手势](../advanced_config/prearm_arm_disarm.md#arm_disarm_gestures)）。
  - 或配置[启动/关闭开关](../config/safety.md#arm-disarm-switch)。
  - 也可以通过_QGroundControl_启动（PX4无需遥控器即可自主飞行）。

:::tip
机体在[校准/配置](../config/index.md)完成并获得定位锁定前不会启动。[机体状态通知](../getting_started/vehicle_status.md)（包括机载LED、音频通知和_QGroundControl_更新）会提示机体是否准备好飞行（并帮助您排查未就绪的原因）。
:::

::: info
如果启动后未能及时起飞，机体将（默认）自动[关闭电机](../advanced_config/prearm_arm_disarm.md#auto-disarming)（[COM_DISARM_PRFLT](../advanced_config/parameter_reference.md#COM_DISARM_PRFLT)参数控制）。这是为了确保在未使用时机体能返回安全状态的安全措施。
:::

<!--
VTOL机体默认只能在多旋翼模式下启动（可通过[CBRK_VTOLARMING](../advanced_config/parameter_reference.md#CBRK_VTOLARMING)启用固定翼模式启动）。
-->

## 起飞

多旋翼飞行员可通过启用手动模式、启动机体并提升油门杆至电机产生足够推力离开地面来手动起飞。在[定位模式](../flight_modes_mc/position.md)或[高度模式](../flight_modes_mc/altitude.md)中，需将油门杆提升至62.5%以上以命令爬升率并使机体离地。超过此值后所有控制器将启用，机体进入悬停所需的油门水平（[MPC_THR_HOVER](../advanced_config/parameter_reference.md#MPC_THR_HOVER)）。

[投掷起飞](../flight_modes_mc/throw_launch.md)也受支持，当机体检测到抛掷到顶点后会激活电机，并根据当前模式运行。

或可通过切换至自动[起飞模式](../flight_modes_mc/takeoff.md)执行起飞。

::: info
如果启动后未能及时起飞，机体可能关闭电机（通过调整[COM_DISARM_PRFLT](../advanced_config/parameter_reference.md#COM_DISARM_PRFLT)参数设置超时时间）。
:::

::: info
[故障检测器](../config/safety.md#failure-detector)在起飞出现问题时会自动停止引擎。
:::

## 降落

多旋翼可在任何手动模式下降落。确保触地后持续下拉油门杆直至电机关闭。

注意，默认情况下机体在降落时会自动关闭：

- 使用[COM_DISARM_LAND](../advanced_config/parameter_reference.md#COM_DISARM_LAND)设置降落后的自动关闭时间（或完全禁用）。
- 手动关闭可通过将油门杆置于左下角。

也可通过启用[降落模式](../flight_modes_mc/land.md)或[返回模式](../flight_modes_mc/return.md)实现自主降落。

::: info
如果在降落时看到机体“抽搐”（电机先停止再立即重启），这可能是由于[降落检测器配置](../advanced_config/land_detector.md)不佳（特别是[MPC_THR_HOVER](../advanced_config/parameter_reference.md#MPC_THR_HOVER)参数设置不当）。
:::

## 飞行控制/指令

机体运动通过四个基本指令控制：横滚、偏航、俯仰和油门。随着油门增加，旋翼转速加快，机体上升；当机体前倾时，部分推力向前；侧倾时部分推力向侧方；改变偏航使机体绕轴旋转。

这些指令分别允许您左/右移动、左/右旋转、前/后移动以及上/下移动，如下面的[模式2](../getting_started/rc_transmitter_receiver.md#remote-control-units-for-aircraft)遥控器所示。

![RC基础指令](../../assets/flying/rc_mode2_mc_position_mode.png)

![多旋翼基础移动](../../assets/flying/basic_movements_multicopter.png)

## 飞行辅助

即使了解了机体的控制方式，全手动模式飞行仍可能难以掌握。新用户应[配置遥控器](../config/flight_mode.md)使用飞行模式，由自动驾驶仪自动补偿用户输入异常或环境因素。

以下三种模式强烈建议新用户使用：

- [定位模式](../flight_modes_mc/position.md) - 松开杆后机体将停止（并抵抗风力漂移）
- [高度模式](../flight_modes_mc/altitude.md) - 爬升和下降受最大速率限制
- [稳定模式](../flight_modes_mc/manual_stabilized.md) - 难以翻转，松开杆后会自动水平（但不保持位置）

::: info
您也可以在_QGroundControl_主飞行界面启用自动模式。
:::

## 参见

- [地形跟随/保持与距离辅助](../flying/terrain_following_holding.md) — 如何启用地形跟随
- [任务](../flying/missions.md)