# 基础飞行（固定翼）

本主题介绍如何使用[遥控器](../getting_started/rc_transmitter_receiver.md)在手动或自动驾驶辅助飞行模式下操作机体的基本方法（关于自主飞行请参见：[任务](../flying/missions.md)）。

::: info
首次飞行前请务必阅读我们的[首次飞行指南](../flying/first_flight_guidelines.md)。
:::

## 上电机体

飞行前必须先[上电](../getting_started/px4_basic_concepts.md#arming-and-disarming)机体。这将为所有电机和执行器供电，并可能启动螺旋桨。

上电步骤：

- 首先关闭[安全开关](../getting_started/px4_basic_concepts.md#safety-switch)（如果存在）。
- 使用对应上电指令：
  - 将油门杆置于右下角（使用[上电手势](../advanced_config/prearm_arm_disarm.md#arm_disarm_gestures)）。
  - 或配置[上/断电开关](../config/safety.md#arm-disarm-switch)。
  - 也可通过_QGroundControl_ 上电（PX4自主飞行无需遥控器）。

:::tip
机体将在完成[校准/配置](../config/index.md)并获取定位锁后才能上电。[机体状态通知](../getting_started/vehicle_status.md)（包括机体LED、音频提示和_QGroundControl_更新）会告知机体是否已准备好飞行（并帮助排查未准备好时的原因）。
:::

::: info
机体默认情况下若起飞延迟过长将自动[断电](../advanced_config/prearm_arm_disarm.md#auto-disarming)（关闭电机）！
这是为确保不使用时机体能返回安全状态的安全机制（通过[COM_DISARM_PRFLT](../advanced_config/parameter_reference.md#COM_DISARM_PRFLT)参数控制）。
:::

<!--
VTOL机体默认仅可在多旋翼模式上电（可通过[CBRK_VTOLARMING](../advanced_config/parameter_reference.md#CBRK_VTOLARMING)参数启用固定翼模式上电）。
-->

## 起飞

::: info
手动起飞（和降落）并不容易！
我们建议使用自动模式，特别是对于新手飞行员。
:::

推荐使用[稳定模式](../flight_modes_fw/stabilized.md)、[特技模式](../flight_modes_fw/acro.md)或[手动模式](../flight_modes_fw/manual.md)进行手动起飞。
也可使用[位置模式](../flight_modes_fw/position.md)和[高度模式](../flight_modes_fw/altitude.md)，但起飞前需充分加速机体——手抛时需要强推力，跑道起飞则需要较长的加速阶段（这是因为这些模式的控制器会优先空速而非高度跟踪）。

手抛飞机的手动起飞：

- 逐渐增加电机推力并水平抛出机体。
- 避免过快抬头，这可能导致飞机失速。
- 起飞前良好的配平至关重要，因为若机体无法水平飞行，飞行员将仅有极短的反应时间避免坠毁！

跑道起飞的手动操作：

- 在跑道上加速至足够起飞速度。
- 若飞机配备转向轮，使用偏航杆保持航向。
- 达到足够速度后通过俯仰杆拉起机头。

自动起飞可在[任务模式](../flight_modes_fw/mission.md#mission-takeoff)或[起飞模式（FW）](../flight_modes_fw/takeoff.md)中实现。
起飞过程中或之后的任何时刻，飞行员都可通过切换至手动飞行模式接管控制。

## 降落

[稳定模式](../flight_modes_fw/stabilized.md)、[特技模式](../flight_modes_fw/acro.md)或[手动模式](../flight_modes_fw/manual.md)是降落推荐模式（与起飞相同）。
在这些模式下飞行员可完全控制电机推力，这在接近地面时执行手动拉平操作（抬头而不增加油门）是必需的。
应迎风降落以降低接地前的地速。

自动降落应使用[固定翼任务降落](../flight_modes_fw/mission.md#mission-landing)。
该降落方式在任务中定义，可在[任务](../flight_modes_fw/mission.md)或[返航](../flight_modes_fw/return.md)模式中使用。

不推荐使用自动[降落模式](../flight_modes_fw/land.md)除非绝对必要，因为其无法考虑地形因素。

<!-- 添加此部分使内容更通用：后续会拆分 -->

注意机体默认在降落时自动断电：

- 使用[COM_DISARM_LAND](../advanced_config/parameter_reference.md#COM_DISARM_LAND)设置降落后的自动断电时间（或完全禁用）。
- 通过将油门杆置于左下角手动断电。

## 飞行控制/指令

所有飞行操作（包括起飞和降落）都通过4个基本指令控制：滚转、偏航、俯仰和油门。

![遥控基本指令](../../assets/flying/rc_basic_commands.png)

要控制飞机，需要理解基本的滚转、俯仰、偏航和油门指令如何影响三维空间中的运动。
前飞飞机（固定翼、VTOL前飞）对运动指令的响应如下所示：

![前飞基本动作](../../assets/flying/basic_movements_forward.png)

- 俯仰 => 上升/下降。
- 滚转 => 左右倾斜和转弯。
- 偏航 => 尾部左右旋转和转弯。
- 油门 => 前进速度变化。

::: info
飞机的最佳转弯称为协调转弯，通过同时使用滚转和少量偏航实现。
这个操作需要经验！
:::

## 辅助飞行

即使理解了机体的控制方式，全手动飞行仍然非常容易出错。
新用户应[配置遥控器](../config/flight_mode.md)使用飞行模式，让飞控自动补偿异常输入或环境因素。

以下三种模式强烈推荐新用户使用：

- [稳定模式](../flight_modes_fw/stabilized.md) - 机体难以翻转，松开操纵杆会自动平飞（但不保持位置）
- [高度模式](../flight_modes_fw/altitude.md) - 上升和下降速率受到限制
- [位置模式](../flight_modes_fw/position.md) - 松开操纵杆后机体停止（并抵抗风力漂移保持位置）

::: info
您也可以通过_QGroundControl_主飞行界面切换自动模式。
:::