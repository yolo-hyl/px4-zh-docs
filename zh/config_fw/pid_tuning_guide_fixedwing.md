# 固定翼速率/姿态控制器调校指南

本指南解释了如何手动调校固定翼PID环路。  
该指南面向高级用户/专家，错误的PID调校可能导致飞行器坠毁。

::: info  
[自动调校](../config/autotune_fw.md) 推荐给大多数用户，因为它更快捷、更容易且能提供大多数机体的良好调校。  
手动调校推荐用于自动调校无法工作的机体，或需要精细调校的情况。  
:::

## 先决条件

- 必须首先配置好配平（在PID调校之前）。  
  《[固定翼配平指南](../config_fw/trimming_guide_fixedwing.md)》解释了具体方法。  
- 调校过程中设置错误的增益可能导致姿态控制不稳定。  
  因此，进行增益调校的飞行员应能够使用[手动](../flight_modes_fw/manual.md)（覆盖）控制模式飞行和着陆。  
- 过大的增益（和快速舵面运动）可能超出机体的最大承受力 - 请谨慎增加增益。  
- 滚转和俯仰的调校遵循相同步骤。  
  唯一的区别是俯仰对配平偏移更敏感，因此[配平](../config_fw/trimming_guide_fixedwing.md)必须谨慎进行，积分增益需要更多关注以补偿这一问题。

## 建立机体基准

如果可用，建议由能进行手动飞行的飞行员通过手动试飞建立几个核心系统属性。  
为此，执行以下机动动作。  
即使无法立即在纸上记录所有数值，日志文件在后续调校中会非常有用。

::: info  
所有这些数值都会被自动记录。  
你只需在不想查看日志文件而直接进行调校时做笔记。  

- 以合适的空速平飞。  
  记录油门杆位置和空速（示例：70% → 0.7油门，15 m/s空速）。  
- 以最大油门和足够空速爬升10-30秒（示例：12 m/s空速，30秒内爬升100米）。  
- 以零油门和合理空速下降10-30秒（示例：18 m/s空速，30秒内下降80米）。  
- 向右满滚转直到60度滚转角，然后向左满滚转直到反向60度。  
- 向上俯仰45度，向下俯仰45度。  
:::  

本指南将使用这些数值来设置后续的控制器增益。

## 调校滚转

先调校滚转轴，再调校俯仰。  
滚转轴更安全，因为错误调校只会导致运动，而不会造成高度损失。

### 调整前馈增益

要调校此增益，首先将其他增益设置为最小值（通常为0.005，但请参阅参数文档）。

#### 需设置为最小值的增益

- [FW_RR_I](../advanced_config/parameter_reference.md#FW_RR_I)  
- [FW_RR_P](../advanced_config/parameter_reference.md#FW_RR_P)  

#### 需调校的增益

- [FW_RR_FF](../advanced_config/parameter_reference.md#FW_RR_FF) - 从0.4开始。  
  每次将值翻倍，直到飞行器滚转满足要求并达到设定值。  
  调校结束时将增益降低20%。

### 调整速率增益

- [FW_RR_P](../advanced_config/parameter_reference.md#FW_RR_P) - 从0.06开始。  
  每次将值翻倍，直到系统开始出现抖动/颤动。  
  然后将增益降低50%。

### 使用积分增益调整配平偏移

- [FW_RR_I](../advanced_config/parameter_reference.md#FW_RR_I) - 从0.01开始。  
  每次将值翻倍，直到命令值与实际滚转值之间没有偏移（这通常需要查看日志文件）。

## 调校俯仰

俯仰轴可能需要更多的积分增益和正确设置的俯仰偏移。

### 调整前馈增益

要调校此增益，将其他增益设置为最小值。

#### 需设置为最小值的增益

- [FW_PR_I](../advanced_config/parameter_reference.md#FW_PR_I)  
- [FW_PR_P](../advanced_config/parameter_reference.md#FW_PR_I)  

#### 需调校的增益

- [FW_PR_FF](../advanced_config/parameter_reference.md#FW_PR_FF) - 从0.4开始。  
  每次将值翻倍，直到飞行器俯仰满足要求并达到设定值。  
  调校结束时将增益降低20%。

### 调整速率增益

- [FW_PR_P](../advanced_config/parameter_reference.md#FW_PR_P) - 从0.04开始。  
  每次将值翻倍，直到系统开始出现抖动/颤动。  
  然后将值降低50%。

### 使用积分增益调整配平偏移

- [FW_PR_I](../advanced_config/parameter_reference.md#FW_PR_I) - 从0.01开始。  
  每次将值翻倍，直到命令值与实际俯仰值之间没有偏移（这通常需要查看日志文件）。

## 调整外环的时间常数

控制环的整体软硬程度可通过时间常数进行调整。  
0.5秒的默认值适用于大多数固定翼配置，通常无需调整。

- [FW_P_TC](../advanced_config/parameter_reference.md#FW_P_TC) - 默认设置为0.5秒，增大使俯仰响应更柔和，减小使响应更硬朗。  
- [FW_R_TC](../advanced_config/parameter_reference.md#FW_R_TC) - 默认设置为0.5秒，增大使滚转响应更柔和，减小使响应更硬朗。

## 其他调校参数

本指南涵盖了最重要的参数。  
其他调校参数请参阅《[参数参考](../advanced_config/parameter_reference.md)》。