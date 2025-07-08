# 切换状态估计器

本页面介绍可用的状态估计器及其切换方法。

:::tip
EKF2 是默认选项，除非有特殊原因不使用（特别是搭载GNSS/GPS的机体）。
Q估计器在无GPS情况下可用，常用于[多旋翼竞速机](../config_mc/racer_setup.md)。
:::

## 可用估计器

可用的估计器包括：

- **EKF2 态度、位置和风状态估计器**（推荐）- 扩展卡尔曼滤波器，用于估计姿态、三维位置/速度和风状态。
- **LPE 位置估计器**（已弃用）- 扩展卡尔曼滤波器，用于三维位置和速度状态。

  :::warning
  LPE 已被弃用。
  当前版本（PX4 v1.14）仍可运行，但不再提供支持和维护。
  :::

- **Q 态度估计器** - 基于四元数的简单互补滤波器，用于姿态估计。
  不需要GPS、磁力计或气压计。
  <!-- Q估计器在PX4 v1.14中受支持。测试代码见PX4-Autopilot/pull/21922 -->

## 如何启用不同估计器

<!-- 修改于 https://github.com/PX4/PX4-Autopilot/pull/22567 v1.14之后 -->

要启用特定估计器，请启用其参数并禁用其他参数：

- [EKF2_EN](../advanced_config/parameter_reference.md#EKF2_EN) - EKF2（默认/推荐）
- [ATT_EN](../advanced_config/parameter_reference.md#ATT_EN) - Q估计器（基于四元数的姿态估计器）
- [LPE_EN](../advanced_config/parameter_reference.md#LPE_EN) - LPE（固定翼不支持）

::: warning
必须启用且只能启用一个估计器。
如果启用了多个，将使用第一个发布UOrb话题[vehicle_attitude](../msg_docs/VehicleAttitude.md)或[vehicle_local_position](../msg_docs/VehicleLocalPosition.md)的估计器。
如果全部禁用则话题不会发布。
:::

::: info
对于FMU-v2（仅限），还需要构建PX4以包含所需估计器（例如EKF2: `make px4_fmu-v2`, LPE: `make px4_fmu-v2_lpe`）。
这是必需的，因为FMU-v2资源受限无法同时包含两个估计器。
其他Pixhawk FMU版本均包含两个估计器。
:::