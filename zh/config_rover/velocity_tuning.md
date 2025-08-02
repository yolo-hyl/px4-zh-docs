# 速度调参

:::warning
在进行本步骤之前，必须已经完成[姿态调参](attitude_tuning.md)！
:::

::: info
调参时我们将使用手动[Position mode](../flight_modes_rover/manual.md#position-mode)模式。
该模式需要全局定位估算（GPS）以及对部分超出速度控制器范围的参数进行调参。
如果你使用的是自定义的外部飞行模式（控制速度但不依赖全局定位估算），可以忽略[手动位置模式参数](#手动位置模式参数)。
:::

## 速度参数

要调整速度控制器，请在 QGroundControl 中配置以下 [参数](../advanced_config/parameters.md)：

1. [RO_SPEED_LIM](#RO_SPEED_LIM) [m/s]: 这是您希望为车辆允许的最大速度。这将定义 [Position 模式](../flight_modes_rover/manual.md#position-mode) 中的摇杆到速度映射，并设置速度设定值的上限。
2. [RO_MAX_THR_SPEED](#RO_MAX_THR_SPEED) [m/s]: 该参数用于计算闭环速度控制的前馈项，该前馈项将期望速度线性映射到归一化的电机指令。如 [Manual 模式](../flight_modes_rover/manual.md#manual-mode) 配置中所述，一个良好的起点是车辆在 [Manual 模式](../flight_modes_rover/manual.md#manual-mode) 下全油门时的观测地面速度。

   <a id="RA_SPEED_TUNING"></a>

   ::: tip
   进一步调整该参数的方法：

   1. 将 [RO_SPEED_P](#RO_SPEED_P) 和 [RO_SPEED_I](#RO_SPEED_I) 设置为零。这样速度仅由前馈项控制，便于调整。
   2. 将车辆置于 [Position 模式](../flight_modes_rover/manual.md#position-mode)，然后将控制器的左摇杆上下移动并保持在不同位置几秒钟。
   3. 关闭车辆动力并从飞行日志中绘制 [RoverVelocityStatus](../msg_docs/RoverVelocityStatus.md) 消息中的 `adjusted_speed_body_x_setpoint` 和 `measured_speed_body_x` 的重叠曲线。
   4. 如果车辆实际速度高于设定值，增加 [RO_MAX_THR_SPEED](#RO_MAX_THR_SPEED)。反之则降低该参数并重复直到满意。

   :::

   ::: info
   如果车辆在 [Position 模式](../flight_modes_rover/manual.md#position-mode) 下直线行驶时出现振荡，请将此参数设置为 [Manual 模式](../flight_modes_rover/manual.md#manual-mode) 全油门时的观测地面速度，并首先完成 [手动位置模式参数](#手动位置模式参数) 的调校，再继续闭环速度控制的调校。
   :::

3. [RO_SPEED_P](#RO_SPEED_P) [-]: 闭环速度控制器的比例增益。

   ::: tip
   该参数的调校方法与 [RO_MAX_THR_SPEED](#RA_SPEED_TUNING) 相同。如果 [RO_MAX_THR_SPEED](#RO_MAX_THR_SPEED) 调校良好，可能只需要一个非常小的值。
   :::

4. [RO_SPEED_I](#RO_SPEED_I) [-]: 闭环速度控制器的积分增益。

   ::: tip
   对于闭环速度控制，积分增益是有用的，因为该设定值通常会保持一段时间，积分器可以消除稳态误差，这些误差可能导致车辆无法达到设定值。
   :::

5. (Advanced) [RO_SPEED_TH](#RO_SPEED_TH) [m/s]: 速度测量值不被解释为零的最小阈值。当车辆静止时，这可以用来消除测量噪声。

## 手动位置模式参数

如果您需要调整/解锁手动[位置模式](../flight_modes_rover/manual.md#position-mode)，才需要执行以下步骤。否则可以直接进入[position tuning](position_tuning.md)，这些参数也将在该部分进行配置。

1. [PP_LOOKAHD_GAIN](#PP_LOOKAHD_GAIN): 在直线行驶（右摇杆居中）时，位置模式会利用与[自动模式](../flight_modes_rover/auto.md)相同的路径跟随算法[pure pursuit](position_tuning.md#pure-pursuit-guidance-logic-info-only)，以实现最佳直线行驶行为。该参数决定控制器转向路径的激进程度。

   ::: tip
   降低该参数值会增加激进程度，但可能导致振荡。

   调整方法：

   1. 将[PP_LOOKAHD_GAIN](#PP_LOOKAHD_GAIN)的值设为1  
   2. 将机体切换到[位置模式](../flight_modes_rover/manual.md#position-mode)，以最大速度约一半的速度直线行驶，观察其行为  
   3. 如果机体无法保持直线行驶，降低参数值；如果在路径周围振荡，则增加参数值  
   4. 重复调整直到满意

   :::

2. [PP_LOOKAHD_MIN](#PP_LOOKAHD_MIN): [pure pursuit算法](position_tuning.md#pure-pursuit-guidance-logic-info-only)使用的前瞻距离最小阈值。

   ::: tip
   将机体切换到[位置模式](../flight_modes_rover/manual.md#position-mode)并以极低速度行驶，如果机体即使在[PP_LOOKAHD_GAIN](#PP_LOOKAHD_GAIN)中等速度调校良好时仍出现振荡，则增大[PP_LOOKAHD_MIN](#PP_LOOKAHD_MIN)的值。
   :::

3. [PP_LOOKAHD_MAX](#PP_LOOKAHD_MAX): [pure pursuit](position_tuning.md#pure-pursuit-guidance-logic-info-only)使用的前瞻距离最大阈值。

   ::: tip
   将机体切换到[位置模式](../flight_modes_rover/manual.md#position-mode)并以极高速度行驶，如果机体即使在[PP_LOOKAHD_GAIN](#PP_LOOKAHD_GAIN)中等速度调校良好时仍无法保持直线行驶，则减小[PP_LOOKAHD_MAX](#PP_LOOKAHD_MAX)的值。
   :::

现在机体已准备好在[位置模式](../flight_modes_rover/manual.md#position-mode)下行驶，配置可以继续进行[position tuning](position_tuning.md)。

## 姿态控制器结构（仅信息）

本节为开发者及具有控制系统设计经验的人员提供附加信息。

速度矢量由以下两个值定义：

1. 绝对速度 [$m/s$]  
2. 方向（方位角） [$rad$]  

速度控制器采用以下结构：  

![Rover Speed Controller](../../assets/config/rover/rover_speed_controller.png)  

前馈映射通过将速度设定值从范围 -[RO_MAX_THR_SPEED](../advanced_config/parameter_reference.md#RO_MAX_THR_SPEED), [RO_MAX_THR_SPEED](../advanced_config/parameter_reference.md#RO_MAX_THR_SPEED)] 插值得到 [-1, 1] 范围。

对于 ackermann 和 differential 类型的底盘，方位角与机体偏航角对齐。因此方位角直接作为偏航设定值发送给 [偏航控制器](attitude_tuning.md#attitude-controller-structure-info-only)，而速度设定值始终定义在机体x轴方向。

对于 mecanum 车体，方位角与偏航角解耦。方向控制通过将速度矢量分解为机体x轴方向和机体y轴方向的两个速度分量实现。
这两个设定值随后分别发送至各自独立的闭环速度控制器。

## 参数概览

| 参数                                                                                                   | 描述                                 | 单位  |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------ | ----- |
| <a id="RO_MAX_THR_SPEED"></a>[RO_MAX_THR_SPEED](../advanced_config/parameter_reference.md#RO_MAX_THR_SPEED) | 越野车在最大油门时的行驶速度         | $m/s$ |
| <a id="RO_SPEED_LIM"></a>[RO_SPEED_LIM](../advanced_config/parameter_reference.md#RO_SPEED_LIM)           | 允许的最高速度                       | $m/s$ |
| <a id="RO_SPEED_P"></a>[RO_SPEED_P](../advanced_config/parameter_reference.md#RO_SPEED_P)                 | 速度控制器的比例增益                 | -     |
| <a id="RO_SPEED_I"></a>[RO_SPEED_I](../advanced_config/parameter_reference.md#RO_SPEED_I)                 | 速度控制器的积分增益                 | -     |
| <a id="RO_SPEED_TH"></a>[RO_SPEED_TH](../advanced_config/parameter_reference.md#RO_SPEED_TH)              | （高级）速度测量阈值                 | $m/s$ |

### Pure Pursuit

| 参数                                                                                                 | 描述                                           | 单位 |
| ----------------------------------------------------------------------------------------------------- | ---------------------------------------------- | ---- |
| <a id="PP_LOOKAHD_GAIN"></a>[PP_LOOKAHD_GAIN](../advanced_config/parameter_reference.md#PP_LOOKAHD_GAIN) | Pure pursuit: 主要调节参数                     | -    |
| <a id="PP_LOOKAHD_MAX"></a>[PP_LOOKAHD_MAX](../advanced_config/parameter_reference.md#PP_LOOKAHD_MAX)    | Pure pursuit: 前瞻半径的最大值               | m    |
| <a id="PP_LOOKAHD_MIN"></a>[PP_LOOKAHD_MIN](../advanced_config/parameter_reference.md#PP_LOOKAHD_MIN)    | Pure pursuit: 前瞻半径的最小值               | m    |