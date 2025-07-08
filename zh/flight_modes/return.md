# 返回模式（通用机体）

<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_Return_ 飞行模式用于 _将机体飞向安全区域_，沿无障碍路径飞向安全目的地并着陆。

使用以下机型时应首先阅读以下主题：

- [多旋翼](../flight_modes_mc/return.md)
- [固定翼（Plane）](../flight_modes_fw/return.md)
- [VTOL](../flight_modes_vtol/return.md)

::: info

- 该模式为自动模式 - 不需要用户干预即可控制机体。
- 该模式需要全局三维位置估计（来自GPS或通过[局部位置](../ros/external_position_estimation.md#enabling-auto-modes-with-a-local-position)推断）。
  - 飞行器无法在没有全局位置的情况下切换到此模式。
  - 飞行器在失去位置估计时会触发失效保护。
- 该模式需要已设置家点位置。
- 该模式禁止解锁（切换到此模式时机体必须已解锁）。
- 通过遥控器模式切换开关可更改任何机体的飞行模式。
- 多旋翼（或VTOL的多旋翼模式）中的遥控器杆位移动[默认](#COM_RC_OVERRIDE)会将机体切换到[Position模式](../flight_modes_mc/position.md)，除非处理电池临界失效保护。
- VTOL将根据触发返回模式时的模式以MC或FW方式返回：
  在MC模式下会遵循多旋翼参数（如着陆"锥体"）；
  在FW模式下会遵循固定翼参数（忽略锥体），除非使用任务降落，否则会在巡航高度盘旋后转换为MC模式并着陆目的地。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 概述

PX4 提供了多种机制用于选择安全的返回路径、目的地和着陆方式，包括使用家的位置（home location）、集合点（rally "safe" points）、任务路径（mission paths）以及任务中定义的着陆序列。

理论上所有机体都支持这些机制，但并非所有机制对特定机体都同样适用。  
例如，多旋翼飞行器几乎可以在任何地方着陆，因此除非在极少数情况下，否则使用着陆序列没有意义。  
同样，固定翼飞行器需要执行安全的着陆路径：它可以将家的位置作为返回点，但默认情况下不会尝试在该位置着陆。

本主题将涵盖所有机体可能配置使用的返回类型——各机体专用的返回模式主题会分别说明每种机体的默认/推荐返回类型及配置方式。

以下章节将解释如何配置 [返回类型](#return_types)、[最小返回高度](#minimum-return-altitude) 以及 [着陆/到达行为](#loiter-landing-at-destination)。

<a id="return_types"></a>## 返回类型 (RTL_TYPE)

PX4 提供了四种寻找无障碍安全路径返回/降落的替代方案，可通过 [RTL_TYPE](#RTL_TYPE) 参数进行设置。

总体而言这些模式包括：

- [Home/集结点返回](#home_return) (`RTL_TYPE=0`)：爬升至安全高度，通过直接路径返回最近的集结点或Home位置。
- [任务降落/集结点返回类型](#mission-landing-rally-point-return-type-rtl-type-1) (`RTL_TYPE=1`)：爬升至安全高度，直接飞向除Home外的最近目标：集结点或任务降落模式起点。  
  如果未定义任务降落或集结点，则通过直接路径返回Home。
- [任务路径返回类型](#mission-path-return-type-rtl-type-2) (`RTL_TYPE=2`)：使用任务路径快速延续至任务降落模式（如果定义）。  
  如果未定义任务降落模式，则快速反向任务返回Home。  
  如果未定义任务，则直接返回Home（忽略集结点）。
- [最近安全目标返回类型](#closest-safe-destination-return-type-rtl-type-3) (`RTL_TYPE=3`)：爬升至安全高度，通过直接路径返回最近目标：Home、任务降落模式起点或集结点。  
  如果目标是任务降落模式，则遵循该模式降落。

每种类型的详细说明如下。

<a id="home_return"></a>

### Home/集结点返回类型 (RTL_TYPE=0)

这是 [多旋翼](../flight_modes_mc/return.md) 的默认返回类型（更多信息请参见相关主题）。

在此返回类型中，机体：

- 爬升至安全的 [最小返回高度](#minimum-return-altitude)（高于预期障碍物）。
- 通过直接路径飞向Home位置或集结点（以较近者为准）。
- 在 [到达](#loiter-landing-at-destination) 后下降至"下降高度"并等待可配置时间。  
  此时间可用于展开起落架。
- 降落或等待（取决于降落参数），  
  默认情况下，MC或以MC模式运行的VTOL会降落，固定翼机会在下降高度盘旋。  
  以FW模式运行的VTOL会调整航向对准目标点，切换至MC模式后降落。

::: info
如果未定义集结点，这与 _Return to Launch_ (RTL)/_Return to Home_ (RTH) 相同。
:::

### 任务降落/集结点返回类型 (RTL_TYPE=1)

这是 [固定翼](../flight_modes_fw/return.md) 或 [VTOL](../flight_modes_vtol/return.md) 的默认返回类型（更多信息请参见相关主题）。

在此返回类型中，机体：

- 如果需要，爬升至安全的 [最小返回高度](#minimum-return-altitude)（高于预期障碍物）。  
  如果当前高度高于最小返回高度，则保持该高度。
- 通过直接恒定高度路径飞向集结点或 [任务降落模式](#mission-landing-pattern) 起点（以较近者为准）。  
  如果未定义任务降落或集结点，则通过直接路径返回Home。
- 如果目标是任务降落模式，将遵循该模式降落。
- 如果目标是集结点或Home，将在下降高度 [降落或等待](#loiter-landing-at-destination)（取决于降落参数）。  
  默认情况下，MC或以MC模式运行的VTOL会降落，固定翼机在下降高度盘旋。  
  以FW模式运行的VTOL会调整航向对准目标点，切换至MC模式后降落。

::: info
固定翼飞机通常还会设置 [MIS_TKO_LAND_REQ](#MIS_TKO_LAND_REQ) 以 _要求_ 任务降落模式。
:::

### 任务路径返回类型 (RTL_TYPE=2)

此返回类型使用任务（如果定义）提供安全返回 _路径_，并使用 [任务降落模式](#mission-landing-pattern)（如果定义）提供降落行为。  
如果定义了任务但未定义任务降落模式，则任务将被 _反向_ 飞行。  
集结点（如果有定义）将被忽略。

::: info
行为较为复杂，因为它取决于飞行模式，以及任务和任务降落模式是否定义。
:::

**带降落模式的任务**:

- **任务模式**：任务以"快速前进模式"继续执行（跳过、延迟和其他非位置指令被忽略，盘旋和其他位置航点转换为简单航点），然后降落。
- **除任务模式外的自动模式**：  
  - 爬升至安全的 [最小返回高度](#minimum-return-altitude)（高于预期障碍物）。  
  - 直接飞向最近航点（固定翼非降落航点）并下降至航点高度。  
  - 从该航点以快速前进模式继续任务。
- **手动模式**：  
  - 爬升至安全的 [最小返回高度](#minimum-return-altitude)（高于预期障碍物）。  
  - 直接飞向降落序列位置并下降至航点高度  
  - 使用任务降落模式降落

**无降落模式定义的任务**:

- **任务模式**：  
  - 从上一航点开始以"快速反向"模式（反向）飞行任务。  
    - 跳过、延迟和其他非位置指令被忽略，盘旋和其他位置航点转换为简单航点。  
    - VTOL在反向飞行任务前（如果需要）切换至FW模式。  
  - 到达第一个航点后，机体爬升至 [最小返回高度](#minimum-return-altitude) 并飞向Home位置（在那里 [降落或等待](#loiter-landing-at-destination)）。
- **除任务模式外的自动模式**：  
  - 直接飞向最近航点（固定翼非降落航点）并下降至航点高度。  
  - 与任务模式中相同方式反向继续任务（如上所述触发Return模式）
- **手动模式**：直接飞向Home位置并降落。

如果未定义任务，PX4将直接飞向Home位置并降落（忽略集结点）。

如果在返回模式中任务发生变化，则根据新任务重新评估行为，遵循与上述相同的规则（例如，如果新任务无降落序列且处于任务模式，则任务将被反向）。

### 最近安全目标返回类型 (RTL_TYPE=3)

在此返回类型中，机体：

- 爬升至安全的 [最小返回高度](#minimum-return-altitude)（高于预期障碍物）。
- 通过直接路径返回最近目标：Home、集结点或任务降落模式起点。
- 如果目标是任务降落模式，则遵循该模式降落。

## 最低返回高度

对于大多数[返回类型](#return_types)，机体将在返回前上升到_最低安全高度_（除非当前高度已高于该高度），以避免其与目标点之间的任何障碍物。

::: info
例外情况是在任务路径返回时，当机体处于任务中时执行[任务路径返回](#mission-path-return-type-rtl-type-2)。在这种情况下，机体将遵循任务航点，我们假设这些航点已规划为避让任何障碍物。
:::

固定翼机体或固定翼模式下的VTOL的返回高度通过参数[RTL_RETURN_ALT](#RTL_RETURN_ALT)配置（不使用下一段描述的代码）。

多旋翼或MC模式下的VTOL的返回高度通过参数[RTL_RETURN_ALT](#RTL_RETURN_ALT)和[RTL_CONE_ANG](#RTL_CONE_ANG)配置，这两个参数定义了一个以目标（家点或安全点）为中心的半圆锥体。

![Return mode cone](../../assets/flying/rtl_cone.jpg)

<!-- Original draw.io diagram can be found here: https://drive.google.com/file/d/1W72XeZYSOkRlBSbPXCCiam9NMAyAWSg-/view?usp=sharing -->

当机体处于以下位置时：

- 高于[RTL_RETURN_ALT](#RTL_RETURN_ALT)（1）时，将保持当前高度返回。
- 低于圆锥体时，将在圆锥体交点（2）或[RTL_DESCEND_ALT](#RTL_DESCEND_ALT)（取较大值）处返回。
- 处于圆锥体外（3）时，将先爬升至[RTL_RETURN_ALT](#RTL_RETURN_ALT)。
- 处于圆锥体内：
  - 高于[RTL_DESCEND_ALT](#RTL_DESCEND_ALT)（4）时，将保持当前高度返回。
  - 低于[RTL_DESCEND_ALT](#RTL_DESCEND_ALT)（5）时，将先爬升至`RTL_DESCEND_ALT`。

注意：

- 如果[RTL_CONE_ANG](#RTL_CONE_ANG)为0度，则不存在"圆锥体"：
  - 机体将在`RTL_RETURN_ALT`（或以上）高度返回。
- 如果[RTL_CONE_ANG](#RTL_CONE_ANG)为90度，机体将返回至`RTL_DESCEND_ALT`和当前高度的较大值。
- 机体返回时将始终至少爬升至[RTL_DESCEND_ALT](#RTL_DESCEND_ALT)。

## 在目标点盘旋/降落

除非在返回模式中执行[任务降落模式](#mission-landing-pattern)，否则机体将抵达目标点并快速下降到[RTL_DESCEND_ALT](#RTL_DESCEND_ALT)高度，在该高度处将盘旋[RTL_LAND_DELAY](#RTL_LAND_DELAY)时间后降落。  
若`RTL_LAND_DELAY=-1`，机体将无限盘旋。

默认降落配置取决于机体类型：

- 多旋翼会短暂悬停（必要时展开起落架）后降落。
- 固定翼使用包含[任务降落模式](#mission-landing-pattern)的返回模式，因为这可实现自动降落。  
  若不使用任务降落模式，则默认配置为无限盘旋，用户可接管并手动降落。
- VTOL在MC模式下飞行和降落方式与多旋翼完全相同。
- VTOL在FW模式下飞向降落点，切换至MC模式后在目标点降落。

## 任务降落模式

任务降落模式是作为任务计划一部分定义的降落模式。
它由 [MAV_CMD_DO_LAND_START](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_LAND_START) 命令、一个或多个位置航点，以及 [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND) 命令（对于VTOL机体则使用 [MAV_CMD_NAV_VTOL_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_LAND)）组成。

在任务中定义的降落模式是PX4上自动降落固定翼机体最安全的方式。
因此，固定翼机体默认配置为使用 [任务降落/集结点返回](#mission-landing-rally-point-return-type-rtl-type-1) 类型的RTL（Return To Launch）模式。

## 参数

RTL 参数列在 [参数参考 > 返回模式](../advanced_config/parameter_reference.md#return-mode)（如下总结）。

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="RTL_TYPE"></a>[RTL_TYPE](../advanced_config/parameter_reference.md#RTL_TYPE)                         | 返回机制（路径和目的地）。<br>`0`: 通过直接路径返回最近的集合点或家点。<br>`1`: 通过直接路径返回最近的集合点或任务降落模式起点。若未定义任务降落或集合点则直接返回家点。若目的地是任务降落模式，则按照该模式降落。<br>`2`: 若定义了降落模式则使用任务路径快速前进至降落，否则快速返回家点。忽略集合点。若未定义任务计划则直接飞回家点。<br>`3`: 通过直接路径返回最近的目的地：家点、任务降落模式起点或安全点。若目的地是任务降落模式，则按照该模式降落。 |
| <a id="RTL_RETURN_ALT"></a>[RTL_RETURN_ALT](../advanced_config/parameter_reference.md#RTL_RETURN_ALT)       | 当 [RTL_CONE_ANG](../advanced_config/parameter_reference.md#RTL_CONE_ANG) 为 0 时的返回高度（默认：60 米）。若当前高度已高于该值，则机体以当前高度返回。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| <a id="RTL_DESCEND_ALT"></a>[RTL_DESCEND_ALT](../advanced_config/parameter_reference.md#RTL_DESCEND_ALT)    | 最小返回高度以及机体从较高返回高度开始减速或停止初始下降的高度（默认：30 米）                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| <a id="RTL_LAND_DELAY"></a>[RTL_LAND_DELAY](../advanced_config/parameter_reference.md#RTL_LAND_DELAY)       | 在 `RTL_DESCEND_ALT` 高度等待着陆的时间（默认：0.5 秒）- 默认周期较短以便机体减速后立即着陆。若设置为 -1 则系统将在 `RTL_DESCEND_ALT` 盘旋而非着陆。延迟时间用于配置着陆装置展开的时间（自动触发）。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <a id="RTL_MIN_DIST"></a>[RTL_MIN_DIST](../advanced_config/parameter_reference.md#RTL_MIN_DIST)             | 触发升至"锥体"指定返回高度的水平最小距离。若机体与家点的水平距离小于该值，它将直接以当前高度或 `RTL_DESCEND_ALT`（较高者）返回，而非先升至 RTL_RETURN_ALT。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| <a id="RTL_CONE_ANG"></a>[RTL_CONE_ANG](../advanced_config/parameter_reference.md#RTL_CONE_ANG)             | 定义机体 RTL 返回高度的锥体半角。取值（度数）：0, 25, 45, 65, 80, 90。注意 0 表示"无锥体"（始终以 `RTL_RETURN_ALT` 或更高高度返回），而 90 表示机体必须以当前高度或 `RTL_DESCEND_ALT`（较高者）返回。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| <a id="COM_RC_OVERRIDE"></a>[COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE)    | 控制多旋翼（或 VTOL 的 MC 模式）上摇杆移动是否导致进入 [Position mode](../flight_modes_mc/position.md)（除机体处理电池紧急情况时）。此功能可分别针对自动模式和外部模式启用，且在自动模式下默认启用。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| <a id="COM_RC_STICK_OV"></a>[COM_RC_STICK_OV](../advanced_config/parameter_reference.md#COM_RC_STICK_OV)    | 导致进入 [Position mode](../flight_modes_mc/position.md) 的摇杆移动量（当 [COM_RC_OVERRIDE](#COM_RC_OVERRIDE) 启用时）。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| <a id="RTL_LOITER_RAD"></a>[RTL_LOITER_RAD](../advanced_config/parameter_reference.md#RTL_LOITER_RAD)       | [Fixed-wing Only] 在 [RTL_LAND_DELAY](#RTL_LAND_DELAY) 时盘旋圈半径。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| <a id="MIS_TKO_LAND_REQ"></a>[MIS_TKO_LAND_REQ](../advanced_config/parameter_reference.md#MIS_TKO_LAND_REQ) | 指定任务降落或起飞模式是否为_必需_。通常固定翼机体设置为需要降落模式但 VTOL 不需要。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |