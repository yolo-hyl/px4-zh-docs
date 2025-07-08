# 模拟故障保护

[Failsafes](../config/safety.md) 定义了安全使用 PX4 的限制/条件，以及触发故障保护时将执行的操作（例如着陆、保持位置或返回指定点）。

在 SITL 中，默认禁用部分故障保护以简化模拟器的使用。本节说明如何在真实世界尝试前，通过 SITL 模拟测试安全性关键行为。

::: info
您也可以通过 [HITL 模拟](../simulation/hitl.md) 测试故障保护。HITL 使用飞行控制器的正常配置参数。
:::

## 数据链路丢失

_Data Link Loss_ 故障保护（MAVLink 外部数据不可用）默认启用。这使得模拟器只能通过连接的地面站、SDK 或其他 MAVLink 应用程序运行。

设置参数 [NAV_DLL_ACT](../advanced_config/parameter_reference.md#NAV_DLL_ACT) 为所需的故障保护动作以修改行为。例如，设为 `0` 可禁用该功能。

::: info
在 SITL 中执行 `make clean` 时，包括此参数在内的所有参数都会重置。
:::

## 遥控器链路丢失

_RC Link Loss_ 故障保护（遥控器数据不可用）默认启用。这使得模拟器只能通过活动的 MAVLink 连接或遥控器连接运行。

设置参数 [NAV_RCL_ACT](../advanced_config/parameter_reference.md#NAV_RCL_ACT) 为所需的故障保护动作以修改行为。例如，设为 `0` 可禁用该功能。

::: info
在 SITL 中执行 `make clean` 时，包括此参数在内的所有参数都会重置。
:::

## 低电量

模拟电池被设计为永远不会耗尽，且默认仅放电至容量的 50%，因此报告的电压也会降低。这使得可以在不触发可能中断其他测试的低电量反应的情况下，测试地面站 UI 中的电池指示。

要修改此最小电池百分比值，请使用参数 [SIM_BAT_MIN_PCT](../advanced_config/parameter_reference.md#SIM_BAT_MIN_PCT)。

要控制电池放电至最小值的速度，请使用参数 [SIM_BAT_DRAIN](../advanced_config/parameter_reference.md#SIM_BAT_DRAIN)。

:::tip
在飞行中修改 [SIM_BAT_MIN_PCT](../advanced_config/parameter_reference.md#SIM_BAT_MIN_PCT)，还可以测试恢复容量以模拟电池状态估计不准确或空中充电技术。
:::

通过 [SIM_BAT_ENABLE](../advanced_config/parameter_reference.md#SIM_BAT_ENABLE) 也可以禁用模拟电池，例如通过 MAVLink 提供外部电池模拟。

## 传感器/系统故障

[故障注入](../debug/failure_injection.md) 可用于模拟许多传感器和系统中的不同故障类型。例如，可以模拟 GPS 信号缺失或间歇性丢失、遥控信号停止或卡在特定值、避障系统故障等。

例如，模拟 GPS 故障：

1. 启用参数 [SYS_FAILURE_EN](../advanced_config/parameter_reference.md#SYS_FAILURE_EN)。
1. 在 SITL 实例的 _pxh shell_ 中输入以下命令：

   ```sh
   # 关闭（所有）GPS
   failure gps off

   # 开启（所有）GPS
   failure gps ok
   ```

参见 [系统故障注入](../debug/failure_injection.md) 获取支持的目标传感器和故障模式列表。