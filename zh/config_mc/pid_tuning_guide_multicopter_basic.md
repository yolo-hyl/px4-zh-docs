# 多旋翼 PID 调参指南（手动/基础）

本教程介绍了如何在 PX4 上为所有 [多旋翼机型配置](../airframes/airframe_reference.md#copter)（四旋翼、六旋翼、八旋翼等）_手动_ 调整 PID 回路。

:::tip
[Autotune](../config/autotune_mc.md) 推荐大多数用户使用，因为它更快捷、更容易且能为大多数机型提供良好的调参效果。
手动调参推荐在自动调参无法工作或需要精细调整的机型中使用。
:::

一般来说，如果你使用的是适当的 [支持的机体配置](../airframes/airframe_reference.md#copter)，默认调参应允许你安全地飞行机体。
建议所有新机体配置进行调参以获得_最佳_性能，因为相对较小的硬件和装配变化都可能影响实现最佳飞行所需的增益。
例如，不同的电调或电机会改变最佳调参增益。

## 简介

PX4 使用 **比例（Proportional）、积分（Integral）、微分（Derivative）**（PID）控制器（这是应用最广泛的控制技术）。

_QGroundControl_ **PID调参** 设置提供机体设定值和响应曲线的实时图表。  
调参的目标是设置 P/I/D 值，使 _响应曲线_ 尽可能接近 _设定值曲线_（即快速响应且无超调）。

![QGC 速率控制器调参界面](../../assets/mc_pid_tuning/qgc_mc_pid_tuning_rate_controller.png)

控制器是分层结构，意味着高级控制器会将其结果传递给低级控制器。  
最低级控制器是 **速率控制器**，然后是 **姿态控制器**，最后是 **速度与位置控制器**。  
PID调参需要按照此顺序进行，需从速率控制器开始，因为它会影响所有其他控制器。

每个控制器（速率、姿态、速度/位置）和轴（偏航、横滚、俯仰）的测试流程始终相同：通过快速移动操纵杆产生快速设定值变化，并观察响应。  
然后调整滑块（如下所述）以改进响应对设定值的跟踪。

:::tip

- 速率控制器调参最重要，调参良好时其他控制器通常无需或仅需少量调整  
- 横滚和俯仰通常可使用相同的调参增益  
- 使用 Acro/Stabilized/Altitude 模式调参速率控制器  
- 使用 [Position模式](../flight_modes_mc/position.md) 调参 _速度控制器_ 和 _位置控制器_  
  确保切换到 _简单位置控制_ 模式以生成阶跃输入。  
  ![QGC PID调参: 简单控制选择器](../../assets/mc_pid_tuning/qgc_mc_pid_tuning_simple_control.png)  
  :::

## 前提条件

- 您已为机体选择了最接近的[默认机架配置](../config/airframe.md)。
  这将为您提供一个可正常飞行的机体。
- 您应已完成[电调校准](../advanced_config/esc_calibration.md)。
- 如果使用PWM输出，其最小值应在[执行器配置](../config/actuators.md)中正确设置。
  这些值需要设置得足够低，但要确保机体上锁时**电机不会停止**。

  可在[Acro模式](../flight_modes_mc/acro.md)或[Stabilized模式](../flight_modes_mc/manual_stabilized.md)中进行测试：

  - 移除螺旋桨
  - 解锁机体并将油门降至最低
  - 将机体向各个方向倾斜约60度
  - 检查所有电机是否都不停转

- 尽可能使用高速率的数传链路（如WiFi）（典型的低范围数传电台无法满足实时反馈和绘图需求）。
  这对角速度控制器尤为重要。
- 调整机体前请禁用[MC_AIRMODE](../advanced_config/parameter_reference.md#MC_AIRMODE)（PID调整界面中提供此选项）。

:::warning
调校不当的机体可能导致不稳定，容易坠毁。
请务必设置好[紧急开关](../config/safety.md#emergency-switches)。
:::

## 调参流程

调参流程如下：

1. 解锁机体，起飞并悬停（通常在[Position mode](../flight_modes_mc/position.md)模式下进行）
1. 打开 _QGroundControl_ **Vehicle Setup > PID Tuning**
   ![QGC Rate Controller Tuning UI](../../assets/mc_pid_tuning/qgc_mc_pid_tuning_rate_controller.png)
1. 选择 **Rate Controller** 标签页
1. 确认气动模式选择器设置为 **Disabled**
1. 设置 _Thrust curve_ 值为：0.3（PWM/功率型控制器）或 1（RPM型电调）

   ::: info
   对于PWM/功率型和（部分）UAVCAN速度控制器，控制信号与推力之间的关系可能非线性。
   结果是，悬停推力下的最佳调参在机体以更高推力运行时可能不理想。

   推力曲线值可用于补偿这种非线性：

   - 对于PWM控制器，0.3 是一个很好的默认值（可能需要[进一步调参](../config_mc/pid_tuning_guide_multicopter.md#thrust-curve)）
   - 对于RPM型控制器，使用 1（无需进一步调参，因为这些控制器的推力曲线是二次方的）

   更多详情请参见 [详细PID调参指南](../config_mc/pid_tuning_guide_multicopter.md#thrust-curve)
   :::

1. 设置 _Select Tuning_ 单选按钮为：**Roll**
1. （可选）选择 **Automatic Flight Mode Switching** 复选框。
   这将在您按下 **Start** 按钮时_自动_从[Position mode](../flight_modes_mc/position.md)切换到[Stabilised mode](../flight_modes_mc/manual_stabilized.md)
1. 对于速率控制器调参，切换到 _Acro mode_、_Stabilized mode_ 或 _Altitude mode_（除非启用了自动切换）
1. 点击 **Start** 按钮以开始跟踪设定值和响应曲线
1. 快速移动 _roll摇杆_ 全行程，并观察图上的阶跃响应
   :::tip
   停止跟踪以便更方便地检查曲线图。
   当您缩放/平移时会自动停止。
   使用 **Start** 按钮重新开始曲线图，**Clear** 用于重置
   :::
1. 使用滑块修改三个PID值（对于滚转速率调参，这些值影响 `MC_ROLLRATE_K`、`MC_ROLLRATE_I`、`MC_ROLLRATE_D`），并再次观察阶跃响应。
   当滑块移动时，值会立即保存到机体
   ::: info
   目标是让 _Response_ 曲线尽可能接近 _Setpoint_ 曲线（即快速响应且无超调）
   :::
   PID值可按以下方式调整：
   - P（比例）或K增益：
     - 增加以提高响应性
     - 如果响应超调和/或振荡，应减少（在一定程度内增加D增益也有帮助）
   - D（微分）增益：
     - 可增加以抑制超调和振荡
     - 仅在需要时增加，因为它会放大噪声（可能导致电机过热）
   - I（积分）增益：
     - 用于减少稳态误差
     - 如果太低，响应可能永远无法达到设定值（例如在有风的情况下）
     - 如果太高，可能发生慢速振荡
1. 重复上述调参过程对俯仰和偏航轴：
   - 使用 _Select Tuning_ 单选按钮选择要调参的轴
   - 移动相应的摇杆（即俯仰轴用俯仰摇杆，偏航轴用偏航摇杆）
   - 对于俯仰调参，从与滚转相同的值开始
     :::tip
     使用 **Save to Clipboard** 和 **Reset from Clipboard** 按钮将滚转设置复制到俯仰的初始设置
     :::
1. 重复调参过程对所有轴的姿态控制器
1. 重复调参过程对速度和位置控制器（所有轴）

   - 调参时使用Position mode
   - 在 _Position control mode ..._ 选择器中选择 **Simple position control** 选项（这允许直接控制以生成阶跃输入）

     ![QGC PID tuning: Simple control selector](../../assets/mc_pid_tuning/qgc_mc_pid_tuning_simple_control.png)

全部完成！
记得离开设置前重新启用气动模式