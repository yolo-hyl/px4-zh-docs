# 返回模式 (VTOL)

<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_Return_ 飞行模式用于通过无障碍路径将机体飞往安全位置，到达后可悬停/盘旋等待或着陆。

VTOL机体默认使用 [Mission Landing/Rally Point](../flight_modes/return.md#mission-landing-rally-point-return-type-rtl-type-1) 返回类型。
在此返回类型中，机体将首先升至障碍物以上最小安全高度（如需），然后直线飞往最近的集结点或任务着陆点起点（若两者均未定义则返回起始点）。
若目标是任务着陆路线，机体将沿该路线着陆。
若目标是集结点或起始点，机体将飞回起始点并着陆。

机体将使用触发返回模式时的飞行模式（MC或FW）进行返航。
通常会遵循对应机体类型的标准返回模式行为，但在着陆前会强制切换至MC模式（如需）。

VTOL支持其他 [PX4返回类型](../flight_modes/return.md#return-types-rtl-type)，包括起始点/集结点返回、任务路径和最近安全目的地。
建议使用默认类型。

::: info

- 该模式为自动模式 - 控制机体无需用户干预。
- 该模式需要三维全局位置估计（来自GPS或通过[局部位置](../ros/external_position_estimation.md#enabling-auto-modes-with-a-local-position)推算）。
  - 飞行器在没有全局位置时无法切换到此模式。
  - 飞行器在失去位置估计时将触发故障保护。
- 该模式要求已设置起始点。
- 该模式会阻止武装（切换到此模式时机体必须已武装）。
- 任何机体均可通过遥控器模式切换开关更改飞行模式。
- 遥控器摇杆输入将被忽略。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

## 技术总结

VTOL机体默认使用[Mission Landing/Rally Point](../flight_modes/return.md#mission-landing-rally-point-return-type-rtl-type-1)返回类型，当触发返回模式时，会使用当前飞行模式（MC或FW）进行返回。

### 固定翼模式（FW）返回

若以固定翼模式返回，机体执行以下操作：

- 上升至由[RTL_RETURN_ALT](#RTL_RETURN_ALT)定义的安全最低返回高度（高于预期障碍物）。
  若初始高度高于最低返回高度则保持初始高度。
  <!-- Note that return altitude cannot be configured using the "cone" parameter in fixed-wing vehicles. -->
- 沿着固定高度的直线路径飞向目标点，目标点为：最近的_任务降落航线_起点、任意集会点，或在未定义任务降落航线和集会点时的家点。
- 如果目标点是任务降落航线，机体将遵循该航线完成降落。

  VTOL机体的任务降落航线由[MAV_CMD_DO_LAND_START](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_LAND_START)、一个或多个位置航点和[MAV_CMD_NAV_VTOL_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_LAND)组成。

- 如果目标点是集会点或家点，机体将执行以下操作：

  - 盘旋/螺旋下降至[RTL_DESCEND_ALT](#RTL_DESCEND_ALT)。
  - 根据[RTL_LAND_DELAY](#RTL_LAND_DELAY)设定短时间盘旋。
  - 航向调整至目标点（盘旋中心）。
  - 切换至MC模式并降落。

    注意[NAV_FORCE_VT](../advanced_config/parameter_reference.md#NAV_FORCE_VT)参数将被忽略：机体始终以多旋翼模式在这些目标点降落。

## 多旋翼模式（MC）返回

如果以多旋翼模式返回：

- 行为方式相同，但机体以多旋翼模式飞行并遵循多旋翼设置。
- 特别是，当在集合点或起始点着陆时，机体使用 [RTL_CONE_ANG](#RTL_CONE_ANG) 而不是仅仅使用 [RTL_RETURN_ALT](#RTL_RETURN_ALT) 来定义最低安全返回高度。
  更多信息请参见 [Return mode (Generic Vehicle) > Minimum Return Altitude](../flight_modes/return.md#minimum-return-altitude) 中对"cone"的解释。

## 参数

RTL 参数详见 [参数参考 > 返回模式](../advanced_config/parameter_reference.md#return-mode)。
若使用任务降落，仅需关注 [RTL_RETURN_ALT](#RTL_RETURN_ALT) 和 [RTL_DESCEND_ALT](#RTL_DESCEND_ALT)。
若目标为集合点或家点位置，其他参数则相关。

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="RTL_TYPE"></a>[RTL_TYPE](../advanced_config/parameter_reference.md#RTL_TYPE)                   | 返回类型。                                                                                                                                                                                                                                                                                                                                                |
| <a id="RTL_RETURN_ALT"></a>[RTL_RETURN_ALT](../advanced_config/parameter_reference.md#RTL_RETURN_ALT) | 返回高度（单位：米，缺省值：60米）。若当前高度已高于该值，机体将在当前高度返回。                                                                                                                                                                                                                                                                           |
| <a id="RTL_CONE_ANG"></a>[RTL_CONE_ANG](../advanced_config/parameter_reference.md#RTL_CONE_ANG)       | 定义机体RTL返回高度的圆锥半角。可选值（单位：度）：0、25、45、65、80、90。注意：0表示“无圆锥”（始终返回至`RTL_RETURN_ALT`或更高），90表示机体必须返回至当前高度或`RTL_DESCEND_ALT`（取较高者）。                                                                                                                                                      |
| <a id="RTL_DESCEND_ALT"></a>[RTL_DESCEND_ALT](../advanced_config/parameter_reference.md#RTL_DESCEND_ALT) | 最小返回高度及从更高返回高度开始减速或停止下降的高度（缺省值：30米）                                                                                                                                                                                                                                                                                       |
| <a id="RTL_LAND_DELAY"></a>[RTL_LAND_DELAY](../advanced_config/parameter_reference.md#RTL_LAND_DELAY) | 在`RTL_DESCEND_ALT`悬停时间（缺省值：0.5秒）- 默认设置较短以便机体减速后立即降落。若设为-1，系统将在`RTL_DESCEND_ALT`盘旋而非降落。该延迟用于配置起落架展开时间（自动触发）。                                                                                                                                                                                   |
| <a id="RTL_LOITER_RAD"></a>[RTL_LOITER_RAD](../advanced_config/parameter_reference.md#RTL_LOITER_RAD) | [固定翼专用] 盘旋圆半径（在[RTL_LAND_DELAY](#RTL_LAND_DELAY)）                                                                                                                                                                                                                                                                                           |
| <a id="MIS_TKO_LAND_REQ"></a>[MIS_TKO_LAND_REQ](../advanced_config/parameter_reference.md#MIS_TKO_LAND_REQ) | 指定任务降落或起飞模式是否为_必须_。通常固定翼设置为需要降落模式，但垂直起降(VTOL)则不需要。                                                                                                                                                                                                                                                             |

## 参见

- [返回模式（通用）](../flight_modes/return.md)
- [返回模式（多旋翼）](../flight_modes_mc/return.md)
- [返回模式（固定翼）](../flight_modes_fw/return.md)