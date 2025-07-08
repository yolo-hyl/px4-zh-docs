# 上电、断电、预上电配置

机体可能包含运动部件，其中部分在通电时具有潜在危险性（尤其是电机和螺旋桨）！

为降低事故风险，PX4为机体组件的通电状态提供了显式控制：

- **断电(Disarmed)：** 电机和执行器均无供电。
- **预上电(Pre-armed)：** 电机/螺旋桨被锁定，但非危险电子设备的执行器已通电（例如副翼、襟翼等）。
- **上电(Armed)：** 机体完全通电。电机/螺旋桨可能正在转动（具有危险性）！

::: info
地面站可能将预上电状态显示为"断电"。
虽然这种显示在技术上不准确，但属于"安全"状态。
:::

用户可通过以下方式控制状态的转换：
[安全开关](../getting_started/px4_basic_concepts.md#safety-switch)（机体可选配置）_和_ [上电开关/按钮](#arm_disarm_switch)，[上电手势](#arm_disarm_gestures)，或地面控制器的_MAVLink command_：

- _安全开关_ 是机体上的控制装置，必须在上电前启用，且可能阻止预上电（取决于配置）。
  通常安全开关集成在GPS模块中，但也可能是独立的物理组件。

  :::warning
  上电的机体具有潜在危险性。
  安全开关是防止意外上电的额外保护机制。
  :::

- _上电开关_ 是遥控器上的开关/按钮，用于上电机体并启动电机（前提是未被安全开关阻止）。
- _上电手势_ 是遥控器上的摇杆动作，可作为上电开关的替代方案。
- 地面控制站也可通过MAVLink命令发送上电/断电信号。

当机体在上电后一定时间内未起飞，且着陆后未手动断电时，PX4将自动断电。
这减少了机体处于危险上电状态的地面时间。

PX4允许通过参数配置预上电、上电和断电行为（可通过_QGroundControl_的[参数编辑器](../advanced_config/parameters.md)修改），具体如下文所述。

:::tip
上电/断电参数可在[参数参考 > 指挥器](../advanced_config/parameter_reference.md#commander)中找到（搜索 `COM_ARM_*` 和 `COM_DISARM_*`）。
:::

## 上锁/解锁手势 {#arm_disarm_gestures}

默认情况下，机体的上锁/解锁通过将遥控器油门/偏航杆移动到特定极限位置并保持1秒实现。

- **上锁:** 油门最小，偏航最大
- **解锁:** 油门最小，偏航最小

遥控器根据其模式使用不同的杆控制油门和偏航 [参考](../getting_started/rc_transmitter_receiver.md#types-of-remote-controllers)，因此对应不同手势：

- **模式2**:
  - _上锁:_ 左杆移至右下角。
  - _解锁:_ 左杆移至左下角。
- **模式1**:
  - _上锁:_ 左杆向右，右杆移至底部。
  - _解锁:_ 左杆向左，右杆移至底部。

所需保持时间可通过 [COM_RC_ARM_HYST](#COM_RC_ARM_HYST) 配置。
注意默认情况下 ([COM_DISARM_MAN](#COM_DISARM_MAN)) 你也可以通过手势/按钮在飞行中解锁：可以选择禁用此功能以避免意外解锁。

| 参数                                                                                                 | 描述                                                                                                        |
| ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| <a id="MAN_ARM_GESTURE"></a>[MAN_ARM_GESTURE](../advanced_config/parameter_reference.md#MAN_ARM_GESTURE) | 启用上锁/解锁杆手势。`0`: 禁用，`1`: 启用（默认）。                                                           |
| <a id="COM_DISARM_MAN"></a>[COM_DISARM_MAN](../advanced_config/parameter_reference.md#COM_DISARM_MAN)    | 在MC手动推力模式下，通过开关/杆/按钮启用飞行中解锁。`0`: 禁用，`1`: 启用（默认）。                            |
| <a id="COM_RC_ARM_HYST"></a>[COM_RC_ARM_HYST](../advanced_config/parameter_reference.md#COM_RC_ARM_HYST) | RC杆必须在上锁/解锁位置保持的时间（默认：`1` 秒）。                                                           |## 上锁按钮/开关 {#arm_disarm_switch}

一个 _上锁按钮_ 或 "瞬时开关" 可被配置为用以触发上锁/解锁 _替代_ [基于手势的上锁](#arm_disarm_gestures) (设置上锁开关将禁用上锁手势)。  
按钮需按住约 ([nominally](#COM_RC_ARM_HYST)) 一秒时间以实现上锁 (处于解锁状态时) 或解锁 (处于上锁状态时)。

双位置开关也可用于上锁/解锁，相应上锁/解锁指令通过开关的 _转换状态_ 触发。

:::tip
双位置上锁开关主要适用于/推荐用于竞速无人机。
:::

开关或按钮通过 [RC_MAP_ARM_SW](#RC_MAP_ARM_SW) 进行分配和启用，开关的 "类型" 通过 [COM_ARM_SWISBTN](#COM_ARM_SWISBTN) 进行配置。

| 参数                                                                                                 | 描述                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <a id="RC_MAP_ARM_SW"></a>[RC_MAP_ARM_SW](../advanced_config/parameter_reference.md#RC_MAP_ARM_SW)   | RC 上锁开关通道 (默认: 0 - 未分配)。若已定义，指定的 RC 通道 (按钮/开关) 将用于上锁，替代摇杆手势。<br>**注意:**<br>- 此设置会 _禁用摇杆手势_!<br>- 此设置适用于 RC 控制器。它不适用于通过 _QGroundControl_ 连接的 Joystick 控制器。 |
| <a id="COM_ARM_SWISBTN"></a>[COM_ARM_SWISBTN](../advanced_config/parameter_reference.md#COM_ARM_SWISBTN) | 上锁开关是一个瞬时按钮。<br>- `0`: 上锁开关是一个双位置开关，上锁/解锁指令通过开关状态转换发送。<br>- `1`: 上锁开关是一个按钮或瞬时按钮，上锁/解锁指令在按住按钮设定时间 ([COM_RC_ARM_HYST](#COM_RC_ARM_HYST)) 后发送。                                     |

::: info
开关也可通过 _QGroundControl_ 的 [飞行模式](../config/flight_mode.md) 配置进行设置。
:::

## 自动解除武装

默认情况下，机体将在着陆时自动解除武装，或在解除武装后过长时间未起飞时自动解除武装。该功能通过以下超时参数进行配置：

| 参数                                                                                                   | 描述                                                                     |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| <a id="COM_DISARM_LAND"></a>[COM_DISARM_LAND](../advanced_config/parameter_reference.md#COM_DISARM_LAND)    | 着陆后自动解除武装的超时时间。默认值：2秒（-1表示禁用）。            |
| <a id="COM_DISARM_PRFLT"></a>[COM_DISARM_PRFLT](../advanced_config/parameter_reference.md#COM_DISARM_PRFLT) | 若起飞过慢时的自动解除武装超时时间。默认值：10秒（<=0表示禁用）。 |## 启动前检查

为了减少事故，机体仅允许在满足特定条件时启动（部分条件可配置）。  
以下情况会阻止启动：

- 机体处于非"健康"状态。例如未校准或报告传感器错误。
- 机体配备有[安全开关](../getting_started/px4_basic_concepts.md#safety-switch)但未启用。
- 机体配备有[远程ID](../peripherals/remote_id.md)但状态不健康或未就绪
- 垂直起降(VTOL)机体处于固定翼模式（[默认行为](../advanced_config/parameter_reference.md#CBRK_VTOLARMING)）
- 当前模式需要可靠的全局定位估算但机体未获得GPS锁定
- 更多情况（详见[启动/关闭安全设置](../config/safety.md#arming-disarming-settings)）

当前失败的检查项可在QGroundControl(v4.2.0及更新版本)中查看[启动检查报告](../flying/pre_flight_checks.md#qgc-arming-check-report)（另见[Fly View > 启动和预飞检查](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/fly_view/fly_view.md#arm)）

注意PX4内部以10Hz频率运行启动检查。失败的检查项列表会被维护，当列表变化时PX4会通过[Events interface](../concept/events_interface.md)发出当前列表。该列表也会在GCS连接时发送。因此GCS可立即获知预启动检查状态，无论机体处于启动或未启动状态。

:::details 开发者实现说明  
客户端实现在[libevents](https://github.com/mavlink/libevents)中:  

- [libevents > Event groups](https://github.com/mavlink/libevents#event-groups)  
- [health_and_arming_checks.h](https://github.com/mavlink/libevents/blob/main/libs/cpp/parse/health_and_arming_checks.h)  

QGC实现在[HealthAndArmingCheckReport.cc](https://github.com/mavlink/qgroundcontrol/blob/master/src/MAVLink/LibEvents/HealthAndArmingCheckReport.cc)。  
:::  

PX4还通过[SYS_STATUS](https://mavlink.io/en/messages/common.html#SYS_STATUS)消息发出部分启动检查信息（详见[MAV_SYS_STATUS_SENSOR](https://mavlink.io/en/messages/common.html#MAV_SYS_STATUS_SENSOR)）。

## 上锁序列：预上锁模式 & 安全按钮

上锁序列取决于是否存在_safety switch_（安全开关），并由参数 [COM_PREARM_MODE](#COM_PREARM_MODE)（预上锁模式）和 [CBRK_IO_SAFETY](#CBRK_IO_SAFETY)（I/O 安全电路断路器）控制。

参数 [COM_PREARM_MODE](#COM_PREARM_MODE) 定义了预上锁模式的启用条件（"安全"/非节流执行器是否可以移动）：

- _禁用_：预上锁模式禁用（不存在仅允许"安全"/非节流执行器启用的阶段）。
- _安全开关_（默认）：预上锁模式由安全开关启用。
  如果没有安全开关，则预上锁模式将不会启用。
- _始终_：从电源开启时预上锁模式即启用。

如果存在安全开关，则其是上锁的先决条件。
如果没有安全开关，则必须启用 I/O 安全电路断路器（[CBRK_IO_SAFETY](#CBRK_IO_SAFETY)），且上锁仅取决于上锁指令。

以下部分详细描述了不同配置的启动序列：

### 默认配置：COM_PREARM_MODE=安全开关 & 安全开关存在

默认配置使用安全开关实现预上锁。
从预上锁状态可以进一步执行上锁以启用所有电机/执行器。
对应参数：[COM_PREARM_MODE=1](#COM_PREARM_MODE)（安全开关）和 [CBRK_IO_SAFETY=0](#CBRK_IO_SAFETY)（I/O 安全电路断路器禁用）。

默认启动序列为：

1. 上电。
   - 所有执行器锁定在上锁位置
   - 无法上锁。
1. 按下安全开关。
   - 系统进入预上锁状态：非节流执行器可以移动（例如副翼）。
   - 系统安全关闭：允许上锁。
1. 发送上锁指令。

   - 系统完成上锁。
   - 所有电机和执行器可以移动。

### COM_PREARM_MODE=禁用 & 安全开关存在

当预上锁模式为_Disabled_时，启用安全开关不会解锁"安全"执行器，但允许后续上锁。
对应参数 [COM_PREARM_MODE=0](#COM_PREARM_MODE)（禁用）和 [CBRK_IO_SAFETY=0](#CBRK_IO_SAFETY)（I/O 安全电路断路器禁用）。

启动序列为：

1. 上电。
   - 所有执行器锁定在上锁位置
   - 无法上锁。
1. 按下安全开关。
   - _所有执行器仍锁定在上锁位置（与上锁状态相同）_。
   - 系统安全关闭：允许上锁。
1. 发送上锁指令。

   - 系统完成上锁。
   - 所有电机和执行器可以移动。

### COM_PREARM_MODE=始终 & 安全开关存在

当预上锁模式为_Always_时，从电源开启即启用预上锁模式。
仍然需要安全开关才能上锁。
对应参数 [COM_PREARM_MODE=2](#COM_PREARM_MODE)（始终）和 [CBRK_IO_SAFETY=0](#CBRK_IO_SAFETY)（I/O 安全电路断路器禁用）。

启动序列为：

1. 上电。
   - 系统进入预上锁状态：非节流执行器可以移动（例如副翼）。
   - 无法上锁。
1. 按下安全开关。
   - 系统安全关闭：允许上锁。
1. 发送上锁指令。
   - 系统完成上锁。
   - 所有电机和执行器可以移动。

### COM_PREARM_MODE=安全或禁用 & 无安全开关

在没有安全开关的情况下，当 `COM_PREARM_MODE` 设置为_Safety_ 或_Disabled_时，预上锁模式将无法启用（与上锁状态相同）。
对应参数 [COM_PREARM_MODE=0 或 1](#COM_PREARM_MODE)（禁用/安全开关）和 [CBRK_IO_SAFETY=22027](#CBRK_IO_SAFETY)（I/O 安全电路断路器启用）。

启动序列为：

1. 上电。
   - 所有执行器锁定在上锁位置
   - 系统安全关闭：允许上锁。
1. 发送上锁指令。
   - 系统完成上锁。
   - 所有电机和执行器可以移动。

### COM_PREARM_MODE=始终 & 无安全开关

当预上锁模式为_Always_时，从电源开启即启用预上锁模式。
对应参数 [COM_PREARM_MODE=2](#COM_PREARM_MODE)（始终）和 [CBRK_IO_SAFETY=22027](#CBRK_IO_SAFETY)（I/O 安全电路断路器启用）。

启动序列为：

1. 上电。
   - 系统进入预上锁状态：非节流执行器可以移动（例如副翼）。
   - 系统安全关闭：允许上锁。
1. 发送上锁指令。
   - 系统完成上锁。
   - 所有电机和执行器可以移动。

### 参数

| 参数                                                                                                    | 描述                                                                                                                                                                                                                        |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="COM_PREARM_MODE"></a>[COM_PREARM_MODE](../advanced_config/parameter_reference.md#COM_PREARM_MODE) | 进入预上锁模式的条件。`0`: 禁用，`1`: 安全开关（通过安全开关启用预上锁模式；如果不存在开关则无法启用），`2`: 始终（从电源开启即启用预上锁模式）。默认值：`1`（安全按钮）。 |
| <a id="CBRK_IO_SAFETY"></a>[CBRK_IO_SAFETY](../advanced_config/parameter_reference.md#CBRK_IO_SAFETY)    | I/O 安全电路断路器。                                                                                                                                                                                                     |

<!-- Discussion:
https://github.com/PX4/PX4-Autopilot/pull/12806#discussion_r318337567
https://github.com/PX4/PX4-user_guide/issues/567#issue-486653048
-->