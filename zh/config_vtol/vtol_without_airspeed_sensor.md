# 无空速传感器的VTOL

<Badge type="warning" text="实验性" />

:::warning
不带空速传感器的VTOL支持功能为实验性功能，建议仅由有经验的飞行员尝试使用。
建议使用空速传感器。
:::

固定翼机体使用[空速传感器](../sensor/airspeed.md)来确定飞机在空气中移动的速度。
由于风速影响，该速度可能与地速不同。
每架飞机都有最低空速阈值，低于该值时飞机将失速。
在风力较小时，若设置值显著高于失速速度，VTOL可不使用空速传感器运行。

本指南将说明在VTOL飞行器上绕过空速传感器所需的参数设置。

::: info
此处描述的大多数设置同样适用于非VTOL的固定翼机体，但目前尚未经过测试。
转向前的转向和四旋翼降落伞功能为VTOL专用。
:::

## 准备工作

在尝试移除空速传感器前，您需要首先确定安全油门水平。
同时需要了解向前转换所需的时间。
为此可通过以下两种方式完成参考飞行：
1. 使用空速传感器进行参考飞行
2. 手动飞行机体
两种情况下参考飞行都应在极低风速条件下进行。

飞行应以足够应对强风条件的速度进行，包括以下内容：
- 成功的向前转换
- 直线平飞
- 急转弯
- 快速爬升至更高高度

## 日志分析

完成参考飞行后，下载日志并使用[FlightPlot](../log/flight_log_analysis.md#flightplot)（或其他分析工具）分析日志。
绘制以下参数：
- 高度（`GPOS.Alt`）
- 推力（`ATC1.Thrust`）
- 地速（公式：`sqrt(GPS.VelN\^2 + GPS.VelE\^2)`）
- 俯仰角（`ATT.Pitch`）
- 滚转角（`AT.Roll`）

观察机体平飞（无或轻微俯仰和滚转）、爬升（高度增加）以及银行（更多滚转）时的油门水平（推力）。
初始巡航速度值应取滚转或爬升期间的最大推力值，平飞推力值可作为最小值参考（若后续需要调低速度）。

同时记录向前转换完成所需的时间。
这将用于设置最小转换时间。
出于安全考虑，建议在此时间基础上增加±30%。

最后记录巡航飞行时的地速。
这可用于在首次无空速传感器飞行后调整油门设置。

## 参数设置

要绕过空速预飞检查，需将[SYS_HAS_NUM_ASPD](../advanced_config/parameter_reference.md#SYS_HAS_NUM_ASPD)设为0。

若已安装空速传感器但不想用于反馈控制，需将[FW_USE_AIRSPD](../advanced_config/parameter_reference.md#FW_USE_AIRSPD)设为`False`。
这允许您在保留真实空速读数（用于检查失速安全余量等）的同时测试无空速传感器状态下的系统行为。

将油门调平值([FW_THR_TRIM](../advanced_config/parameter_reference.md#FW_THR_TRIM))设置为参考飞行日志中确定的百分比。
注意QGC将其缩放为1到100，而日志中的推力值范围为0到1。
因此0.65的推力值应输入为65。
出于安全考虑，建议在首次飞行测试时将确定值增加±10%油门。

将最小向前转换时间([VT_TRANS_MIN_TM](../advanced_config/parameter_reference.md#VT_TRANS_MIN_TM))设置为参考飞行中确定的秒数，并增加±30%作为安全余量。

### 可选推荐参数

由于失速风险真实存在，建议设置"固定翼最低高度"（即"四旋翼降落伞"）阈值([VT_FW_MIN_ALT](../advanced_config/parameter_reference.md#VT_FW_MIN_ALT))。

这会使VTOL在低于特定高度时切换回多旋翼模式并启动[Return模式](../flight_modes_vtol/return.md)。
建议设置为15或20米，为多旋翼提供从失速中恢复的时间。

该模式下测试的位置估计算法是EKF2，默认已启用（更多信息请参见[Switching State Estimators](../advanced/switching_state_estimators.md#how-to-enable-different-estimators)和[EKF2_EN ](../advanced_config/parameter_reference.md#EKF2_EN)）。

## 无空速传感器的首次飞行

这些值适用于位置控制飞行（如[Hold模式](../flight_modes_fw/hold.md)或[Mission模式](../flight_modes_vtol/mission.md)）。
因此建议在安全高度配置任务，大约比四旋翼降落伞阈值高10米。

与参考飞行相同，该飞行应在极低风速条件下进行。
首次飞行建议：
- 保持同一高度
- 设置足够宽的航点，避免需要急转弯
- 任务规模应足够小，以便在需要手动接管时仍能保持目视
- 若空速过高，可考虑切换到高度模式进行手动后转换

若任务成功完成，应检查日志以下内容：
- 地速应明显高于参考飞行的地速
- 高度不应显著低于参考飞行
- 俯仰角不应持续明显不同于参考飞行

若所有条件都满足，可逐步调低巡航油门，直至地速与参考飞行匹配。

## 参数概览

相关参数包括：
- [FW_USE_AIRSPD](../advanced_config/parameter_reference.md#FW_USE_AIRSPD)
- [SYS_HAS_NUM_ASPD](../advanced_config/parameter_reference.md#SYS_HAS_NUM_ASPD)
- [EKF2_EN](../advanced_config/parameter_reference.md#EKF2_EN) (1), [ATT_EN](../advanced_config/parameter_reference.md#ATT_EN) (0), [LPE_EN](../advanced_config/parameter_reference.md#LPE_EN) (0)
- [FW_THR_TRIM](../advanced_config/parameter_reference.md#FW_THR_TRIM)：已确定（例如70%）
- [VT_TRANS_MIN_TM](../advanced_config/parameter_reference.md#VT_TRANS_MIN_TM)：已确定（例如10秒）
- [VT_FW_MIN_ALT](../advanced_config/parameter_reference.md#VT_FW_MIN_ALT)：15