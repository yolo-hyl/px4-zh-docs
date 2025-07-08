# Acro模式（固定翼）

<img src="../../assets/site/difficulty_hard.png" title="Hard to fly" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="Manual/Remote control required" width="30px" />&nbsp;

_Acro模式_ 是用于执行特技动作的手动模式，例如滚转、翻转、失速和特技飞行动作。

横滚、俯仰和偏航摇杆控制机体绕相应轴的旋转角速度，油门信号直接传递给控制分配。
当摇杆处于中立位时，机体将停止旋转，但保持当前姿态（侧飞、倒飞等）并根据当前动量继续运动。

![FW Manual Acrobatic Flight](../../assets/flight_modes/acrobatic_fw.png)

## 技术描述

用于执行特技动作的手动模式，例如滚转、翻转、失速和特技飞行动作。

横滚-俯仰-偏航摇杆输入转换为角速度指令，由自动驾驶仪进行稳定。
油门信号直接传递给控制分配。

## 参数

| 参数                                                                                             | 描述                                                                                                                                              |
| ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="FW_ACRO_X_MAX"></a>[FW_ACRO_X_MAX](../advanced_config/parameter_reference.md#FW_ACRO_X_MAX)    | Acro机体x轴最大角速度（在Acro模式下，当用户施加满舵横滚输入时控制器试图达到的机体x轴角速度）。默认值：90度。  |
| <a id="FW_ACRO_Y_MAX"></a>[FW_ACRO_Y_MAX](../advanced_config/parameter_reference.md#FW_ACRO_Y_MAX)    | Acro机体y轴最大角速度（在Acro模式下，当用户施加满舵俯仰输入时控制器试图达到的机体y轴角速度）。默认值：90度。 |
| <a id="FW_ACRO_Z_MAX"></a>[FW_ACRO_Z_MAX](../advanced_config/parameter_reference.md#FW_ACRO_Z_MAX)    | Acro机体z轴最大角速度（在Acro模式下，当用户施加满舵偏航输入时控制器试图达到的机体z轴角速度）。默认值：45度。   |
| <a id="FW_ACRO_YAW_EN"></a>[FW_ACRO_YAW_EN](../advanced_config/parameter_reference.md#FW_ACRO_YAW_EN) | 启用偏航角速度控制器（若禁用则飞行员直接控制偏航执行器）。 `0`: 禁用（默认），`1`: 启用。                            |