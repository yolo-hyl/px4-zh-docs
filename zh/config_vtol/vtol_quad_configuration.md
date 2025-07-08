# 通用标准垂直起降（QuadPlane）配置与调参

本文是[通用标准垂直起降](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)的配置文档，也称为"QuadPlane VTOL"。  
本质上这是一种通过添加四旋翼电机的固定翼机体。

对于机体特定文档和构建说明请参阅[VTOL 机架构建](../frames_vtol/index.md)。

## 固件与基本设置

1. 运行 _QGroundControl_
2. 为当前版本或主分支(PX4 `main` 分支构建)烧录固件。
3. 在[机架设置](../config/airframe.md)部分选择合适的VTOL机架。

   如果未列出您的机架，请选择[通用标准VTOL](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)机架。

### 飞行/过渡模式开关

您需要在遥控器上分配一个开关用于切换多旋翼和固定翼模式。

::: info
虽然PX4允许无遥控器飞行，但在调试/配置新机架时必须使用遥控器。
:::

这在[飞行模式](../config/flight_mode.md)配置中完成，您将[分配飞行模式及其他功能](../config/flight_mode.md#what-flight-modes-and-switches-should-i-set)到遥控器上的开关。
该开关也可通过参数 [RC_MAP_TRANS_SW](../advanced_config/parameter_reference.md#RC_MAP_TRANS_SW) 进行分配。

开关在关闭位置表示您正在以多旋翼模式飞行。

### 多旋翼/固定翼调参

在尝试首次过渡到固定翼飞行前，您必须确保VTOL在多旋翼模式下调校良好。
原因之一是当过渡出现异常时，您将返回到该模式，且此时机体可能已具有较高速度。
如果调校不当可能导致严重后果。

如果您有跑道且总重量不过高，也建议调校固定翼飞行。
如果没有，则需在切换到固定翼模式时进行此操作。
如果出现异常，您需要准备好（并能够）切换回多旋翼模式。

请遵循相应的调参指南进行多旋翼和固定翼的调校。

### 过渡调参

尽管您可能认为处理的是两种飞行模式（多旋翼用于垂直起降，固定翼用于前飞），但您还需要调校一个额外状态：过渡。

正确完成过渡调参对安全进入固定翼模式至关重要，例如当过渡时空速过低可能导致失速。

#### 过渡油门
参数: [VT_F_TRANS_THR](../advanced_config/parameter_reference.md#VT_F_TRANS_THR)

前过渡油门定义了前过渡期间推杆/拉杆电机的目标油门。

必须将其设置得足够高以确保达到过渡空速。
如果机体配备有空速传感器，可以增加此参数以加快前过渡完成。
首次过渡时建议将值设得比实际需要更高。

#### 前向过渡推杆/拉杆斜率
参数: [VT_PSHER_SLEW](../advanced_config/parameter_reference.md#VT_PSHER_SLEW)

前向过渡指从多旋翼到固定翼模式的转换。
前向过渡推杆/拉杆斜率是将油门提升到目标值所需的时间（由`VT_F_TRANS_THR`定义）。

值为0将立即设置过渡油门值。
默认斜率为0.33，意味着需要3秒才能将油门提升至100%。
如果希望油门提升更平滑，可以降低此值。

请注意，一旦提升周期结束，油门将保持在目标设置，直到过渡速度达到。

#### 空速融合
参数: [VT_ARSP_BLEND](../advanced_config/parameter_reference.md#VT_ARSP_BLEND)

默认情况下，当空速接近过渡速度时，多旋翼姿态控制将逐渐减少，固定翼控制将逐渐增加，直到过渡完成。

将此参数设为0可禁用融合，此时将保持完整的多旋翼控制和零固定翼控制，直到过渡完成。

#### 过渡空速
参数: [VT_ARSP_TRANS](../advanced_config/parameter_reference.md#VT_ARSP_TRANS)

这是触发从多旋翼模式过渡到固定翼模式的空速。
您必须正确校准空速传感器。
同时需要选择一个明显高于机体失速速度（检查`FW_AIRSPD_MIN`）的空速（此检查目前未启用）。

#### 开环过渡时间
参数: [VT_F_TR_OL_TM](../advanced_config/parameter_reference.md#VT_F_TR_OL_TM)

当无空速反馈时（例如未安装空速传感器），此参数定义前过渡的持续时间（秒）。
应将其设置为足以使机体达到足够空速完成过渡的值，例如空速应超过[VT_ARSP_TRANS](../advanced_config/parameter_reference.md#VT_ARSP_TRANS)。

#### 过渡超时
[VT_TRANS_TIMEOUT](../advanced_config/parameter_reference.md#VT_TRANS_TIMEOUT)

此参数定义前过渡持续时间的上限。
当过渡时间超过此值时，将触发Quadchute事件。

::: note
如果在开环过渡期间检测到空速传感器，系统将切换到闭环控制。
:::

#### 中断过渡

了解在过渡过程中撤销过渡指令时的预期行为非常重要。

当从**多旋翼向固定翼**过渡（开关在开启/固定翼位置）时，在过渡完成前将开关切回（关闭/多旋翼位置）将立即返回多旋翼模式。

当从**固定翼向多旋翼**过渡时，对于这种类型的VTOL，切换是即时的，因此没有回退选项（与倾转旋翼VTOL不同）。
如果需要返回固定翼模式，必须执行完整过渡。
如果机体仍以较高速度飞行，此过程将快速完成。

### 支持

如果您有关于VTOL改装或配置的任何问题，请参见[discuss.px4.io/c/px4/vtol](https://discuss.px4.io/c/px4/vtol)。