# 特技模式（多旋翼）

<img src="../../assets/site/difficulty_hard.png" title="飞行难度高" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="需要手动/遥控控制" width="30px" />&nbsp;

_Acro mode_ 是用于执行特技动作的遥控模式，例如翻转、滚转和环飞。

滚转、俯仰和偏航操纵杆控制机体绕对应轴的角速度，油门直接传递到控制分配。当操纵杆居中时，机体将停止旋转，但保持当前姿态（侧飞、倒飞等），并根据当前动量移动。

![多旋翼特技飞行](../../assets/flight_modes/acrobatic_mc.png)

<!-- 上述图片错误: https://github.com/PX4/PX4-user_guide/issues/182 -->

## 技术描述

执行特技动作的手动模式，例如翻转、滚转和环飞。

遥控器滚转/俯仰/偏航（RPY）操纵杆输入控制机体绕对应轴的角速度。油门直接传递到控制分配。当操纵杆居中时，机体将停止旋转，但保持当前姿态（不一定是水平状态），并根据当前动量移动。

需要手动控制输入（如遥控器控制、手柄）：

- 滚转、俯仰、偏航：自动驾驶仪提供姿态率稳定的辅助。
  遥控器操纵杆位置映射到对应姿态的旋转速率。
- 油门：通过遥控器操纵杆进行手动控制。
  遥控输入直接发送到控制分配。

## 操纵杆输入映射

[参数](#参数)中 expo 和 rate 的默认值为_新手友好型_，可降低用户首次尝试此模式或使用特技模式进行手动率调时翻转机体的概率。

但这些值对特技飞行并不理想！
以下参数值更适合有经验的用户：

- [MC_ACRO_R_MAX](#MC_ACRO_R_MAX): `720`（每秒2转）
- [MC_ACRO_P_MAX](#MC_ACRO_P_MAX): `720`（每秒2转）
- [MC_ACRO_Y_MAX](#MC_ACRO_Y_MAX): `540`（每秒2转）
- [MC_ACRO_EXPO](#MC_ACRO_R_MAX): `0.69`
- [MC_ACRO_EXPO_Y](#MC_ACRO_EXPO_Y): `0.69`
- [MC_ACRO_SUPEXPO](#MC_ACRO_SUPEXPO): `0.69`
- [MC_ACRO_SUPEXPOY](#MC_ACRO_SUPEXPOY): `0.7`

使用上述值时，特技模式下滚转、俯仰和偏航的操纵杆输入映射如图所示。曲线在最大操纵杆输入时提供高转速以执行特技动作，而在操纵杆中心附近提供较低灵敏度区域用于微调。

![特技模式 - 默认输入曲线](../../assets/flight_modes/acro_mc_input_curve_expo_superexpo_default.png)

可通过 [MC_ACRO_EXPO](#MC_ACRO_EXPO) 和 [MC_ACRO_SUPEXPO](#MC_ACRO_SUPEXPO) "指数" 参数调整滚转和俯仰的操纵杆输入响应，偏航操纵杆输入响应则通过 [MC_ACRO_EXPO_Y](#MC_ACRO_EXPO_Y) 和 [MC_ACRO_SUPEXPOY](#MC_ACRO_SUPEXPOY) 进行调整。`MC_ACRO_EXPO` 和 `MC_ACRO_EXPO_Y` 用于在纯线性曲线和立方曲线之间调整曲线形状。`MC_ACRO_SUPEXPO` 和 `MC_ACRO_SUPEXPOY` 进一步调整形状，修改低灵敏度区域的宽度。

![特技模式 - expo - 纯线性输入曲线](../../assets/flight_modes/acro_mc_input_curve_expo_linear.png) ![特技模式 - expo - 纯立方输入曲线](../../assets/flight_modes/acro_mc_input_curve_expo_cubic.png)

::: info
数学关系为：

$$\mathrm{y} = r(f \cdot x^3 + x(1-f)) (1-g)/(1-g |x|)$$

其中 `f = MC_ACRO_EXPO` 或 `MC_ACRO_EXPO_Y`，`g = MC_ACRO_SUPEXPO` 或 `MC_ACRO_SUPEXPOY`，`r` 为最大速率。

您可以使用 [PX4 SuperExpo 计算器](https://www.desmos.com/calculator/yty5kgurmc) 图形化地实验这些关系。
:::

## 参数

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                                                                        |
| ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="MC_ACRO_EXPO"></a>[MC_ACRO_EXPO](../advanced_config/parameter_reference.md#MC_ACRO_EXPO)         | 特技模式 "指数" 因子，用于调整滚转和俯仰的操纵杆输入曲线形状。值：`0` 纯线性输入曲线，`1` 纯立方输入曲线。默认：`0`。                                                                                                                       |
| <a id="MC_ACRO_EXPO_Y"></a>[MC_ACRO_EXPO_Y](../advanced_config/parameter_reference.md#MC_ACRO_EXPO_Y)   | 特技模式 "指数" 因子，用于调整偏航的操纵杆输入曲线形状。值：`0` 纯线性输入曲线，`1` 纯立方输入曲线。默认：`0.69`。                                                                                                                               |
| <a id="MC_ACRO_SUPEXPO"></a>[MC_ACRO_SUPEXPO](../advanced_config/parameter_reference.md#MC_ACRO_SUPEXPO) | 特技模式 "SuperExpo" 因子，用于优化滚转和俯仰轴的操纵杆输入曲线形状（通过 `MC_ACRO_EXPO` 调整）。值：`0` 纯 Expo 函数，`0.7` 合理的形状增强以获得直观的操纵杆手感，`0.95` 仅在最大值附近有强弯曲输入曲线。默认：`0`。 |
| <a id="MC_ACRO_SUPEXPOY"></a>[MC_ACRO_SUPEXPOY](../advanced_config/parameter_reference.md#MC_ACRO_SUPEXPOY) | 特技模式 "SuperExpo" 因子，用于优化偏航轴的操纵杆输入曲线形状（通过 `MC_ACRO_EXPO_Y` 调整）。值：`0` 纯 Expo 函数，`0.7` 合理的形状增强以获得直观的操纵杆手感，`0.95` 仅在最大值附近有强弯曲输入曲线。默认：`0`。    |
| <a id="MC_ACRO_P_MAX"></a>[MC_ACRO_P_MAX](../advanced_config/parameter_reference.md#MC_ACRO_P_MAX)      | 最大特技俯仰速率。默认：`100.0` 度/秒。                                                                                                                                                                                                                                                       |
| <a id="MC_ACRO_R_MAX"></a>[MC_ACRO_R_MAX](../advanced_config/parameter_reference.md#MC_ACRO_R_MAX)      | 最大特技滚转速率。默认：`100` 度/秒。                                                                                                                                                                                                                                                          |
| <a id="MC_ACRO_Y_MAX"></a>[MC_ACRO_Y_MAX](../advanced_config/parameter_reference.md#MC_ACRO_Y_MAX)      | 最大特技偏航速率。默认：`540` 度/秒。                                                                                                                                                                                                                                                          |