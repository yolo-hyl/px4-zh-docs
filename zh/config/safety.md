# 安全（故障安全）配置

PX4 提供了多种安全功能，用于在出现问题时保护和恢复机体：

- _故障安全_ 允许您定义安全飞行的区域和条件，并指定在触发故障安全时执行的[操作](#failsafe-actions)（例如降落、保持位置或返回指定点）。  
  最重要的故障安全设置在 _QGroundControl_ 的 [安全设置](#qgroundcontrol-safety-setup) 页面中配置。  
  其他设置必须通过 [参数](../advanced_config/parameters.md) 进行配置。
- 遥控器上的 [安全开关](#emergency-switches) 可用于立即停止电机或在出现问题时将机体返航。

## QGroundControl 安全设置

_QGroundControl_ 安全设置页面通过点击 _QGroundControl_ 图标、**机体设置**，然后在侧边栏中选择 **Safety** 进入。
包括许多最重要的安全设置（电池、遥控器丢失等）以及触发操作 **Return** 和 **Land** 的设置。

![Safety Setup(QGC)](../../assets/qgc/setup/safety/safety_setup.png)

## 故障安全动作

当触发故障安全机制时，默认行为（对大多数故障安全机制而言）是在执行相关故障安全动作前进入[COM_FAIL_ACT_T](../advanced_config/parameter_reference.md#COM_FAIL_ACT_T)秒的保持模式。
这为用户提供了观察当前状态并必要时覆盖故障安全机制的时间。
在大多数情况下，可以通过使用遥控器或地面站（GCS）切换模式来实现（注意：在故障保持期间移动遥控杆不会触发覆盖操作）。

下表按严重程度递增的顺序列出了所有故障安全动作。
请注意，不同类型的故障安全机制可能不支持所有动作。

| 动作                                                 | 描述                                                                                                                                                                                                                                                                                                                            |
| ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="act_none"></a>无/禁用                           | 无操作。故障安全机制将被忽略。                                                                                                                                                                                                                                                                                               |
| <a id="act_warn"></a>警告                             | 将发送警告信息（例如发送至_QGroundControl_）。                                                                                                                                                                                                                                                                             |
| <a id="act_hold"></a>保持模式                         | 机体将进入[保持模式（多旋翼）](../flight_modes_mc/hold.md)或[保持模式（固定翼）](../flight_modes_fw/hold.md)并悬停或盘旋。VTOL机体将根据当前模式（多旋翼/固定翼）保持状态。                                                                                                                |
| <a id="act_return"></a>[返回模式][return]           | 机体将进入_返回模式_。返回行为可通过[返回家中设置](#return-mode-settings)（下方）进行配置。                                                                                                                                                                                                        |
| <a id="act_land"></a>降落模式                         | 机体将进入[降落模式（多旋翼）](../flight_modes_mc/land.md)或[降落模式（固定翼）](../flight_modes_fw/land.md)并降落。VTOL将首先转换为多旋翼模式。                                                                                                                                                                |
| <a id="act_disarm"></a>解除武装                     | 立即停止电机运转。                                                                                                                                                                                                                                                                                                          |
| <a id="act_term"></a>[飞行终止][flight_term]         | 关闭所有控制器并将所有PWM输出设置为故障安全值（例如[PWM_MAIN_FAILn][pwm_main_failn]，[PWM_AUX_FAILn][pwm_main_failn]）。故障安全输出可用于释放降落伞、起落架或其他操作。对于固定翼机体，这可能允许滑翔到安全区域。 |

[flight_term]: ../advanced_config/flight_termination.md
[return]: ../flight_modes/return.md
[pwm_main_failn]: ../advanced_config/parameter_reference.md#PWM_MAIN_FAIL1
[pwm_aux_failn]: ../advanced_config/parameter_reference.md#PWM_AUX_FAIL1

若触发多个故障安全机制，将执行更严重的动作。
例如，若同时丢失遥控和GPS信号，且手动控制丢失设置为[返回模式](#act_return)、地面站（GCS）链路丢失设置为[降落](#act_land)，则会执行降落操作。

:::tip
可通过[Failsafe State Machine Simulation](safety_simulation.md)模拟不同故障安全机制触发时的具体行为。
:::

### 返回模式设置

<!-- 建议用摘要和链接替换此部分 - 返回模式较为复杂 -->

_Return_ 是一种常见的[故障安全动作](#failsafe-actions)，用于激活[返回模式](../flight_modes/return.md)使机体返回家点。
每种机体的默认设置通常适用，但固定翼通常需要定义一个任务降落程序。

::: tip
若要修改配置，请务必阅读您机体类型的[返回模式](../flight_modes/return.md)文档以了解可用选项。
:::

QGC允许用户设置返回模式和降落行为的某些方面，例如返回飞行的海拔高度和部署起落架时的盘旋时间。

![安全 - 返回家中设置 (QGC)](../../assets/qgc/setup/safety/safety_return_home.png)

### 降落模式设置

_在当前位置降落_ 是一种常见的[故障安全动作](#failsafe-actions)（尤其适用于多旋翼），用于激活[降落模式](../flight_modes_mc/land.md)。
每种机体的默认设置通常适用。

::: tip
若要修改配置，请务必阅读您机体类型的[降落模式](../flight_modes_fw/land.md)文档以了解可用选项。
:::

QGC允许用户设置降落行为的某些方面，例如降落后的解除武装时间（仅限多旋翼）和下降速率。

![安全 - 降落模式设置 (QGC)](../../assets/qgc/setup/safety/safety_land_mode.png)

## 电池断电保护

### 电池电量断电保护

当电池容量降至电池断电保护阈值以下时，低电量断电保护将被触发。您可以在QGroundControl中配置每个层级的阈值和对应的断电保护动作。

![Safety - Battery (QGC)](../../assets/qgc/setup/safety/safety_battery.png)

最常见的配置是将值和动作设置为上述方式（`警告 > 断电保护 > 紧急`），并将[Failsafe Action](#COM_LOW_BAT_ACT)设置为：在"警告层级"发出警告，在"断电保护层级"触发返航模式，在"紧急层级"立即降落。

相关设置和底层参数如下：

| 设置                                             | 参数                                                                    | 描述                                                                           |
| ------------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| <a id="COM_LOW_BAT_ACT"></a>断电保护动作         | [COM_LOW_BAT_ACT](../advanced_config/parameter_reference.md#COM_LOW_BAT_ACT) | 当容量低于触发层级时，基于警告、返航或降落进行操作。             |
| <a id="BAT_LOW_THR"></a>电池警告层级          | [BAT_LOW_THR](../advanced_config/parameter_reference.md#BAT_LOW_THR)         | 警告的百分比容量（或其他动作）。                                  |
| <a id="BAT_CRIT_THR"></a>电池断电保护层级     | [BAT_CRIT_THR](../advanced_config/parameter_reference.md#BAT_CRIT_THR)       | 触发返航动作的百分比容量（若选择单一动作则为其他动作）。 |
| <a id="BAT_EMERGEN_THR"></a>电池紧急层级 | [BAT_EMERGEN_THR](../advanced_config/parameter_reference.md#BAT_EMERGEN_THR) | 触发立即降落动作的百分比容量。                         |

### 飞行时间断电保护

可通过参数配置多个与电池相关的其他断电保护机制：

- **安全返航剩余飞行时间断电保护** ([COM_FLTT_LOW_ACT](#COM_FLTT_LOW_ACT))：当PX4估计机体仅剩足够电量完成返航模式降落时触发。您可配置为忽略断电保护、发出警告或直接进入返航模式。
- **最大飞行时间断电保护** ([COM_FLT_TIME_MAX](#COM_FLT_TIME_MAX))：允许设置起飞后的最大飞行时间，达到该时间后机体将自动进入返航模式（在90%时间时也会发出警告）。这相当于对电池总飞行时间的“硬编码”估算。该功能默认禁用。
- **武装最低电池参数** ([COM_ARM_BAT_MIN](#COM_ARM_BAT_MIN))：若电池电量低于指定值，则禁止武装。

相关设置和底层参数如下：

| 设置                                                              | 参数                                                                      | 描述                                                                                                                     |
| -------------------------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| <a id="COM_FLTT_LOW_ACT"></a>安全返航低飞行时间动作 | [COM_FLTT_LOW_ACT](../advanced_config/parameter_reference.md#COM_FLTT_LOW_ACT) | 当返航模式仅能勉强达到安全区域时的响应动作。`0`: 无响应，`1`: 警告，`3`: 返航模式（默认）。 |
| <a id="COM_FLT_TIME_MAX"></a>最大飞行时间断电保护层级     | [COM_FLT_TIME_MAX](../advanced_config/parameter_reference.md#COM_FLT_TIME_MAX) | 触发返航模式前允许的最大飞行时间（秒）。`-1`: 禁用（默认）。                           |## 手动控制丢失的紧急保护

当与[遥控器](../getting_started/rc_transmitter_receiver.md)或[操纵杆](../config/joystick.md)的连接丢失且没有备用控制源时，可能会触发手动控制丢失的紧急保护机制。  
若使用[遥控器](../getting_started/rc_transmitter_receiver.md)，该机制在[遥控器链路丢失](../getting_started/rc_transmitter_receiver.md#set-signal-loss-behaviour)时触发。  
若通过MAVLink数据链连接[操纵杆](../config/joystick.md)，该机制在操纵杆断开或数据链丢失时触发。

::: info
PX4和接收机可能还需要配置以_检测遥控器丢失_：[无线电设置 > 遥控器丢失检测](../config/radio.md#rc-loss-detection)。
:::

![Safety - RC Loss (QGC)](../../assets/qgc/setup/safety/safety_rc_loss.png)

QGroundControl安全界面允许设置[紧急保护动作](#failsafe-actions)和[遥控器丢失超时](#COM_RC_LOSS_T)。  
希望在特定自动模式（任务、悬停、外部控制）中禁用遥控器丢失保护的用户，可通过参数[COM_RCL_EXCEPT](#COM_RCL_EXCEPT)实现。

附加（及底层）参数设置如下：

| 参数                                                                                             | 设置                     | 描述                                                                                                                                                                                                                                                                                                                                                                                              |
| ----------------------------------------------------------------------------------------------------- | --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="COM_RC_LOSS_T"></a>[COM_RC_LOSS_T](../advanced_config/parameter_reference.md#COM_RC_LOSS_T)    | 手动控制丢失超时           | 从选定的手动控制源接收到最后一个设定点后的时间，超过该时间则视为手动控制丢失。此值需保持较短，因为机体将持续使用旧的手动控制设定点飞行，直到超时触发。                                                                                                                                              |
| <a id="COM_FAIL_ACT_T"></a>[COM_FAIL_ACT_T](../advanced_config/parameter_reference.md#COM_FAIL_ACT_T) | 紧急保护反应延迟           | 紧急保护条件触发（`COM_RC_LOSS_T`）与紧急保护动作（RTL、降落、悬停）之间的延迟时间（秒）。在此状态下，机体将在悬停模式下等待手动控制源重新连接。对于长距离飞行，此值可设置较长以避免间歇性连接丢失立即触发紧急保护。该值可设为零以立即触发紧急保护。 |
| <a id="NAV_RCL_ACT"></a>[NAV_RCL_ACT](../advanced_config/parameter_reference.md#NAV_RCL_ACT)          | 紧急保护动作               | 禁用、盘旋、返航、降落、解除武装、终止。                                                                                                                                                                                                                                                                                                                                                       |
| <a id="COM_RCL_EXCEPT"></a>[COM_RCL_EXCEPT](../advanced_config/parameter_reference.md#COM_RCL_EXCEPT) | 遥控器丢失例外情况          | 设置忽略手动控制丢失的模式：任务、悬停、外部控制。                                                                                                                                                                                                                                                                                                                          |## 数据链路丢失故障保护

当遥测链路（与地面站的连接）丢失时，数据链路丢失故障保护将被触发。

![安全 - 数据链路丢失 (QGC)](../../assets/qgc/setup/safety/safety_data_link_loss.png)

以下设置和底层参数显示如下。

| 设置                | 参数                                                                | 描述                                                                       |
| ---------------------- | ------------------------------------------------------------------------ | --------------------------------------------------------------------------------- |
| 数据链路丢失超时 | [COM_DL_LOSS_T](../advanced_config/parameter_reference.md#COM_DL_LOSS_T) | 在数据连接丢失后，故障保护触发前的时间间隔。 |
| 故障保护动作        | [NAV_DLL_ACT](../advanced_config/parameter_reference.md#NAV_DLL_ACT)     | 禁用、悬停模式、返航模式、降落模式、解除武装、终止。                   |

以下设置也适用，但在QGC UI中未显示。

| 设置                                                     | 参数                                                                  | 描述                                          |
| ----------------------------------------------------------- | -------------------------------------------------------------------------- | ---------------------------------------------------- |
| <a id="COM_DLL_EXCEPT"></a>DLL故障保护的模式例外 | [COM_DLL_EXCEPT](../advanced_config/parameter_reference.md#COM_DLL_EXCEPT) | 设置数据链路丢失时不会触发故障保护的模式。 |## 地理围栏安全机制

当无人机突破了“虚拟”边界时，将触发 _地理围栏安全机制_。  
在最简单的形式中，边界被设置为以起降点为中心的圆柱体。如果机体移动超出半径或高于指定的 _安全机制动作_ 时，将触发响应动作。

![Safety - Geofence (QGC)](../../assets/qgc/setup/safety/safety_geofence.png)

:::tip
PX4 支持更复杂的地理围栏几何形状，包括多个任意多边形和圆形的包含及排除区域：[飞行 > 地理围栏](../flying/geofence.md)。
:::

设置和底层 [地理围栏参数](../advanced_config/parameter_reference.md#geofence) 如下所示。

| 设置          | 参数                                                                    | 描述                                                     |
| ---------------- | ---------------------------------------------------------------------------- | --------------------------------------------------------------- |
| 突破时的动作 | [GF_ACTION](../advanced_config/parameter_reference.md#GF_ACTION)             | 无、警告、悬停模式、返航模式、终止、降落。         |
| 最大半径       | [GF_MAX_HOR_DIST](../advanced_config/parameter_reference.md#GF_MAX_HOR_DIST) | 地理围栏圆柱的水平半径。若设为0则禁用地理围栏。 |
| 最大高度     | [GF_MAX_VER_DIST](../advanced_config/parameter_reference.md#GF_MAX_VER_DIST) | 地理围栏圆柱的高度。若设为0则禁用地理围栏。            |

::: info
将 `GF_ACTION` 设置为终止会在违反围栏时立即关闭机体。  
由于此功能存在固有危险性，因此通过 [CBRK_FLIGHTTERM](#CBRK_FLIGHTTERM) 禁用（需将其重置为0才能真正关闭系统）。
:::

以下设置也适用，但不会在 QGC UI 中显示。

| 设置                                                            | 参数                                                                    | 描述                                                                                                                                         |
| ------------------------------------------------------------------ | ---------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="GF_SOURCE"></a>地理围栏来源                              | [GF_SOURCE](../advanced_config/parameter_reference.md#GF_SOURCE)             | 设置位置来源是估计的全局位置还是直接来自GPS设备。                                                             |
| <a id="GF_PREDICT"></a>预触发地理围栏                             | [GF_PREDICT](../advanced_config/parameter_reference.md#GF_PREDICT)           | （实验性）如果机体当前运动趋势预测会触发突破，则提前触发地理围栏（而非在突破后触发）。 |
| <a id="CBRK_FLIGHTTERM"></a>飞行终止电路保护器                  | [CBRK_FLIGHTTERM](../advanced_config/parameter_reference.md#CBRK_FLIGHTTERM) | 启用/禁用飞行终止动作（默认禁用）。                                                                                   |## 位置（GNSS）丢失故障安全

当机体处于需要有效位置估算的模式下时，若PX4的位置估算质量低于可接受水平（可能由GNSS信号丢失引起），则会触发**位置丢失故障安全**机制。下文将分别介绍触发条件和控制器的故障安全响应动作。

### 位置丢失故障安全触发机制

PX4中触发位置故障安全机制主要有两种方式：
- 自上次融合直接速度或水平位置测量传感器数据以来的时间超时。此类传感器包括：GNSS、光流传感器、空速传感器、VIO（视觉惯性里程计）以及辅助全球定位传感器。
- 估算的水平位置精度超过设定阈值。该检查仅适用于悬停系统（旋翼飞行器或VTOL在悬停阶段）。

下表展示了相关参数：

| 参数                                                                                                 | 描述                                                                                                   |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| <a id="EKF2_NOAID_TOUT"></a>[EKF2_NOAID_TOUT](../advanced_config/parameter_reference.md#EKF2_NOAID_TOUT) | 最大惯性航迹推算时间，即距离上次接收任何限制速度漂移的传感器数据样本后的时间 [微秒]。 |
| <a id="COM_POS_FS_EPH"></a>[COM_POS_FS_EPH](../advanced_config/parameter_reference.md#COM_POS_FS_EPH) | 悬停飞行器（多旋翼和VTOL在悬停阶段）的水平位置误差阈值。固定翼飞行器的该值设置为无穷大。 |

### 位置丢失故障安全响应动作

故障响应动作由[COM_POSCTL_NAVL](../advanced_config/parameter_reference.md#COM_POSCTL_NAVL)控制，具体取决于是否假定遥控器控制可用（以及高度信息）：

- `0`: 遥控器可用。
  若高度估算可用则切换至**高度模式**，否则切换至**稳定模式**。
- `1`: 遥控器不可用。
  若高度估算可用则切换至**下坠模式**，否则进入飞行终止。
  **下坠模式**是一种无需位置估算的降落模式。

固定翼飞机和未配置为悬停降落的VTOL（[NAV_FORCE_VT](../advanced_config/parameter_reference.md#NAV_FORCE_VT)未启用），会通过参数[FPGPSF_LT](../advanced_config/parameter_reference.md#FW_GPSF_LT)定义其在丢失位置后盘旋（以固定滚转角[FPGPSF_R](../advanced_config/parameter_reference.md#FW_GPSF_R)在当前高度盘旋）的持续时间，直到尝试降落。若VTOL配置为切换悬停模式降落（[NAV_FORCE_VT](../advanced_config/parameter_reference.md#NAV_FORCE_VT)启用），则会先进入悬停模式再下降。

下表展示了所有机体的关联参数：

| 参数                                                                                                 | 描述                                                                                                   |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| <a id="COM_POSCTL_NAVL"></a>[COM_POSCTL_NAVL](../advanced_config/parameter_reference.md#COM_POSCTL_NAVL) | 任务执行期间位置控制的导航丢失响应。取值：`0` - 假设使用遥控器，`1` - 假设无遥控器。 |

仅影响固定翼的参数：

| 参数                                                                                   | 描述                                                                                                 |
| --------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| <a id="FW_GPSF_LT"></a>[FW_GPSF_LT](../advanced_config/parameter_reference.md#FW_GPSF_LT) | 盘旋时间（等待GNSS恢复后进入降落或飞行终止前的持续时间）。设为0则禁用。 |
| <a id="FW_GPSF_R"></a>[FW_GPSF_R](../advanced_config/parameter_reference.md#FW_GPSF_R)    | 盘旋时的固定滚转/倾斜角。                                                                           |## 外部控制丢失故障安全

当在[外部控制](../flight_modes/offboard.md)模式下丢失外部连接时，将触发_Offboard Loss Failsafe_。  
根据是否有可用的遥控器（RC）连接，可以指定不同的故障安全行为。

相关参数如下：

| 参数名称 | 描述 |
| --- | --- |
| [COM_OF_LOSS_T](../advanced_config/parameter_reference.md#COM_OF_LOSS_T) | 在外部连接丢失后，触发故障安全前的延迟时间。 |
| [COM_OBL_RC_ACT](../advanced_config/parameter_reference.md#COM_OBL_RC_ACT) | 当有RC连接时的故障安全动作：位置模式、高度模式、手动模式、返航模式、降落模式、悬停模式。 |## 交通避让保险机制

交通避让保险机制允许PX4在任务期间响应应答器数据（例如来自[ADSB应答器](../advanced_features/traffic_avoidance_adsb.md)）。

相关参数如下所示：

| 参数                                                                    | 描述                                                      |
| ---------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| [NAV_TRAFF_AVOID](../advanced_config/parameter_reference.md#NAV_TRAFF_AVOID) | 设置保险机制动作：禁用、警告、返航模式、降落模式。 |## Quad-chute 应急措施

当 VTOL 机体无法再以固定翼模式飞行时（例如推力电机、空速传感器或控制面故障），将触发此应急措施。触发后，机体将立即切换为多旋翼模式并执行 [COM_QC_ACT](#COM_QC_ACT) 参数定义的操作。

::: info
Quad-chute 也可通过发送 MAVLINK [MAV_CMD_DO_VTOL_TRANSITION](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_VTOL_TRANSITION) 消息并设置 `param2` 为 `1` 来触发。
:::

控制 quad-chute 触发条件的参数如下表所示。

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="COM_QC_ACT"></a>[COM_QC_ACT](../advanced_config/parameter_reference.md#COM_QC_ACT)             | 切换至多旋翼飞行后的 quad-chute 操作。可设置为：[Warning](#act_warn)、[Return](#act_return)、[Land](#act_land)、[Hold](#act_hold)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| <a id="VT_FW_QC_HMAX"></a>[VT_FW_QC_HMAX](../advanced_config/parameter_reference.md#VT_FW_QC_HMAX)      | 最大 quad-chute 高度，低于此高度时无法触发 quad-chute 应急措施。该设置防止高空 quad-chute 下降导致电量耗尽（进而引发坠机）。高度相对于地面、家点或局部原点（按可用性优先级依次）。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| <a id="VT_QC_ALT_LOSS"></a>[VT_QC_ALT_LOSS](../advanced_config/parameter_reference.md#VT_QC_ALT_LOSS)   | 非指令下降 quad-chute 高度阈值。<br><br>在高度控制模式（如 [Hold 模式](../flight_modes_fw/hold.md)、[Position 模式](../flight_modes_fw/position.md)、[Altitude 模式](../flight_modes_fw/altitude.md) 或 [Mission 模式](../flight_modes_fw/mission.md)）下，机体应跟踪当前"指令"高度设定值。若机体持续低于指令设定值（超过本参数定义的阈值），将触发 quad-chute 应急措施。<br><br>注意：仅当机体连续下降超过指令设定值时才会触发 quad-chute；若指令高度设定值上升速度超过机体跟踪能力，则不会触发。 |
| <a id="VT_QC_T_ALT_LOSS"></a>[VT_QC_T_ALT_LOSS](../advanced_config/parameter_reference.md#VT_QC_T_ALT_LOSS) | VTOL 转为固定翼飞行时 quad-chute 触发的高度损失阈值。若机体在完成转换前下降超过初始高度此阈值，将触发 quad-chute。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <a id="VT_FW_MIN_ALT"></a>[VT_FW_MIN_ALT](../advanced_config/parameter_reference.md#VT_FW_MIN_ALT)      | 固定翼飞行的最低家点高度。当固定翼飞行中高度低于此值时将触发 quad-chute。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| <a id="VT_FW_QC_R"></a>[VT_FW_QC_R](../advanced_config/parameter_reference.md#VT_FW_QC_R)               | 固定翼模式下触发 quad-chute 的绝对滚转阈值。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| <a id="VT_FW_QC_P"></a>[VT_FW_QC_P](../advanced_config/parameter_reference.md#VT_FW_QC_P)               | 固定翼模式下触发 quad-chute 的绝对俯仰阈值。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |## 强风故障安全

当风速超过警告和最大风速阈值时，强风故障安全机制可触发警告和/或其他模式切换。相关参数如下表所示。

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="COM_WIND_MAX"></a>[COM_WIND_MAX](../advanced_config/parameter_reference.md#COM_WIND_MAX)       | 触发故障安全动作的风速阈值（以米每秒为单位），见[COM_WIND_MAX_ACT](#COM_WIND_MAX_ACT)。                                                                                                                                                 |
| <a id="COM_WIND_MAX_ACT"></a>[COM_WIND_MAX_ACT](../advanced_config/parameter_reference.md#COM_WIND_MAX_ACT) | 强风故障安全动作（由[COM_WIND_MAX](#COM_WIND_MAX)触发）。可设置为：`0`：无（默认），`1`：[警告](#act_warn)，`2`：[悬停](#act_hold)，`3`：[返航](#act_return)，`4`：[终止](#act_term)，`5`：[降落](#act_land)。 |
| <a id="COM_WIND_WARN"></a>[COM_WIND_WARN](../advanced_config/parameter_reference.md#COM_WIND_WARN)    | 触发周期性故障安全警告的风速阈值。                                                                                                                                                                                                           |## 故障检测器

故障检测器允许机体在意外翻转时，或被外部故障检测系统通知时采取保护性措施。

在**飞行**过程中，如果满足故障条件，故障检测器可用于触发[飞行终止](../advanced_config/flight_termination.md)，这可能会启动[降落伞](../peripherals/parachute.md)或执行其他操作。

::: info
飞行过程中的故障检测默认是禁用的（通过设置参数 [CBRK_FLIGHTTERM=0](#CBRK_FLIGHTTERM) 可启用）。
:::

在**起飞**阶段，如果机体翻转，故障检测器的[姿态触发器](#attitude-trigger)将触发[解除武装动作](#act_disarm)（解除武装会关闭电机，但与飞行终止不同，不会启动降落伞或执行其他故障动作）。  
请注意，此检查在起飞时始终启用，与 `CBRK_FLIGHTTERM` 参数无关。

故障检测器在所有机体类型和模式中均处于活动状态，除非机体预期会进行翻转（例如 [Acro mode (MC)](../flight_modes_mc/altitude.md)、[Acro mode (FW)](../flight_modes_fw/altitude.md) 和 [Manual (FW)](../flight_modes_fw/manual.md)）。

### 姿态触发器

故障检测器可配置为在机体姿态超过预设的俯仰和翻滚值并持续指定时间后触发。

相关参数如下：

| 参数                                                                                                 | 描述                                                                                                                             |
| ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| <a id="CBRK_FLIGHTTERM"></a>[CBRK_FLIGHTTERM](../advanced_config/parameter_reference.md#CBRK_FLIGHTTERM) | 飞行终止电路断路器。从默认值 121212 取消设置以启用由于故障检测器或 FMU 损失导致的飞行终止。                                       |
| <a id="FD_FAIL_P"></a>[FD_FAIL_P](../advanced_config/parameter_reference.md#FD_FAIL_P)                 | 允许的最大俯仰角（以度为单位）。                                                                                                 |
| <a id="FD_FAIL_R"></a>[FD_FAIL_R](../advanced_config/parameter_reference.md#FD_FAIL_R)                 | 允许的最大翻滚角（以度为单位）。                                                                                                 |
| <a id="FD_FAIL_P_TTRI"></a>[FD_FAIL_P_TTRI](../advanced_config/parameter_reference.md#FD_FAIL_P_TTRI)  | 超过 [FD_FAIL_P](#FD_FAIL_P) 的时间以触发故障检测（默认 0.3 秒）。                                                               |
| <a id="FD_FAIL_R_TTRI"></a>[FD_FAIL_R_TTRI](../advanced_config/parameter_reference.md#FD_FAIL_R_TTRI)  | 超过 [FD_FAIL_R](#FD_FAIL_R) 的时间以触发故障检测（默认 0.3 秒）。                                                               |

### 外部自动触发系统 (ATS)

如果[启用](#CBRK_FLIGHTTERM)，[故障检测器](#failure-detector)还可以由外部 ATS 系统触发。  
外部触发系统必须连接到飞行控制器的 AUX5 端口（或在没有 AUX 端口的板上使用 MAIN5 端口），并通过以下参数进行配置。

::: info
外部 ATS 符合 [ASTM F3322-18](https://webstore.ansi.org/Standards/ASTM/ASTMF332218) 要求。  
一个 ATS 设备的例子是 [FruityChutes Sentinel 自动触发系统](https://fruitychutes.com/uav_rpv_drone_recovery_parachutes/sentinel-automatic-trigger-system.htm)。
:::

| 参数                                                                                                 | 描述                                                                                                                                           |
| ----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="FD_EXT_ATS_EN"></a>[FD_EXT_ATS_EN](../advanced_config/parameter_reference.md#FD_EXT_ATS_EN)    | 在 AUX5 或 MAIN5（取决于板型）上启用 PWM 输入以通过外部自动触发系统（ATS）触发故障保护。默认：禁用。                                           |
| <a id="FD_EXT_ATS_TRIG"></a>[FD_EXT_ATS_TRIG](../advanced_config/parameter_reference.md#FD_EXT_ATS_TRIG) | 从外部自动触发系统触发故障保护的 PWM 阈值。默认：1900 毫秒。                                                                                   |## 任务可行性检查

在任务开始前会执行多项检查以确保任务是可执行的。
例如，检查确保第一个航点不会过远，并且任务飞行路径不会与任何地理围栏冲突。

由于这些检查并非严格意义上的"故障安全"机制，因此它们被记录在[Mission Mode (FW) > Mission Feasibility Checks](../flight_modes_fw/mission.md#mission-feasibility-checks)和[Mission Mode (MC) > Mission Feasibility Checks](../flight_modes_mc/mission.md#mission-feasibility-checks)中。

## 紧急开关

远程控制开关可被配置（作为_QGroundControl_ [飞行模式设置](../config/flight_mode.md)的一部分），以便在出现问题或紧急情况下快速采取纠正措施；例如，停止所有电机，或激活[返回模式](#return-switch)。

本节列出可用的紧急开关。

### Kill开关

Kill开关会立即停止所有电机输出——如果正在飞行，机体将开始坠落！

[默认情况下](#COM_KILL_DISARM) 如果在5秒内将开关复位，电机将重新启动，之后机体将自动断开，需要重新上锁才能启动电机。

| 参数                                                                                                   | 描述                                                                     |
| ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| <a id="COM_KILL_DISARM"></a>[COM_KILL_DISARM](../advanced_config/parameter_reference.md#COM_KILL_DISARM) | 在触发Kill开关后断开的超时值。默认值：`5`秒。                              |

::: info
还有一个[Kill手势](#kill-gesture)，其无法被复位。
:::

### 上锁/断开开关

上锁/断开开关是默认摇杆上锁/断开机制的_直接替代方案_（并具有相同功能：确保电机启动/停止前有明确的操作步骤）。  
可能优先使用该开关的原因包括：

- 个人偏好开关而非摇杆动作。
- 避免因特定摇杆动作意外触发上锁/断开。
- 无延迟（立即响应）。

上锁/断开开关会立即断开（停止）电机，针对支持飞行中断开的[飞行模式](../flight_modes/index.md#flight-modes)。  
这包括：

- _手动模式_
- _特技模式_
- _稳定模式_

对于不支持飞行中断开的模式，飞行中该开关将被忽略，但可在检测到着陆后使用。  
这包括_位置模式_和自主模式（例如_任务_、_降落_等）。

::: info
[自动断开超时](#auto-disarming-timeouts)（例如通过[COM_DISARM_LAND](#COM_DISARM_LAND)）与上锁/断开开关独立——即即使开关处于上锁状态，超时机制仍会生效。
:::

<!--
**Note** This can also be done by [manually setting](../advanced_config/parameters.md) the [RC_MAP_ARM_SW](../advanced_config/parameter_reference.md#RC_MAP_ARM_SW) parameter to the corresponding switch RC channel.
  If the switch positions are reversed, change the sign of the parameter [RC_ARMSWITCH_TH](../advanced_config/parameter_reference.md#RC_ARMSWITCH_TH) (or also change its value to alter the threshold value).
-->

### 返回开关

返回开关可用于立即激活[返回模式](../flight_modes/return.md)。

## 紧急停止手势

紧急停止手势会立即停止所有电机输出——如果正在飞行，机体将开始下坠！

该操作只能通过重启才能恢复（这与[Kill Switch](#kill-switch)不同，后者可以在[COM_KILL_DISARM](#COM_KILL_DISARM)定义的时限内恢复操作）。

| 参数                                                                                                    | 描述                                                                                                  |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| <a id="MAN_KILL_GEST_T"></a>[MAN_KILL_GEST_T](../advanced_config/parameter_reference.md#MAN_KILL_GEST_T) | 摇杆保持在手势位置时触发电机停止的持续时间。默认值：`-1`秒（禁用）。 |## 上电/断电设置

[指挥模块](../advanced_config/parameter_reference.md#commander)包含多个以`COM_ARM`为前缀的参数，用于配置机体是否可以上电以及在何种条件下上电（注意部分以`COM_ARM`为前缀的参数用于其他系统的上电）。以`COM_DISARM_`为前缀的参数则会影响断电行为。

### 自动断电超时设置

可设置超时时间在起飞过慢或着陆后自动断电（断电将切断电机供电，螺旋桨将停止旋转）。

相关参数如下：

| 参数                                                                                                   | 描述                                                |
| ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| <a id="COM_DISARM_LAND"></a>[COM_DISARM_LAND](../advanced_config/parameter_reference.md#COM_DISARM_LAND)    | 着陆后自动断电的超时时间。                            |
| <a id="COM_DISARM_PRFLT"></a>[COM_DISARM_PRFLT](../advanced_config/parameter_reference.md#COM_DISARM_PRFLT) | 机体起飞过慢时自动断电的超时时间。                    |

### 上电前置条件

这些参数可用于设置禁止上电的条件。

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="COM_ARMABLE"></a>[COM_ARMABLE](../advanced_config/parameter_reference.md#COM_ARMABLE)                | 启用上电功能。`0`: 禁用，`1`: 启用（默认）。                                                                                                                             |
| <a id="COM_ARM_BAT_MIN"></a>[COM_ARM_BAT_MIN](../advanced_config/parameter_reference.md#COM_ARM_BAT_MIN)    | 上电所需的最低电池电量。`0`: 禁用（默认）。取值范围：`0`-`0.9`                                                                                                          |
| <a id="COM_ARM_WO_GPS"></a>[COM_ARM_WO_GPS](../advanced_config/parameter_reference.md#COM_ARM_WO_GPS)       | 无GPS时启用上电。`0`: 禁用，`1`: 启用（默认）。                                                                                                                          |
| <a id="COM_ARM_MIS_REQ"></a>[COM_ARM_MIS_REQ](../advanced_config/parameter_reference.md#COM_ARM_MIS_REQ)    | 要求有效任务才能上电。`0`: 禁用（默认），`1`: 启用。                                                                                                                    |
| <a id="COM_ARM_SDCARD"></a>[COM_ARM_SDCARD](../advanced_config/parameter_reference.md#COM_ARM_SDCARD)       | 要求SD卡才能上电。`0`: 禁用（默认），`1`: 警告，`2`: 启用。                                                                                                              |
| <a id="COM_ARM_AUTH_REQ"></a>[COM_ARM_AUTH_REQ](../advanced_config/parameter_reference.md#COM_ARM_AUTH_REQ) | 需要外部（MAVLink）系统的上电授权。允许上电的标志位。`1`: 启用，`0`: 禁用（默认）。相关配置参数以`COM_ARM_AUTH_`为前缀。                                                  |
| <a id="COM_ARM_ODID"></a>[COM_ARM_ODID](../advanced_config/parameter_reference.md#COM_ARM_ODID)             | 要求健康的远程ID系统才能上电。`0`: 禁用（默认），`1`: 警告，`2`: 启用。                                                                                                   |

此外还有一些参数配置系统和传感器限制，若超出限制将禁止上电：[COM_CPU_MAX](../advanced_config/parameter_reference.md#COM_CPU_MAX)，[COM_ARM_IMU_ACC](../advanced_config/parameter_reference.md#COM_ARM_IMU_ACC)，[COM_ARM_IMU_GYR](../advanced_config/parameter_reference.md#COM_ARM_IMU_GYR)，[COM_ARM_MAG_ANG](../advanced_config/parameter_reference.md#COM_ARM_MAG_ANG)，[COM_ARM_MAG_STR](../advanced_config/parameter_reference.md#COM_ARM_MAG_STR)。

## 进一步信息

- [QGroundControl 用户指南 > 安全设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/safety.html)