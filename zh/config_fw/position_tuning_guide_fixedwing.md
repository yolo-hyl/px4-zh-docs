# 固定翼高度/位置控制器调参

本指南提供飞行任务和高度/位置控制模式下所需固定翼控制器的调参帮助。PX4 使用 TECS 进行高度和空速控制，使用 NPFG 进行水平航向/位置控制。

::: info
调参过程中如果增益设置不当，可能导致高度或航向控制不稳定。
因此，调参 TECS 增益的飞手应具备在稳定控制模式下操控飞机起降的能力。
:::

:::tip
所有参数的说明请参考 [参数参考文档](../advanced_config/parameter_reference.md#fw-tecs)。
本指南主要覆盖最重要的参数。
:::

## TECS 调参（高度与空速）

TECS (Total Energy Control System) 是固定翼飞机的引导算法，通过协调油门和俯仰角设定值来控制飞机的高度和空速。关于 TECS 算法的详细描述和控制图示，请参见[控制器图示](../flight_stack/controller_diagrams.md)。

在调参 TECS 之前需要确保姿态控制器调校良好：[PID 调参指南](../config_fw/pid_tuning_guide_fixedwing.md)。

TECS 调参主要涉及正确设置机体限制。这些限制可以通过参数指定，这些参数可通过一系列飞行机动确定，具体如下。大部分机动需要飞手在[稳定飞行模式](../flight_modes_fw/stabilized.md)下操作飞机。

:::tip
建议在飞手进行机动飞行时，有专人负责读取和记录遥测数据。为提高精度，建议将飞行中获得的数据与飞行日志记录的数据进行核对。
:::

#### 1. 修正条件

在[稳定模式](../flight_modes_fw/stabilized.md)下飞行，找到油门和俯仰角在修正空速下的修正值。使用油门调整空速，使用俯仰角保持水平飞行。

设置以下参数：

- [FW_AIRSPD_TRIM](../advanced_config/parameter_reference.md#FW_AIRSPD_TRIM) - 设置为机动飞行中达到的期望修正空速。
- [FW_THR_TRIM](../advanced_config/parameter_reference.md#FW_THR_TRIM) - 设置为维持修正空速所需的油门值。
- [FW_PSP_OFF](../advanced_config/parameter_reference.md#FW_PSP_OFF) - 设置为维持水平飞行所需的俯仰角。

#### 2. 空速与油门限制

在[稳定模式](../flight_modes_fw/stabilized.md)下飞行，保持水平飞行时增加油门 - 直到机体达到最大允许空速。

设置以下参数：

- [FW_THR_MAX](../advanced_config/parameter_reference.md#FW_THR_MAX) - 设置为在水平飞行中达到最大空速时的油门值。
- [FW_THR_MIN](../advanced_config/parameter_reference.md#FW_THR_MIN) - 设置为飞机应运行的最低油门值。
- [FW_AIRSPD_MAX](../advanced_config/parameter_reference.md#FW_AIRSPD_MAX) - 设置为在 `FW_THR_MAX` 油门下水平飞行时达到的最大空速。

#### 3. 俯仰角与爬升率限制

:::warning
不要使用 [FW_T_CLMB_MAX](../advanced_config/parameter_reference.md#FW_T_CLMB_MAX)、[FW_T_SINK_MAX](../advanced_config/parameter_reference.md#FW_T_SINK_MAX) 或 [FW_T_SINK_MIN](../advanced_config/parameter_reference.md#FW_T_SINK_MIN) 来指定期望的爬升或下降性能！
这些参数定义的是操作限制，应在调参阶段按以下方法设置。
:::

在稳定模式下飞行，施加全油门 (`FW_THR_MAX`) 并缓慢增加机体俯仰角，直到空速达到 `FW_AIRSPD_TRIM`。

- [FW_P_LIM_MAX](../advanced_config/parameter_reference.md#FW_P_LIM_MAX) - 设置为在 `FW_THR_MAX` 油门下以修正空速爬升所需的俯仰角。
- [FW_T_CLMB_MAX](../advanced_config/parameter_reference.md#FW_T_CLMB_MAX) - 设置为在 `FW_AIRSPD_TRIM` 空速下爬升时达到的爬升率。

在稳定模式下飞行，将油门降低至 `FW_THR_MIN` 并缓慢减小俯仰角，直到机体达到 `FW_AIRSPD_MAX`。

- [FW_P_LIM_MIN](../advanced_config/parameter_reference.md#FW_P_LIM_MIN) - 设置为在 `FW_THR_MIN` 油门下达到 `FW_AIRSPD_MAX` 所需的俯仰角。
- [FW_T_SINK_MAX](../advanced_config/parameter_reference.md#FW_T_SINK_MAX) - 设置为下降时达到的下降率。

在稳定模式下飞行，将油门降低至 `FW_THR_MIN` 并调整俯仰角以使机体保持 `FW_AIRSPD_TRIM`。

- [FW_T_SINK_MIN](../advanced_config/parameter_reference.md#FW_T_SINK_MIN) - 设置为在维持 `FW_AIRSPD_TRIM` 时的下降率。

通过调整 [FW_T_CLMB_R_SP](../advanced_config/parameter_reference.md#FW_T_CLMB_R_SP) 和 [FW_T_SINK_R_SP](../advanced_config/parameter_reference.md#FW_T_SINK_R_SP) 可指定自主任务的爬升和下降目标率。这些参数定义了机体在改变高度时的爬升/下降速率。此外，这两个值定义了用户在[高度模式](../flight_modes_fw/altitude.md)和[位置模式](../flight_modes_fw/position.md)中指定的高度变化率限制。

### FW 路径控制调参（位置）

所有路径控制参数请参考[此处](../advanced_config/parameter_reference.md#fw-path-control)。

- [NPFG_PERIOD](../advanced_config/parameter_reference.md#NPFG_PERIOD) - 这个参数以前称为 L1 距离，定义了飞机跟随的前向跟踪点距离。
  对于大多数飞机，10-20 米的值是合适的。
  在调参过程中缓慢缩短该值，直到响应灵敏且无振荡。
  滚转动态较慢的机体应增加此值。