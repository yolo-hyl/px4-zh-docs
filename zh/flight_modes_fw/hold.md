# 保持模式 (固定翼)

<img src="../../assets/site/position_fixed.svg" title="位置固定所需（例如GPS）" width="30px" />

_保持_ 飞行模式使机体围绕当前GPS位置盘旋（画圈）并保持当前高度。

:::tip
_保持模式_ 可用于暂停任务或在紧急情况下帮助您重新控制机体。  
通常通过预设的开关激活该模式。
:::

::: info

- 模式为自动 - 无需用户干预来控制机体。
- 模式需要全局3D位置估算（来自GPS或通过[本地位置](../ros/external_position_estimation.md#enabling-auto-modes-with-a-local-position)推算）。
  - 飞行中的机体无法在无全局位置时切换到此模式。
  - 飞行中的机体若丢失位置估算将触发失效保护。
  - 未武装的机体可在无有效位置估算时切换到此模式，但无法武装。
- 模式要求风速和飞行时间在允许范围内（通过参数指定）。
- 无线电控制开关可用于任何机体的飞行模式切换。
- 无线电摇杆输入将被忽略。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 技术摘要

机体在当前高度围绕GPS保持位置盘旋。  
若模式在低于此高度时激活，机体将首先上升至 [NAV_MIN_LTR_ALT](#NAV_MIN_LTR_ALT)。

无线电摇杆输入将被忽略。

### 参数

可通过以下参数配置保持模式的行为。

| 参数                                                                                                 | 描述                                                                                                   |
| ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| [NAV_LOITER_RAD](../advanced_config/parameter_reference.md#NAV_LOITER_RAD)                            | 盘旋圆的半径。                                                                                         |
| <a id="NAV_MIN_LTR_ALT"></a>[NAV_MIN_LTR_ALT](../advanced_config/parameter_reference.md#NAV_MIN_LTR_ALT) | 盘旋模式的最小高度（若模式在更低高度激活，机体将上升至该高度）。                                       |

## 参见

[保持模式 (多旋翼)](../flight_modes_mc/hold.md)

<!-- this maps to AUTO_LOITER in flight mode state machine -->