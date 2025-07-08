# 多旋翼设定点调校（轨迹生成器）

本文件概述了影响多旋翼**用户体验**的调校参数：机体对操作杆移动或任务中方向变化的响应速度、最大允许速度等。

换句话说，本主题解释如何调校影响**期望设定点**数值的参数（而非影响机体**跟踪**设定点能力的参数）。生成这些设定点的算法称为"轨迹生成器"。

:::warning
本指南面向高级用户/专家。
:::

:::tip
在执行此处描述的任何调校前，请先遵循[Multicopter PID Tuning Guide](../config_mc/pid_tuning_guide_multicopter.md)的说明。
不要使用这些调校参数来修复跟踪不良或振动问题！
:::

## 概述

P/PID控制器的输入是一个机体应尝试跟踪的**期望设定点**。
[PID Tuning](../config_mc/pid_tuning_guide_multicopter.md)（"底层调校"）旨在减少期望设定点与机体状态估计值之间的误差。

传递给P/PID控制器的**期望设定点**本身是通过**需求设定点**计算得出的，该需求设定点来自操作杆位置（在遥控模式下）或任务指令。
需求设定点可能变化非常迅速（例如用户将操作杆从零移动到最大值的"阶跃"动作）。
若期望设定点以"斜坡"方式变化，机体飞行特性将更优。

**设定点数值调校**（"高层调校"）用于定义**需求设定点**与**期望设定点**之间的映射关系 - 即定义期望设定点跟随需求设定点的"斜坡"。

:::tip
调校不当的[P/PID Gains](../config_mc/pid_tuning_guide_multicopter.md)可能导致不稳定。
调校不当的**设定点数值**不会导致不稳定，但可能导致对设定点变化的反应过于突兀或响应迟钝。
:::

<a id="modes"></a>

## 飞行模式轨迹支持

[Mission mode](../flight_modes_mc/mission.md)始终使用[Jerk-limited](../config_mc/mc_jerk_limited_type_trajectory.md)轨迹。

[Position mode](../flight_modes_mc/position.md)支持以下[实现](#position-mode-implementations)：
默认使用加速度映射；其他类型可通过[MPC_POS_MODE](../advanced_config/parameter_reference.md#MPC_POS_MODE)设置。

[Altitude mode](../flight_modes_mc/altitude.md)同样支持通过[MPC_POS_MODE](../advanced_config/parameter_reference.md#MPC_POS_MODE)选择的[实现](#altitude-mode-implementations)，但**仅**用于平滑垂直分量（即控制高度时）。

其他模式不支持轨迹调校。

## 位置模式实现

以下列表概述了操作杆输入如何被解释并转换为轨迹设定点的不同实现方式：

- 基于加速度（默认）
  - 水平操作杆输入映射为加速度设定点。
  - 直观的操作杆感觉，如同推着机体移动。
  - 在达到巡航速度时不会出现意外的倾角变化。
  - 垂直操作杆输入通过jerk-limited轨迹映射。
  - 通过 `MPC_POS_MODE=Acceleration based` 在位置模式中设置。
- [Jerk-limited](../config_mc/mc_jerk_limited_type_trajectory.md)
  - 用于需要平滑运动的场景（如：拍摄、测绘、货物运输）。
  - 生成对称的平滑S曲线，始终保证jerk和加速度限制。
  - 可能不适合需要快速响应的机体/使用场景 - 例如竞速四轴。
  - 通过 `MPC_POS_MODE=Smoothed velocity` 在位置模式中设置。
- **简易位置控制**
  - 操作杆直接映射到无平滑处理的速率设定点。
  - 用于速率控制调校。
  - 通过 `MPC_POS_MODE=Direct velocity` 在位置模式中设置。

## 高度模式实现

与[位置模式实现](#position-mode-implementations)类似，这些是解释垂直操作杆输入的实现方式：

- [Jerk-limited](../config_mc/mc_jerk_limited_type_trajectory.md)
  - 垂直输入平滑处理。
  - 通过 `MPC_POS_MODE` 设置为 Smoothed velocity 或 Acceleration based 在高度模式中使用。
- **简易高度控制**
  - 垂直输入无平滑处理。
  - 仅在使用 `MPC_POS_MODE=Direct velocity` 时在高度模式中设置。