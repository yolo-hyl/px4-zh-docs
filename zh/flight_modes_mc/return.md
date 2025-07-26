# Return 模式 (多旋翼)

<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_Return_ 飞行模式用于沿无障碍路径将机体飞往安全目的地，以便降落。

多旋翼默认使用 [起始点/集合点返回类型](../flight_modes/return.md#home-rally-point-return-type-rtl-type-0)  
在该返回类型中，机体如需先上升至障碍物之上的安全高度，然后直线飞往最近的安全着陆点（集合点或起始点），下降至"下降高度"，短暂停留后着陆。  
返回高度、下降高度和降落延迟通常设置为保守的"安全"值，但可根据需要修改。

多旋翼支持 [其他 PX4 返回类型](../flight_modes/return.md#return-types-rtl-type)（包括任务降落、任务路径和最近安全目的地）。  
推荐使用默认类型。

::: info

- 该模式为自动模式 - 不需要用户干预来控制机体。
- 模式需要全局三维位置估算（来自 GPS 或通过 [本地位置](../ros/external_position_estimation.md#enabling-auto-modes-with-a-local-position) 推导）。
  - 飞行中的机体无法切换到此模式（无全局位置时）。
  - 飞行中的机体在失去位置估算时会进入失败安全状态。
- 模式要求已设置起始点。
- 模式禁止解锁（切换到此模式时机体必须已解锁）。
- RC 控制开关可用于在任何机身上切换飞行模式。
- RC 操纵杆移动将 [默认](#COM_RC_OVERRIDE) 将机体切换到 [Position 模式](../flight_modes_mc/position.md)，除非正在处理电池临界失败安全。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 技术摘要

多旋翼默认使用 [家/集结点返回类型](../flight_modes/return.md#home-rally-point-return-type-rtl-type-0) (RTL_TYPE=0)。对于该返回类型，飞行器将执行以下操作：

- 上升至 [最小返回高度](#最小返回高度)（高于预期障碍物的安全高度）。若初始高度高于最小返回高度，则保持初始高度不变。
- 通过固定高度直线路径飞往安全着陆点（家点与最近集结点中的最近点）。
- 抵达目标点后，快速下降至“下降高度”([RTL_DESCEND_ALT](#RTL_DESCEND_ALT))。
- 等待可配置的延时时间([RTL_LAND_DELAY](#RTL_LAND_DELAY))，可用于展开起落架。
- 最后执行着陆。

### 最小返回高度

默认情况下，_最小返回高度_ 通过 [RTL_RETURN_ALT](#RTL_RETURN_ALT) 设置，飞行器将返回 `RTL_RETURN_ALT` 与初始高度中的较大值。

通过 [RTL_CONE_ANG](#RTL_CONE_ANG) 可进一步配置最小返回高度。该参数与 [RTL_RETURN_ALT](#RTL_RETURN_ALT) 共同定义一个以目标着陆点为中心的半锥体。当返回模式在接近目标点时执行时，锥角允许设置较低的最小返回高度。这在目标点附近障碍物较少时非常有用，因为它可能降低飞行器着陆前需要上升的最小高度，从而减少能耗和着陆时间。

![返回模式锥体](../../assets/flying/rtl_cone.jpg)

当返回模式在由最大锥体半径和 `RTL_RETURN_ALT` 定义的圆柱体内触发时，锥体会影响最小返回高度：圆柱体外使用 `RTL_RETURN_ALT`。锥体内的最小返回高度为飞行器位置与锥体的交点高度（或 `RTL_DESCEND_ALT`，取较大者）。换句话说，如果当前高度低于 `RTL_DESCEND_ALT`，飞行器必须至少上升至 `RTL_DESCEND_ALT`。

关于该返回类型的更多信息，请参见 [家/集结点返回类型 (RTL_TYPE=0)](../flight_modes/return.md#home-rally-point-return-type-rtl-type-0)

## 参数

RTL 参数列于 [参数参考 > 返回模式](../advanced_config/parameter_reference.md#return-mode)。

对于多旋翼（假设 [RTL_TYPE](../advanced_config/parameter_reference.md#RTL_TYPE) 设置为 0）相关的参数如下所示。

| 参数                                                                                             | 描述                                                                                                                                                                                                                                                                                                                                                                               |
|-------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <a id="RTL_RETURN_ALT"></a>[RTL_RETURN_ALT](../advanced_config/parameter_reference.md#RTL_RETURN_ALT)     | 当 [RTL_CONE_ANG](../advanced_config/parameter_reference.md#RTL_CONE_ANG) 为 0 时的返回高度（默认值：60米）。如果当前高度已高于此值，则机体将在当前高度返回。                                                                                                                                                                                                                           |
| <a id="RTL_DESCEND_ALT"></a>[RTL_DESCEND_ALT](../advanced_config/parameter_reference.md#RTL_DESCEND_ALT)  | 最小返回高度，以及从更高返回高度开始下降时的机体降速或停止高度（默认值：30米）                                                                                                                                                                                                                                                                                                      |
| <a id="RTL_LAND_DELAY"></a>[RTL_LAND_DELAY](../advanced_config/parameter_reference.md#RTL_LAND_DELAY)     | 在 `RTL_DESCEND_ALT` 悬停至着陆的持续时间（默认值：0.5秒）- 默认此周期较短，机体将直接减速后立即着陆。若设置为 -1，系统将在 `RTL_DESCEND_ALT` 盘旋而非着陆。此延迟用于允许配置降落伞展开时间（自动触发）。                                                                                                                                                                           |
| <a id="RTL_MIN_DIST"></a>[RTL_MIN_DIST](../advanced_config/parameter_reference.md#RTL_MIN_DIST)           | 从家位置水平距离触发升至锥形返回高度的最小值。如果机体与家的水平距离小于此值，则将直接在当前高度或 `RTL_DESCEND_ALT`（取较大值）返回，而非先升至 RTL_RETURN_ALT。                                                                                                                                                                                                                     |
| <a id="RTL_CONE_ANG"></a>[RTL_CONE_ANG](../advanced_config/parameter_reference.md#RTL_CONE_ANG)           | 定义机体 RTL 返回高度的锥形半角。取值（度）：0, 25, 45, 65, 80, 90。注意：0 表示"无锥形"（始终返回至 `RTL_RETURN_ALT` 或更高），而 90 表示机体必须在当前高度或 `RTL_DESCEND_ALT`（取较大值）返回。                                                                                                                                                                                       |
| <a id="COM_RC_OVERRIDE"></a>[COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE) | 控制多旋翼（或 VTOL 在 MC 模式）的操作杆动作是否导致切换至 [Position mode](../flight_modes_mc/position.md)（除机体处理电池紧急保护时）。此功能可分别对自动模式和外部模式启用，默认在自动模式中启用。                                                                                                                                                                                      |
| <a id="COM_RC_STICK_OV"></a>[COM_RC_STICK_OV](../advanced_config/parameter_reference.md#COM_RC_STICK_OV) | 操作杆移动量触发切换至 [Position mode](../flight_modes_mc/position.md)（如果 [COM_RC_OVERRIDE](#COM_RC_OVERRIDE) 已启用）。                                                                                                                                                                                                                                                        |

## 另请参见

- [返航模式 (通用)](../flight_modes/return.md)
- [返航模式 (固定翼)](../flight_modes_fw/return.md)
- [返航模式 (垂直起降)](../flight_modes_vtol/return.md)