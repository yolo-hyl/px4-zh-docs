# 起飞模式（多旋翼）

<img src="../../assets/site/position_fixed.svg" title="需要定位固定（例如GPS）" width="30px" />

_起飞_飞行模式会引导机体升至指定高度并等待进一步输入。

::: info

- 该模式为自动模式 - 控制机体无需用户干预。
- 该模式至少需要有效的局部位置估算（无需全局位置）。
  - 未配备有效局部位置的飞行器无法切换至此模式。
  - 位置估算丢失时飞行器将触发故障保护。
  - 未上锁的飞行器可切换至该模式但无法上锁。
- 可通过遥控器模式切换开关更改飞行模式。
- 遥控器操纵杆移动[默认](#COM_RC_OVERRIDE)会将机体切换至[位置模式](../flight_modes_mc/position.md)（电池故障保护例外）。
- [故障检测器](../config/safety.md#failure-detector)会在起飞阶段出现问题时自动关闭引擎。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 技术概要

多旋翼垂直上升至[MIS_TAKEOFF_ALT](../advanced_config/parameter_reference.md#MIS_TAKEOFF_ALT)定义的高度并保持位置。

遥控器操纵杆移动将[默认](#COM_RC_OVERRIDE)将机体切换至[位置模式](../flight_modes_mc/position.md)。

### 参数

起飞模式受以下参数影响：

| 参数                                                                                                     | 描述                                                                                                                                                                                                                                                  |
| -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| <a id="MIS_TAKEOFF_ALT"></a>[MIS_TAKEOFF_ALT](../advanced_config/parameter_reference.md#MIS_TAKEOFF_ALT) | 起飞时的目标高度（默认：2.5米）                                                                                                                                                                                                               |
| <a id="MPC_TKO_SPEED"></a>[MPC_TKO_SPEED](../advanced_config/parameter_reference.md#MPC_TKO_SPEED)       | 上升速度（默认：1.5米/秒）                                                                                                                                                                                                                            |
| <a id="COM_RC_OVERRIDE"></a>[COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE) | 控制多旋翼（或VTOL在MC模式）的操纵杆移动是否切换至[位置模式](../flight_modes_mc/position.md)。可单独针对自动模式和离线模式启用，默认在自动模式中启用。 |
| <a id="COM_RC_STICK_OV"></a>[COM_RC_STICK_OV](../advanced_config/parameter_reference.md#COM_RC_STICK_OV) | 操纵杆移动量触发切换至[位置模式](../flight_modes_mc/position.md)的阈值（若[COM_RC_OVERRIDE](#COM_RC_OVERRIDE)已启用）                                                                                                 |

## 参见

- [抛投起飞（MC）](../flight_modes_mc/throw_launch.md)
- [起飞模式（FW）](../flight_modes_fw/takeoff.md)

<!-- this maps to AUTO_TAKEOFF in dev -->