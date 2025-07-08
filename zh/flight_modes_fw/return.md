# 返回模式（固定翼）

<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_Return_ 飞行模式用于在无障碍路径上将机体安全返回，飞往安全目的地并降落。

固定翼机体默认使用 [任务降落/集结点](../flight_modes/return.md#mission-landing-rally-point-return-type-rtl-type-1) 返回类型，且要求任务中始终包含降落模式。使用此配置时，返回模式将使机体升至高于障碍物的安全高度（如需），飞行至任务中定义的降落模式起点，然后按该模式降落。

固定翼支持 [其他PX4返回类型](../flight_modes/return.md#return-types-rtl-type)，包括回家/集结点返回、任务路径和最近安全目的地。建议使用默认类型。

::: info

- 模式为自动模式 - 无需用户干预控制机体。
- 模式需要全局3D位置估计（来自GPS或通过[局部位置](../ros/external_position_estimation.md#enabling-auto-modes-with-a-local-position)推断）。
  - 机体无法切换到此模式而没有全局位置。
  - 机体在失去位置估计时会触发安全措施。
- 模式需要设置家位置。
- 模式防止武装（机体在切换到此模式时必须已武装）。
- 无线电控制切换可用于在任何机身上更改飞行模式。
- 无线电杆的移动被忽略。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 技术总结

固定翼机体默认使用 _任务降落/集结点_ 返回类型。在此返回类型下，机体执行以下操作：

- 升至由 [RTL_RETURN_ALT](#RTL_RETURN_ALT) 定义的安全最低返回高度（高于预期障碍物）。若当前高度高于最低返回高度则保持当前高度。注意固定翼无法通过"cone"参数配置返回高度。
- 通过恒定高度的直线路径飞往目的地，目的地为最近的任务降落模式起点、集结点或家位置（若未定义降落模式或集结点）。
- 若目的地为 _任务降落模式_，则按该模式降落。
- 若目的地为集结点或家位置，将下降至下降高度，然后盘旋或降落（取决于降落参数）。

固定翼应通过任务中定义的降落模式作为返回目的地，这是自动降落最安全的方式。此要求通常由 [MIS_TKO_LAND_REQ](#MIS_TKO_LAND_REQ) 参数强制执行。

任务降落模式由 [MAV_CMD_DO_LAND_START](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_LAND_START)、一个或多个位置航点和 [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND) 组成。

当目的地为集结点或家位置时，机体到达后将快速下降至 [RTL_DESCEND_ALT](#RTL_DESCEND_ALT) 定义的高度，并默认以 [RTL_LOITER_RAD](#RTL_LOITER_RAD) 半径无限盘旋。通过修改 [RTL_LAND_DELAY](#RTL_LAND_DELAY) 可强制降落（需不为-1）。此时机体以 [Land mode](../flight_modes_fw/land.md) 方式降落。

## 参数

RTL参数在[参数参考 > 返回模式](../advanced_config/parameter_reference.md#return-mode)中列出。若使用任务降落，仅需 [RTL_RETURN_ALT](#RTL_RETURN_ALT) 和 [RTL_DESCEND_ALT](#RTL_DESCEND_ALT)。若目的地为集结点或家位置则需其他参数。

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="RTL_TYPE"></a>[RTL_TYPE](../advanced_config/parameter_reference.md#RTL_TYPE)                   | 返回类型。                                                                                                                                                                                                                                                                                                                                                |
| <a id="RTL_RETURN_ALT"></a>[RTL_RETURN_ALT](../advanced_config/parameter_reference.md#RTL_RETURN_ALT) | 返回高度（单位：米，默认值：60米）。若当前高度已高于此值则按当前高度返回。                                                                                                                                                                                                                                                                                  |
| <a id="RTL_DESCEND_ALT"></a>[RTL_DESCEND_ALT](../advanced_config/parameter_reference.md#RTL_DESCEND_ALT) | 最低返回高度及从更高返回高度下降时的减速/停止高度（默认值：30米）                                                                                                                                                                                                                                                                                         |
| <a id="RTL_LAND_DELAY"></a>[RTL_LAND_DELAY](../advanced_config/parameter_reference.md#RTL_LAND_DELAY)  | 在 `RTL_DESCEND_ALT` 悬停时间（默认值：0.5秒） - 默认值较短以便机体减速后立即降落。若设为-1则系统将在 `RTL_DESCEND_ALT` 盘旋而非降落。此延迟用于配置起落架展开时间（自动触发）。                                                                                                                                                                               |
| <a id="RTL_LOITER_RAD"></a>[RTL_LOITER_RAD](../advanced_config/parameter_reference.md#RTL_LOITER_RAD)  | [固定翼仅] 盘旋半径（在 [RTL_LAND_DELAY](#RTL_LAND_DELAY) 时）                                                                                                                                                                                                                                                                                           |
| <a id="MIS_TKO_LAND_REQ"></a>[MIS_TKO_LAND_REQ](../advanced_config/parameter_reference.md#MIS_TKO_LAND_REQ) | 指定是否需要任务降落/起飞模式。固定翼通常需要此模式。                                                                                                                                                                                                                                                                                                       |

## 参见

- [返回模式（通用）](../flight_modes/return.md)
- [返回模式（多旋翼）](../flight_modes_mc/return.md)
- [返回模式（垂直起降）](../flight_modes_vtol/return.md)