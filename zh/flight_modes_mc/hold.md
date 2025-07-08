# Hold 模式（多旋翼）

<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_Hold_ 飞行模式会使机体停止并悬停在当前的GPS位置和高度。

:::tip
_Hold模式_ 可用于暂停任务，或在紧急情况下帮助你重新接管机体控制权。  
该模式通常通过预设的开关激活。
:::

::: info

- 模式为自动 - 无需用户干预即可控制机体。
- 模式需要全局3D位置估算（来自GPS或通过[本地位置](../ros/external_position_estimation.md#enabling-auto-modes-with-a-local-position)推导）。
  - 飞行中的机体无法切换到此模式（无全局位置时）。
  - 飞行中的机体在失去位置估算时会触发安全保护。
  - 未上锁的机体可以切换到此模式（无需有效位置估算），但无法上锁。
- 模式要求风速和飞行时间在允许范围内（通过参数指定）。
- 无线电控制开关可用于在任何机体上切换飞行模式。
- 无线电摇杆移动将[默认](#COM_RC_OVERRIDE)将机体切换到[Position模式](../flight_modes_mc/position.md)，除非处理关键电池安全保护。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 技术摘要

机体悬停在当前位置和高度。  
如果模式激活时的高度低于[NAV_MIN_LTR_ALT](#NAV_MIN_LTR_ALT)，机体将首先上升到该高度。

无线电摇杆移动将[默认](#COM_RC_OVERRIDE)将机体切换到[Position模式](../flight_modes_mc/position.md)。

### 参数

Hold模式行为可通过以下参数进行配置。

| 参数                                                                                               | 描述                                                                                                                                                                                                                                                       |
| --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="NAV_MIN_LTR_ALT"></a>[NAV_MIN_LTR_ALT](../advanced_config/parameter_reference.md#NAV_MIN_LTR_ALT) | 这是系统在Hold模式下切换到此模式时（例如通过RC开关）始终遵守的高于Home点的最小高度。                                                                                                                                                                     |
| <a id="COM_RC_OVERRIDE"></a>[COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE) | 控制多旋翼（或VTOL在MC模式下）的摇杆移动是否导致模式切换到[Position模式](../flight_modes_mc/position.md)。此功能可分别针对自动模式和外部模式启用，并在自动模式下默认启用。                                                                                   |
| <a id="COM_RC_STICK_OV"></a>[COM_RC_STICK_OV](../advanced_config/parameter_reference.md#COM_RC_STICK_OV) | 摇杆移动导致切换到[Position模式](../flight_modes_mc/position.md)的量（如果[COM_RC_OVERRIDE](#COM_RC_OVERRIDE)已启用）。                                                                                                                                  |

<!-- Code for this here: https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/loiter.cpp#L61 -->

## 参见

[Hold 模式（FW）](../flight_modes_fw/hold.md)