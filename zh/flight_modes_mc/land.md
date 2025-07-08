# 降落模式（多旋翼）

<img src="../../assets/site/position_fixed.svg" title="Position estimate required (e.g. GPS)" width="30px" />

_降落_ 飞行模式使机体在该模式被激活的位置进行降落。  
机体将在降落完成后不久自动解除武装（默认行为）。

::: info

- 该模式为自动模式 - 不需要用户干预来控制机体。
- 该模式至少需要有效的局部位置估计（不需要全球位置）。
  - 未获得有效局部位置的飞行器无法切换到此模式。
  - 飞行器在丢失位置估计时将触发紧急降落。
- 该模式会阻止武装（切换到此模式时机体必须已武装）。
- 任何机体均可使用遥控器控制开关切换飞行模式。
- 多旋翼（或VTOL在多旋翼模式下）的遥控器摇杆移动将[默认](#COM_RC_OVERRIDE)使机体切换到[位置模式](../flight_modes_mc/position.md)，除非处理电池紧急情况。
- 该模式可通过 [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND) MAVLink 命令触发，或通过明确切换到降落模式激活。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 技术摘要

机体将在该模式被激活的位置降落。  
机体以 [MPC_LAND_SPEED](#MPC_LAND_SPEED) 参数指定的速率下降，并在降落完成后自动解除武装（[默认](#COM_DISARM_LAND)行为）。

遥控器摇杆移动将使机体切换到[位置模式](../flight_modes_mc/position.md)（[默认](#COM_RC_OVERRIDE)行为）。

### 参数

可通过以下参数配置降落模式行为。

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                     |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="MPC_LAND_SPEED"></a>[MPC_LAND_SPEED](../advanced_config/parameter_reference.md#MPC_LAND_SPEED)    | 降落时的下降速率。由于地面状况未知，应将此值保持较低水平。                                                                                                                                                                               |
| <a id="COM_DISARM_LAND"></a>[COM_DISARM_LAND](../advanced_config/parameter_reference.md#COM_DISARM_LAND) | 降落后的自动解除武装超时时间（秒）。若设置为 -1，则机体在降落时不会解除武装。                                                                                                                                                           |
| <a id="COM_RC_OVERRIDE"></a>[COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE) | 控制多旋翼（或VTOL在MC模式下）的遥控器摇杆移动是否导致模式切换到[位置模式](../flight_modes_mc/position.md)。此设置可分别针对自动模式和外部模式启用，默认在自动模式中启用。 |
| <a id="COM_RC_STICK_OV"></a>[COM_RC_STICK_OV](../advanced_config/parameter_reference.md#COM_RC_STICK_OV) | 遥控器摇杆移动导致切换到[位置模式](../flight_modes_mc/position.md)的阈值（如果[COM_RC_OVERRIDE](#COM_RC_OVERRIDE)已启用）。                                                                                                            |

## 参见

- [降落模式（固定翼）](../flight_modes_fw/land.md)
- [降落模式（垂直起降）](../flight_modes_vtol/land.md)