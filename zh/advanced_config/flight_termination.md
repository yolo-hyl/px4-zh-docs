# 飞行终止配置

_飞行终止_ 是一种 [安全措施](../config/safety.md#failsafe-actions)，可能由 [安全检查](../config/safety.md)（例如在任何机体类型或飞行模式下的遥控器丢失、地理围栏违规等）触发，也可能由 [故障检测器](../config/safety.md#failure-detector) 触发。

::: info
飞行终止也可以通过地面站或伴飞计算机使用 MAVLink [MAV_CMD_DO_FLIGHTTERMINATION](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_FLIGHTTERMINATION) 命令触发。
例如，当调用 [MAVSDK Action 插件](https://mavsdk.mavlink.io/main/en/cpp/api_reference/classmavsdk_1_1_action.html#classmavsdk_1_1_action_1a47536c4a4bc8367ccd30a92eb09781c5) 的 `terminate()` 或 `terminate_async()` 方法时会发送此命令。
:::

当 _飞行终止_ 被激活时，PX4 会同时关闭所有控制器，并将所有 PWM 输出设置为其安全值。

根据连接的设备不同，PWM 安全输出可用于：

- 部署 [降落伞](../peripherals/parachute.md)。
- 伸出可收起的起落架。
- 将 PWM 连接的云台移动到安全方向（或收起）以保护摄像头。
- 触发气囊等充气装置。
- 触发警报。

飞行终止后无法恢复。
触发后应尽快断开电池。
需要重新启动/上电机体后才能再次使用。

:::tip
PX4 不知道连接了哪些安全设备 - 它只是向输出端应用一组预定义的 PWM 值。
:::

:::tip
终止时所有输出端都会应用安全值。
无法配置电机或特定安全设备的基于时间（或其他）的独立触发。
:::

::: info
这 _不是_ 独立的 _飞行终止系统_。
如果电源丢失或自动驾驶仪完全崩溃，安全设备将不会被触发。
:::

## 硬件配置

任何可通过更改 PWM 值触发的 _安全设备_（例如 [降落伞](../peripherals/parachute.md)）均可使用，可连接到任意空闲的 PWM 端口（包括 MAIN 和 AUX）。

::: info
如果使用 Pixhawk 系列飞控，您需要单独为舵机供电（例如从 5V BEC 供电，通常也可以从电调获得）。
:::

## 软件配置

[Safety](../config/safety.md) 主题解释了如何将 _飞行终止_ 设置为针对特定安全检查执行的 [安全措施](../config/safety.md#failsafe-actions)。

[故障检测器](../config/safety.md#failure-detector) 也可以（可选）配置为在机体翻滚（超过一定姿态）或外部自动触发系统（ATS）检测到故障时触发飞行终止：

- 通过设置 [CBRK_FLIGHTTERM=0](../advanced_config/parameter_reference.md#CBRK_FLIGHTTERM) 在飞行中启用故障检测器。
- [Safety > Failure Detector > Attitude Trigger](../config/safety.md#attitude-trigger) 解释了如何配置触发 _飞行终止_ 的姿态限制。
  ::: info
  在 _起飞_ 阶段，过大的姿态会触发 _锁定_（关闭电机但不释放降落伞）而非飞行终止。
  此功能始终启用，无论 `CBRK_FLIGHTTERM` 的值如何。
  :::
- [Safety > External Automatic Trigger System (ATS)](../config/safety.md#external-automatic-trigger-system-ats) 解释了如何配置外部触发系统。

对于每个连接安全设备的 MAIN 输出，其中 "n" 为 PWM 端口号，设置：

- [PWM_MAIN_DISn](../advanced_config/parameter_reference.md#PWM_MAIN_DIS1) 为设备的 "关闭" PWM 值。
- [PWM_MAIN_FAILn](../advanced_config/parameter_reference.md#PWM_MAIN_FAIL1) 为设备的 "开启" PWM 值。

对于每个连接安全设备的 AUX 输出，其中 "n" 为 PWM 端口号，设置：

- [PWM_AUX_DIS1](../advanced_config/parameter_reference.md#PWM_AUX_DIS1) 为设备的 "关闭" PWM 值。
- [PWM_AUX_FAILn](../advanced_config/parameter_reference.md#PWM_AUX_FAIL1) 为设备的 "开启" PWM 值。

最后，为任何电机设置 `PWM_AUX_FAILn` 和 `PWM_MAIN_FAILn` PWM 值。

## 逻辑图

下图展示了飞行终止的逻辑流程。

![Logic diagram](../../assets/config/flight_termination_logic_diagram.png)