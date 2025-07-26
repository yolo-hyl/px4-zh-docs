

<!-- 这是用于autotune_mc.md和autotune_fy.md中自动调参文档的单一来源
撰写本文时，仅FW、MC和VTOL支持自动调参。
VTOL有其独立的文档，该文档引用了其他两个文档
-->

<div v-if="$frontmatter.frame === 'Multicopter'">

# 自动调参（多旋翼）

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

# 自动调参（固定翼）

自动调参自动化了PX4速率和姿态控制器的调参过程，这些控制器对飞行的稳定性和响应性至关重要（其他调参属于"可选"操作）。

调参只需执行一次，除非您使用的是制造商已调参完成且未做任何修改的机体，否则建议执行自动调参。

:::warning
自动调参在飞行过程中进行。
机体必须具备足够的飞行性能以应对中等扰动，且需要密切注意：

- 测试您的机体是否[稳定到足以进行自动调参](#调参前测试)
- 准备随时中止自动调参过程
  您可以通过切换飞行模式<div style="display: inline;" v-if="$frontmatter.frame === 'Plane'"> 或使用自动调参开关（[如已配置](#enable-disable-autotune-switch)）</div>
- 调参完成后验证机体飞行性能

:::

<lite-youtube videoid="5xswOhhqrIQ" title="QGroundControl Autotune Feature Breakdown for PX4 Autopilot"/>

## 调参前测试

机体必须能够飞行并充分稳定自身才能进行自动调参。
此测试确保机体能够在位置控制模式下安全飞行。

为确保机体稳定性满足自动调参要求：

1. 执行常规飞行前安全检查清单，确保飞行区域空旷且有足够的空间。

2. 起飞并<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">在[高度模式](../flight_modes_mc/altitude.md)或[稳定模式](../flight_modes_mc/manual_stabilized.md)下悬停在离地1米高处</div><div style="display: inline;" v-else-if="$frontmatter.frame === 'Plane'">以巡航速度在[位置模式](../flight_modes_fw/position.md)或[高度模式](../flight_modes_fw/altitude.md)下飞行</div>。

3. 使用遥控器的横滚摇杆执行以下动作，使机体仅倾斜几度：_左横滚 > 右横滚 > 中心_（整个动作应耗时约3秒）。
   机体应在2次振荡内稳定自身。

4. 重复该动作，每次尝试以更大的幅度倾斜。
   如果机体在约20度的移动范围内2次振荡内稳定自身，可进入下一步。

5. 重复相同的动作但改为俯仰轴。
   如上所述，先从小角度开始，并确认机体在增加倾斜度前能2次振荡内稳定自身。

如果无人机能在2次振荡内稳定自身，即可进入[自动调参流程](#自动调参流程)。

::: warning
如果无人机无法充分稳定自身，请遵循[故障排查](#故障排除)部分的指引。
这些说明提供最小的手动调参步骤，以准备机体进行自动调参。
:::

## 自动调参流程

自动调参流程必须在**安全飞行区域且有足够的空间**内执行。  
该流程耗时约40秒（[19到68秒之间](#how-long-does-autotuning-take)）。  
为了获得最佳效果，建议在风力平和的天气条件下进行测试。

推荐的自动调参模式为<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">[高度模式](../flight_modes_mc/altitude.md)</div><div  style="display: inline;" v-else-if="$frontmatter.frame === 'Plane'">[保持模式](../flight_modes_fw/hold.md)</div>，但也可使用其他飞行模式。  
在自动调参过程中无法使用遥控器摇杆（移动摇杆会停止自动调参操作）。

测试步骤如下：  

1. 执行[调参前测试](#调参前测试)。  

2. 使用遥控器控制起飞<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'>在[高度模式](../flight_modes_mc/altitude.md)下。  
   在安全距离和离地几米处悬停（4到20米之间）。</div><div v-else-if="$frontmatter.frame === 'Plane'">  
   在巡航速度飞行后激活[保持模式](../flight_modes_fw/hold.md)。  
   这将引导飞机以恒定高度和速度环形飞行。</div>  

3. 启用自动调参。  

   <div v-if="$frontmatter.frame === 'Plane'">  
   <div class="tip custom-block"><p class="custom-block-title">TIP</p>  

   如果已配置[启用/禁用自动调参开关](#enable-disable-autotune-switch)，只需将开关切换到"启用"位置即可。  

   </div></div>  

   1. 在QGroundControl中打开菜单 **Vehicle setup > PID Tuning**：  

      ![Tuning Setup > Autotune Enabled](../../assets/qgc/setup/autotune/autotune.png)  

   2. 选择 _Rate Controller_ 或 _Attitude Controller_ 选项卡。  
   3. 确保 **Autotune enabled** 按钮已启用（这将显示 **Autotune** 按钮并移除手动调参选择器）。  
   4. 阅读警告弹窗并点击 **OK** 开始调参。  

4. 飞行器将首先执行快速横滚动作，随后是俯仰和偏航动作。  
   调参进度显示在进度条中，位于 _Autotune_ 按钮旁边。  

<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">  

5. 手动降落并解除武装以应用新调参参数。  
   谨慎起飞并手动测试机体稳定性。  

</div><div v-else-if="$frontmatter.frame === 'Plane'">  

5. 调参将立即/自动在飞行中应用（默认设置）。  
   PX4 将运行4秒测试并在检测到问题时回滚新调参参数。  

</div>  

::: warning  
如果出现强烈振荡，请立即降落并参考下方[故障排除](#故障排除)部分的说明。  
:::  

附加说明：  

<div v-if="$frontmatter.frame === 'Multicopter'">  

- 上述说明在[高度模式](../flight_modes_mc/altitude.md)下调参。  
  如果机体在[起飞模式](../flight_modes_mc/takeoff.md)和[位置模式](../flight_modes_mc/position.md)中已知稳定，也可以在这些模式下起飞并调参。  

</div>  
<div v-else-if="$frontmatter.frame === 'Plane'">  

- 自动调参也可在[高度模式](../flight_modes_fw/altitude.md)或[位置模式](../flight_modes_fw/position.md)中运行。  
  但飞行直线时进行测试需要更大的安全区域，且不会显著改善调参结果。  

</div>  

- 调参是在飞行中应用还是降落后再应用，[可通过参数配置](#apply-tuning-when-in-air-landed)。  

<div v-if="$frontmatter.frame === 'Multicopter'">

## 自适应调节大型机体

对于大型多旋翼机体，可能需要增加阶跃响应的期望上升时间 [MC_AT_RISE_TIME](../advanced_config/parameter_reference.md#MC_AT_RISE_TIME)。  
这需要反复尝试，因为合适的上升时间取决于机体尺寸和旋翼响应。  

需要注意的是，如果预调时出现缓慢振荡，可能意味着姿态环相对于速率环过快。  
此时应按照 [预调测试时无人机发生振荡](#无人机在预调参测试时发生振荡) 中的说明进行排查。  

<!-- 固定翼的上升时间是硬编码的（低于多旋翼，且对于大多数平台来说可能已经足够）。 -->

## 故障排除

### 无人机在预调参测试时发生振荡

<div v-if="$frontmatter.frame === 'Multicopter'">

缓慢振荡（每秒1次或更慢）：这通常出现在大型平台上，表示姿态环相对于速率环过快：

- 以1.0为步长逐步减小 [MC_ROLL_P](../advanced_config/parameter_reference.md#MC_ROLL_P) 和 [MC_PITCH_P](../advanced_config/parameter_reference.md#MC_PITCH_P)。

快速振荡（每秒超过1次）：这是由于速率环增益过高。

- 以0.02为步长逐步减小 `MC_[ROLL|PITCH|YAW]RATE_K`

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

缓慢振荡（每秒1次或更慢）：这通常出现在大型平台上，表示姿态环相对于速率环过快。

- 以0.1为步长逐步增加 [FW_R_TC](../advanced_config/parameter_reference.md#FW_R_TC) 和 [FW_P_TC](../advanced_config/parameter_reference.md#FW_P_TC)。

快速振荡（每秒超过1次）：这是由于速率环增益过高。

- 以0.01为步长逐步减小 [FW_RR_P](../advanced_config/parameter_reference.md#FW_RR_P)、[FW_PR_P](../advanced_config/parameter_reference.md#FW_PR_P)、[FW_YR_P](../advanced_config/parameter_reference.md#FW_YR_P)

</div>

### 自动调参序列失败

如果在自动调参过程中无人机运动幅度不足，系统识别算法可能无法正确识别出系数。

请逐步将 <div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">[MC_AT_SYSID_AMP](../advanced_config/parameter_reference.md#MC_AT_SYSID_AMP)</div><div style="display: inline;" v-else-if="$frontmatter.frame === 'Plane'">[FW_AT_SYSID_AMP](../advanced_config/parameter_reference.md#FW_AT_SYSID_AMP)</div> 参数增加1，并重新触发自动调参。

### 无人机在自动调参后发生振荡

由于数学模型中未包含的效应（如延迟、饱和、变化率、机体柔性），回路增益可能过高。  
要解决此问题，请按照[当无人机在预调参测试中发生振荡时](#无人机在预调参测试时发生振荡)描述的相同步骤操作。

### 我仍然无法使其正常工作

请尝试使用下方[另请参见](#另请参见)中列出的指南进行手动调整。

## 可选配置

### 在飞行中/着陆时应用调参

<div v-if="$frontmatter.frame === 'Multicopter'">

多旋翼需在着陆后应用参数。  
该行为可通过 [MC_AT_APPLY](../advanced_config/parameter_reference.md#MC_AT_APPLY) 参数配置：

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

默认情况下固定翼在飞行中应用参数，随后PX4会运行测试以确认控制器正常工作。  
该行为可通过 [FW_AT_APPLY](../advanced_config/parameter_reference.md#FW_AT_APPLY) 参数配置：

</div>

- `0`: 不应用增益。  
  用于测试目的，如果用户仅想检查自动调参算法的结果而不想直接使用它们。
- `1`: 在解除武装后应用增益（多旋翼默认值）。  
  操作员随后可以谨慎起飞测试新调参。
- `2`: 立即应用（固定翼默认值）。  
  新调参立即生效，扰动信号发送至控制器并在接下来4秒内监控稳定性。  
  如果控制环不稳定，控制增益会立即恢复为原始值。  
  如果测试通过，飞行员可使用新的调参。

<div v-if="$frontmatter.frame === 'Plane'">

### 启用/禁用自动调参开关

通过遥控器开关可配置启用/禁用自动调参功能（在任何模式下）（需使用遥控辅助通道，注意此功能仅支持固定翼机体）。

要映射开关：

1. 在遥控器上选择一个通道用于自动调参启用/禁用开关  
2. 设置 [RC_MAP_AUX1](../advanced_config/parameter_reference.md#RC_MAP_AUX1) 以匹配开关所用的遥控通道（您可以使用任意 `RC_MAP_AUX1` 至 `RC_MAP_AUX6`）  
3. 设置 [FW_AT_MAN_AUX](../advanced_config/parameter_reference.md#FW_AT_MAN_AUX) 为选定的通道（例如若映射了 `RC_MAP_AUX1`，则设置为 `1: Aux 1`）

当开关值低于 `0.5`（在手动控制设定值范围 `[-1, 1]` 内）时自动调参功能将被禁用，当开关通道值高于 `0.5` 时自动调参功能将被启用。

若使用遥控辅助开关启用自动调参，请在飞行前 [选择调参轴](#选择调校轴)

### 选择调校轴

固定翼机体（仅限）可以选择哪些轴参与调校，通过[FW_AT_AXES](../advanced_config/parameter_reference.md#FW_AT_AXES)位掩码参数进行设置：

- bit `0`: 横滚（默认）
- bit `1`: 俯仰（默认）
- bit `2`: 偏航

## 开发者/SDKs

自动调参通过 [MAV_CMD_DO_AUTOTUNE_ENABLE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_AUTOTUNE_ENABLE) 命令启动。  

当前文档撰写时，该消息会定期重发以轮询PX4的进度：`COMMAND_ACK` 包含操作进行中的结果，以及进度百分比。  
当进度达到100%或机体着陆并解除武装时，操作完成。  

::: info  
这并非符合MAVLink标准的[命令协议长时运行指令](https://mavlink.io/en/services/command.html#long_running_commands)实现。  
PX4应流式传输进度，因为协议不允许轮询。  
:::  

该功能尚未被MAVSDK支持。

## 背景/细节

PX4 使用 [PID控制器](../flight_stack/controller_diagrams.md) (角速度、姿态、速度和位置) 来计算将机体从当前估计状态移动到目标设定值所需的输出。控制器必须良好调校，才能发挥机体的最佳性能。特别是，调校不佳的角速度控制器会导致所有模式下飞行稳定性下降，并延长从扰动中恢复的时间。

通常如果你使用的 [框架配置](../config/airframe.md) 与你的机体相似，那么机体将能够飞行。但除非配置与硬件完全匹配，否则应调校角速度和姿态控制器。调校速度和位置控制器的重要性较低，因为它们受机体动力学的影响较小，且相似框架的默认调校配置通常已足够。

Autotuning 提供了一种自动调校角速度和姿态控制器的机制。它可用于调校固定翼、多旋翼以及垂直起降（VTOL）机体（当以多旋翼或固定翼模式飞行时，模式切换需手动调校）。理论上适用于其他具有角速度控制器的机体类型，但目前仅支持上述类型。

自动调校对于PX4支持的多旋翼和固定翼机体配置效果良好，前提是机体结构不过于柔性（详见 [下方信息](#does-autotuning-work-for-all-supported-airframes)）。

机体必须在高度稳定模式下飞行（如 [高度模式](../flight_modes_mc/altitude.md)、[悬停模式](../flight_modes_mc/hold.md) 或 [位置模式](../flight_modes_mc/position.md)）。飞行栈会向机体每个轴施加轻微扰动，然后尝试计算新的调校参数。对于固定翼，新调校参数默认在空中应用，之后机体将测试新设置并在控制器不稳定时恢复原有调校。对于多旋翼，机体着陆并在解除武装后应用新调校参数，飞行员需小心起飞并测试调校效果。

调校过程耗时约40秒（[19到70秒](#how-long-does-autotuning-take)）。默认行为可通过 [参数](#可选配置) 进行配置。

### 常见问题

#### 支持哪些机体类型？

自适应调参功能已启用多旋翼、固定翼以及混合垂直起降固定翼机体。  
尽管该功能尚未对其他机体类型启用，但理论上它可以用于任何使用速率控制器的机体。

#### 自动调参是否适用于所有支持的机身结构？

自动调参所使用的数学模型假设机体是一个轴间无耦合的线性系统（SISO），且复杂度有限（2个极点和2个零点）。如果实际机体与这些条件偏差过大，该模型将无法准确表示机体的真实动力学特性。

在实践中，只要机身结构不过于柔韧，自动调参通常在固定翼和多旋翼机体上表现良好。

#### 自动调参需要多长时间？

每个轴的调参耗时5s-20s（若20s内无法建立调参则中止），轴之间有2s间隔，新增增益在空中的测试耗时4s。

多旋翼必须调节所有三个轴，默认情况下不进行空中增益测试。
因此总耗时在19s (`5 + 2 + 5 + 2 + 5`) 到64s (`20x3 + 2x2`) 之间。

默认情况下，固定翼机体调节所有三个轴后会进行空中增益测试。
因此总耗时在25s (`5 + 2 + 5 + 2 + 5 + 2 + 4`) 到70s (`20x3 + 3x2 + 4`) 之间。

但需要注意上述设置均为默认值。
多旋翼可以选择在空中运行测试，固定翼可以选择不进行测试。
此外，固定翼可以选择调节更少的轴。

通常情况下，无论是哪种机体，耗时大约在40秒左右。

<!--

#### 调参过程中施加的干扰有多剧烈

这可能会在后续添加。我只想引用一个视频。

如果不这样处理，或许可以说"不太剧烈"，但你应该预期机体偏转最大可达20度，因此应能通过默认调参应对这种偏转。

-->

<div v-if="$frontmatter.frame === 'Multicopter'">

## 另请参阅

- [多旋翼PID调校指南](../config_mc/pid_tuning_guide_multicopter_basic.md) (手动/简易)
- [多旋翼PID调校指南](../config_mc/pid_tuning_guide_multicopter.md) (高级/详细)
- [参数 > 自动调校](../advanced_config/parameter_reference.md#autotune) (前缀为 `MC_AT_`).

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

## 另请参见

- [固定翼PID调校指南](../config_fw/pid_tuning_guide_fixedwing.md)
- [参数 > 自动调校](../advanced_config/parameter_reference.md#autotune) (前缀为 `FW_AT_`)