# 固定翼调整指南

调整用于在特定条件下（相对空速、空气密度、攻角、机体构型等）校准控制面。  
在理想调整条件下飞行的机体将无需飞行员或稳定计算机的控制输入即可保持姿态。

通用航空、商用飞机及大型无人机通过[调整片](https://en.wikipedia.org/wiki/Trim_tab)校准控制面，而小型无人机则直接对控制面执行器增加偏移量。

[基础调整](#基础调整)章节将解释每个调整参数的作用及如何获取正确值。  
[高级调整](#高级调整)章节将介绍可根据测量空速和襟翼位置自动调整的参数。

## 基础调整

操作员可能需要使用以下参数来正确调整固定翼机体。  
参数及其使用场景概述如下：

- [RCx_TRIM](../advanced_config/parameter_reference.md#RC1_TRIM) 对接收的遥控器信号应用调整。  
  这些参数在[遥控器校准](../config/radio.md)期间会自动设置。
- [CA_SV_CSx_TRIM](../advanced_config/parameter_reference.md#CA_SV_CS0_TRIM) 对控制面通道应用调整。  
  用于飞行前精细对齐控制面到默认角度。
- [FW_PSP_OFF](../advanced_config/parameter_reference.md#FW_PSP_OFF) 对俯仰设定值应用偏移。  
  用于设置机体在巡航速度下所需的攻角。
- [FW_AIRSPD_TRIM](../advanced_config/parameter_reference.md#FW_AIRSPD_TRIM) 由速率控制器使用，根据测量空速缩放输出。  
  详见[Airspeed Scaling](../flight_stack/controller_diagrams.md#airspeed-scaling)。
- [TRIM_ROLL](../advanced_config/parameter_reference.md#TRIM_ROLL)、[TRIM_PITCH](../advanced_config/parameter_reference.md#TRIM_PITCH) 和 [TRIM_YAW](../advanced_config/parameter_reference.md#TRIM_YAW) 在混控前对控制信号应用调整。  
  例如，如果升降舵有双舵机，`TRIM_PITCH` 会同时调整这两个舵机。  
  当控制面已对齐但机体在手动（非稳定）飞行时存在俯仰/滚转/偏航或稳定飞行时控制信号存在恒定偏移时，需要设置这些参数。

设置上述参数的正确顺序为：

1. 通过物理调整连杆长度对舵机进行调整，并在测试台上通过调整PWM通道（使用 `PWM_MAIN/AUX_TRIMx`）精细校准控制面到理论位置。
1. 以稳定模式在巡航速度飞行，设置俯仰设定值偏移（`FW_PSP_OFF`）到所需攻角。  
   巡航速度下所需的攻角对应机体在机翼水平飞行时维持恒定高度所需的俯仰角。  
   如果使用空速传感器，还需设置正确的巡航空速（`FW_AIRSPD_TRIM`）。
1. 查看日志文件中的执行器控制（上传到 [Flight Review](https://logs.px4.io) 并查看 _Actuator Controls_ 图表），设置俯仰调整（`TRIM_PITCH`）。  
   将此值设置为机翼水平飞行时俯仰信号的平均偏移量。

如果不想查看日志，或习惯手动飞行，步骤3可先于步骤2执行。  
您可以通过遥控器（使用调整开关）进行调整，并将值记录到 `TRIM_PITCH`（随后移除遥控器调整）或通过遥测和QGC在飞行中直接更新 `TRIM_PITCH`。

## 高级调整

由于非对称机翼产生的下俯力矩会随空速增加及襟翼展开而增大，机体需根据当前测量空速和襟翼位置重新调整。  
为此，可通过以下参数定义空速的双线性曲线函数和襟翼状态的俯仰调整增量函数（见下图）：

- [FW_DTRIM\_\[R/P/Y\]\_\[VMIN/VMAX\]](../advanced_config/parameter_reference.md#FW_DTRIM_R_VMIN) 是在最小/最大空速（由 [FW_AIRSPD_MIN](../advanced_config/parameter_reference.md#FW_AIRSPD_MIN) 和 [FW_AIRSPD_MAX](../advanced_config/parameter_reference.md#FW_AIRSPD_MAX) 定义）下添加到 `TRIM_ROLL/PITCH/YAW` 的滚转/俯仰/偏航调整值。
- [CA_SV_CSx_FLAP](../advanced_config/parameter_reference.md#CA_SV_CS0_FLAP) 和 [CA_SV_CSx_SPOIL](../advanced_config/parameter_reference.md#CA_SV_CS0_SPOIL) 分别是襟翼和扰流板完全展开时应用到这些控制面的调整值。

![Dtrim 曲线](../../assets/config/fw/fixedwing_dtrim.png)

<!-- 图表源自 draw.io: https://drive.google.com/file/d/15AbscUF1kRdWMh8ONcCRu6QBwGbqVGfl/view?usp=sharing  
请求开发团队访问权限。 -->

完全对称的机体仅需俯仰调整增量，但由于实际机体从不完全对称，有时也需要滚转和偏航调整增量。