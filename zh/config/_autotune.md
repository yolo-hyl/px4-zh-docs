<!-- 本文档是 autotune_mc.md 和 autotune_fy.md 中使用的自动调参功能的单一来源
撰写时，仅有固定翼、多旋翼和垂直起降飞行器支持自动调参功能
垂直起降飞行器有其独立文档并引用其他两种文档
-->

<div v-if="$frontmatter.frame === 'Multicopter'">

# 自动调参 (多旋翼)

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

# 自动调参（固定翼）

</div>

自动调参功能可以自动完成PX4角速度和姿态控制器的调参过程，这些控制器对飞行的稳定性和响应性至关重要（其他调参属于可选操作）。

调参只需执行一次，除非您使用的是制造商已调参完成的机体（且未进行过任何修改），否则建议进行自动调参。

:::warning
自动调参在飞行中进行。
机体必须具备足够的飞行能力以应对中等扰动，并需密切注意：

- 测试机体是否[达到自动调参所需的稳定性](#pre-tuning-test)。
- 做好随时中止自动调参的准备。
  可通过切换飞行模式<div style="display: inline;" v-if="$frontmatter.frame === 'Plane'">或使用自动调参启用/禁用开关（[如果已配置](#enable-disable-autotune-switch)）</div>来中止。
- 调参后需验证机体飞行性能。

:::

<lite-youtube videoid="5xswOhhqrIQ" title="QGroundControl 自动调参功能解析用于 PX4 自动驾驶仪"/>

## 调参前测试

在运行自动调参前，机体必须能够飞行并适当稳定自身。
此测试确保机体能够在位置控制模式下安全飞行。

为确保机体稳定度满足自动调参要求：

1. 执行常规的飞行前安全检查清单，确保飞行区域空旷且空间充足。

2. 起飞并<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">在[高度模式](../flight_modes_mc/altitude.md)或[稳定模式](../flight_modes_mc/manual_stabilized.md)下悬停在离地1米处</div><div style="display: inline;" v-else-if="$frontmatter.frame === 'Plane'">以巡航速度在[位置模式](../flight_modes_fw/position.md)或[高度模式](../flight_modes_fw/altitude.md)下飞行</div>。

3. 使用遥控器的横滚杆执行以下动作，仅倾斜几度：_向左滚转 > 向右滚转 > 回中_（整个动作应持续约3秒）。
   机体应在2次振荡内稳定自身。

4. 重复该动作，每次尝试增加倾斜幅度。
   如果机体在约20度倾斜时能在2次振荡内稳定自身，则进入下一步。

5. 重复相同的动作，但作用于俯仰轴。
   如前所述，从小角度开始，并在增加倾斜角度前确认机体能在2次振荡内稳定自身。

如果无人机能在2次振荡内稳定自身，则可进行[自动调参流程](#auto-tuning-procedure)。

::: warning
如果无人机无法充分稳定自身，请遵循[故障排查](#troubleshooting)部分的说明。
这些说明解释了为自动调参做准备的最小手动调参步骤。
:::

## 自动调参流程

自动调参序列必须在**安全飞行区域（并留有足够空间）**中进行。  
该过程耗时约40秒（[19到68秒之间](#how-long-does-autotuning-take)）。  
为获得最佳效果，建议在平静的天气条件下进行测试。

推荐的自动调参模式是<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">[高度模式](../flight_modes_mc/altitude.md)</div><div  style="display: inline;" v-else-if="$frontmatter.frame === 'Plane'">[保持模式](../flight_modes_fw/hold.md)</div>，但也可以使用其他飞行模式。  
在自动调参期间无法使用遥控器摇杆（移动摇杆会停止自动调参操作）。

测试步骤如下：

1. 执行[调参前测试](#pre-tuning-test)。

2. 使用遥控器控制起飞<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">，在[高度模式](../flight_modes_mc/altitude.md)下操作。  
   在安全距离和离地面几米高处悬停机体（4到20米之间）。</div><div v-else-if="$frontmatter.frame === 'Plane'">  
   达到巡航速度后激活[保持模式](../flight_modes_fw/hold.md)。  
   这将引导飞行器在恒定高度和速度下画圆飞行。</div>

3. 启用自动调参。

   <div v-if="$frontmatter.frame === 'Plane'">
   <div class="tip custom-block"><p class="custom-block-title">TIP</p>

   如果已配置[启用/禁用自动调参开关](#enable-disable-autotune-switch)，只需将开关切换至"启用"位置即可。

   </div></div>

   1. 在QGroundControl中打开菜单**机体设置 > PID调参**:

      ![调参设置 > 自动调参启用](../../assets/qgc/setup/autotune/autotune.png)

   2. 选择"Rate Controller"或"Attitude Controller"标签页。
   3. 确保**自动调参启用**按钮处于启用状态（这将显示**自动调参**按钮并移除手动调参选择器）。
   4. 阅读警告弹窗并点击**OK**开始调参。

4. 机体将首先快速进行横滚运动，随后进行俯仰和偏航运动。  
   调参进度在进度条中显示，位于**自动调参**按钮旁边。

<div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">

5. 手动降落并解除武装以应用新的调参参数。  
   谨慎起飞并手动测试机体是否稳定。

</div><div v-else-if="$frontmatter.frame === 'Plane'">

5. 调参将立即/自动在飞行中应用并测试（默认设置）。  
   PX4随后会运行4秒测试，如果检测到问题则会恢复原始调参。

</div>

::: warning
如果出现任何强烈振荡，请立即降落并按照下方[故障排除](#troubleshooting)中的说明操作。
:::

附加说明：

<div v-if="$frontmatter.frame === 'Multicopter'">

- 上述说明在[高度模式](../flight_modes_mc/altitude.md)下调参机体。  
  如果机体在[起飞模式](../flight_modes_mc/takeoff.md)和[位置模式](../flight_modes_mc/position.md)中已知稳定，也可以在这些模式下起飞并调参。

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

- 自动调参也可以在[高度模式](../flight_modes_fw/altitude.md)或[位置模式](../flight_modes_fw/position.md)中运行。  
  然而，直线飞行时进行测试需要更大的安全区域，且不会显著提升调参效果。

</div>

- 调参是在飞行中还是着陆后应用，可以通过[参数配置](#apply-tuning-when-in-air-landed)进行设置。

<div v-if="$frontmatter.frame === 'Multicopter'">## 大型机体的自动调参

对于大型多旋翼机体，您可能需要增加阶跃响应的期望上升时间 [MC_AT_RISE_TIME](../advanced_config/parameter_reference.md#MC_AT_RISE_TIME)。  
这需要通过一些试验和调整，因为合适的上升时间取决于机体尺寸和旋翼响应。

注意，在预调参过程中如果出现缓慢振荡，可能意味着姿态环相对于速率环过快。  
在这种情况下，应按照 [Drone oscillates when performing the pre-tuning test](#drone-oscillates-when-performing-the-pre-tuning-test) 中描述的方法进行排查。

<!-- 固定翼的上升时间是硬编码的（其值低于多旋翼，且对大多数平台来说可能已足够）。 -->

</div>

## 故障排除

### 无人机在预调谐测试中出现振荡

<div v-if="$frontmatter.frame === 'Multicopter'">

慢速振荡（每秒1次或更慢）：这通常出现在大型平台上，表示姿态环相比速率环过快：

- 按步长1.0降低 [MC_ROLL_P](../advanced_config/parameter_reference.md#MC_ROLL_P) 和 [MC_PITCH_P](../advanced_config/parameter_reference.md#MC_PITCH_P)。

快速振荡（每秒超过1次）：这是因为速率环增益过高。

- 按步长0.02降低 `MC_[ROLL|PITCH|YAW]RATE_K`

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

慢速振荡（每秒1次或更慢）：这通常出现在大型平台上，表示姿态环相比速率环过快。

- 按步长0.1增加 [FW_R_TC](../advanced_config/parameter_reference.md#FW_R_TC) 和 [FW_P_TC](../advanced_config/parameter_reference.md#FW_P_TC)。

快速振荡（每秒超过1次）：这是因为速率环增益过高。

- 按步长0.01降低 [FW_RR_P](../advanced_config/parameter_reference.md#FW_RR_P), [FW_PR_P](../advanced_config/parameter_reference.md#FW_PR_P), [FW_YR_P](../advanced_config/parameter_reference.md#FW_YR_P)

</div>

### 自动调谐序列失败

如果自动调谐过程中无人机运动不足，系统识别算法可能无法找到正确的系数。

按步长1增加 <div style="display: inline;" v-if="$frontmatter.frame === 'Multicopter'">[MC_AT_SYSID_AMP](../advanced_config/parameter_reference.md#MC_AT_SYSID_AMP)</div><div style="display: inline;" v-else-if="$frontmatter.frame === 'Plane'">[FW_AT_SYSID_AMP](../advanced_config/parameter_reference.md#FW_AT_SYSID_AMP)</div> 参数，并再次触发自动调谐。

### 自动调谐后无人机出现振荡

由于数学模型未包含的延迟、饱和、速率限制、机体柔性等因素，环路增益可能过高。
修复方法：遵循[预调谐测试中无人机振荡](#drone-oscillates-when-performing-the-pre-tuning-test)部分中描述的相同步骤。

### 我仍然无法使其正常工作

尝试使用下方[见其他](#see-also)中列出的指南进行手动调谐。

## 可选配置

### 飞行中/着陆时应用调参

<div v-if="$frontmatter.frame === 'Multicopter'">

多旋翼机体在参数应用前会先着陆。  
此行为可通过参数 [MC_AT_APPLY](../advanced_config/parameter_reference.md#MC_AT_APPLY) 配置：

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

默认情况下固定翼机体在飞行中应用调参参数，随后 PX4 会运行测试以确认控制器正常工作。  
此行为可通过参数 [FW_AT_APPLY](../advanced_config/parameter_reference.md#FW_AT_APPLY) 配置：

</div>

- `0`: 增益不会被应用。  
  如果用户希望检查自动调参算法的结果但不直接使用，可将此用作测试用途。
- `1`: 在解除武装后应用增益（多旋翼默认值）。  
  操作员随后可以在谨慎起飞时测试新调参。
- `2`: 立即应用（固定翼默认值）。  
  新调参会被应用，干扰信号会发送到控制器，并在接下来的4秒内监控稳定性。  
  如果控制环不稳定，控制增益会立即恢复为先前值。  
  如果测试通过，飞行员可以使用新调参。

<div v-if="$frontmatter.frame === 'Plane'">

### 启用/禁用自动调参开关

可通过遥控器辅助通道配置开关以启用/禁用自动调参（任何模式下均支持，注意此功能仅支持固定翼机体）。

配置开关的步骤：  
1. 在控制器上选择一个遥控通道用于自动调参的启用/禁用开关。  
2. 设置 [RC_MAP_AUX1](../advanced_config/parameter_reference.md#RC_MAP_AUX1) 与开关所用的遥控通道匹配（可使用任意 `RC_MAP_AUX1` 到 `RC_MAP_AUX6`）。  
3. 设置 [FW_AT_MAN_AUX](../advanced_config/parameter_reference.md#FW_AT_MAN_AUX) 为所选通道（例如若映射了 `RC_MAP_AUX1`，则设置为 `1: Aux 1`）。

当开关值低于 `0.5`（在手动控制设定范围 `[-1, 1]` 内）时，自动调参将被禁用；当开关值高于 `0.5` 时启用。

如果使用遥控器辅助开关启用自动调参，请在飞行前 [选择调参轴](#select-tuning-axis)。

### 选择调参轴

固定翼机体（仅支持）可通过 [FW_AT_AXES](../advanced_config/parameter_reference.md#FW_AT_AXES) 位掩码参数选择要调参的轴：

- bit `0`: 横滚轴（默认）  
- bit `1`: 俯仰轴（默认）  
- bit `2`: 偏航轴## 开发者/SDKs

</div>

## Developers/SDKs

通过 [MAV_CMD_DO_AUTOTUNE_ENABLE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_AUTOTUNE_ENABLE) MAVLink 命令启动自整定。

撰写时，该消息会定期重新发送以查询 PX4 的进度：`COMMAND_ACK` 包含操作正在进行的结果，并以百分比形式显示进度。
当进度达到 100% 或机体着陆并解除武装时，操作完成。

::: info
这不是 [command protocol long running command](https://mavlink.io/en/services/command.html#long_running_commands) 的 MAVLink 兼容实现。
PX4 应该主动流式传输进度，因为协议不允许轮询。
:::

该功能尚未由 MAVSDK 支持。

## 背景/细节

PX4 使用 [PID控制器](../flight_stack/controller_diagrams.md)（速率、姿态、速度和位置）来计算将机体从当前估计状态移动到目标设定值所需的输出。控制器必须经过良好调参才能发挥机体的最佳性能。特别是，调参不当的速率控制器会导致所有飞行模式下飞行稳定性下降，并且从干扰中恢复所需时间更长。

通常情况下，如果你使用与机体相似的[机型配置](../config/airframe.md)，机体将能够飞行。但除非配置与你的硬件完全匹配，否则应调参速率和姿态控制器。速度和位置控制器的调参相对不那么重要，因为它们受机体动力学的影响较小，且相似机型的默认调参配置通常就足够。

自动调参提供了一种自动调参速率和姿态控制器的机制。它可用于调参多旋翼、固定翼和垂直起降（VTOL）机体，当VTOL以多旋翼或固定翼模式飞行时（模式转换需手动调参）。理论上它适用于其他具有速率控制器的机体类型，但目前仅支持上述类型。

自动调参在PX4支持的多旋翼和固定翼机体配置中效果良好，前提是机体结构不过于柔性（[更多信息见下文](#does-autotuning-work-for-all-supported-airframes)）。

机体必须以高度稳定模式飞行（如[高度模式](../flight_modes_mc/altitude.md)、[悬停模式](../flight_modes_mc/hold.md)或[位置模式](../flight_modes_mc/position.md)）。飞控栈会在每个轴施加小扰动，然后尝试计算新的调参参数。对于固定翼，默认在空中应用新调参，随后机体测试新设置并在控制器不稳定时恢复调参。对于多旋翼，在解除武装后落地并应用新调参参数，飞行员需谨慎起飞并测试调参效果。

调参过程耗时约40秒（[19到70秒之间](#how-long-does-autotuning-take)）。默认行为可通过[参数](#optional-configuration)进行配置。

### 常见问题

#### 支持哪些机型类型？

自动调参支持多旋翼、固定翼和混合式VTOL固定翼机体。虽然目前未对其他机型类型启用，但理论上可用于任何使用速率控制器的机型。

#### 自动调参适用于所有支持的机型吗？

自动调参使用的数学模型假设机体是一个线性系统，轴间无耦合（SISO），且复杂度有限（2个极点和2个零点）。如果真实机体过于偏离这些条件，模型将无法准确表示机体的动力学特性。

实际上，只要结构不过于柔性，自动调参通常对固定翼和多旋翼效果良好。

#### 自动调参需要多长时间？

每个轴调参耗时5-20秒（若20秒内未完成则中止）+ 每轴间2秒间隔 + 若在空中应用新增益则4秒测试时间。多旋翼需调参所有三个轴，默认不进行空中测试，因此耗时范围为19秒(`5 + 2 + 5 + 2 + 5`)到64秒(`20x3 + 2x2`)。固定翼默认调参所有三个轴并进行空中测试，耗时范围为25秒(`5 + 2 + 5 + 2 + 5 + 2 + 4`)到70秒(`20x3 + 3x2 + 4`)。需要注意的是，这些是默认设置，多旋翼可选择进行空中测试，固定翼可选择不测试或减少调参轴数。实际中通常耗时约40秒。

<!-- 
#### 调参施加的扰动有多大

这可能会在后续添加。我想直接指向一个视频。

如果未添加，或许可以说"扰动不大"，但应预期机体可能偏转最多20度，因此默认调参应能应对这种偏转。

-->

<div v-if="$frontmatter.frame === 'Multicopter'">

## 另请参见

- [多旋翼PID调参指南](../config_mc/pid_tuning_guide_multicopter_basic.md) (手动/简易)
- [多旋翼PID调参指南](../config_mc/pid_tuning_guide_multicopter.md) (高级/详细)
- [参数 > 自动调参](../advanced_config/parameter_reference.md#autotune) (前缀为 `MC_AT_`)

</div>
<div v-else-if="$frontmatter.frame === 'Plane'">

## 参见

- [固定翼PID调参指南](../config_fw/pid_tuning_guide_fixedwing.md)
- [参数 > 自动调参](../advanced_config/parameter_reference.md#autotune) (`FW_AT_`前缀)

</div>