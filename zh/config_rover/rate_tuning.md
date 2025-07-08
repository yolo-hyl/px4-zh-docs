# 速率调节

速率调节是使用 [Acro mode](../flight_modes_rover/manual.md#acro-mode) 和所有后续模式所必需的。

::: warning
在进行此步骤前，必须已完成 [Basic Setup](basic_setup.md)！
:::

在 QGroundControl 中配置以下 [参数](../advanced_config/parameters.md)：

1. [RO_YAW_RATE_LIM](#RO_YAW_RATE_LIM): 您希望为机体允许的最大偏航速率。

   :::tip
   如果机体容易翻滚、高速时失去抓地力或乘客舒适性重要，则需要限制偏航速率。
   小型机体在高速急转向时尤其容易翻滚。

   如果出现这种情况：

   1. 在 [Acro mode](../flight_modes_rover/manual.md#acro-mode) 中，将 [RO_YAW_RATE_LIM](#RO_YAW_RATE_LIM) 设置为较小值，全油门驱动机体并将转向杆完全左/右转。
   2. 增加 [RO_YAW_RATE_LIM](#RO_YAW_RATE_LIM) 直到机体车轮开始抬起。
   3. 将 [RO_YAW_RATE_LIM](#RO_YAW_RATE_LIM) 设置为不会使机体抬起的最大值。

   如果认为无需限制偏航速率，请将参数设置为机体可达到的最大偏航速率：

   1. 在 [Manual mode](../flight_modes_rover/manual.md#manual-mode) 中全油门驱动机体并以最大转向角操作。
   2. 从 [RoverRateStatus](../msg_docs/RoverRateStatus.md) 绘制 `measured_yaw_rate`，并将观察到的最高值输入 [RO_YAW_RATE_LIM](#RO_YAW_RATE_LIM)。
   :::

2. (可选) [RO_YAW_RATE_CORR](#RO_YAW_RATE_CORR) [-]: 偏航速率校正因子。

   如果偏航速率指令与 [理想映射](#kinematic-models) 存在偏差（可能因车轮错位、摩擦过大等），可使用此参数缩放指令到转向力的映射。

   ::: info
   滑移/履带转向和麦卡纳姆轮机体很可能需要此调整。
   :::

   :::tip
   调整此参数时，首先将 [RO_YAW_RATE_P](#RO_YAW_RATE_P) 和 [RO_YAW_RATE_I](#RO_YAW_RATE_I) 设置为零。
   这样偏航速率仅由前馈项控制，便于调整。
   现将机体置于 [Acro mode](../flight_modes_rover/manual.md#acro-mode)，然后将控制器右摇杆向右/左移动并保持在不同水平约几秒，同时以恒定油门行驶（差速/麦卡纳姆轮机体也可在静止状态下进行）。
   解锁机体后，从飞行日志中绘制 [RoverRateStatus](../msg_docs/RoverRateStatus.md) 的 `adjusted_yaw_rate_setpoint` 和 `measured_yaw_rate`。
   如果实际偏航速率高于指令值，请减小 [RO_YAW_RATE_CORR](#RO_YAW_RATE_CORR)（范围 [0, 1]）。
   如果相反则增加参数（范围 [1, inf]），重复调整直到满意。
   :::

3. [RO_YAW_RATE_P](#RO_YAW_RATE_P) [-]: 闭环偏航速率控制器的比例增益。
   闭环加速度控制会比较偏航速率指令与实际值，并根据误差调整电机指令。
   比例增益与误差相乘后添加到电机指令中，以补偿地面不平或外力等干扰。

   :::tip
   调整此参数：

   1. 将机体置于 [Acro mode](../flight_modes_rover/manual.md#acro-mode) 并将油门杆和右杆保持在不同水平约几秒。
   1. 解锁机体后，从飞行日志中绘制 [RoverRateStatus](../msg_docs/RoverRateStatus.md) 的 `adjusted_yaw_rate_setpoint` 和 `measured_yaw_rate`。
   1. 如果测量值未能快速跟踪指令值，请增加 [RO_YAW_RATE_P](#RO_YAW_RATE_P)；如果测量值过度超调指令值，请减小该参数。
   1. 重复调整直到满意。
      :::

4. [RO_YAW_RATE_I](#RO_YAW_RATE_I) [-]: 闭环偏航速率控制器的积分增益。
   积分增益会随时间累积目标与实际偏航速率的误差（按积分增益缩放），并将该值添加到电机指令中。

   ::: tip
   此阶段可能不需要积分器，但它对后续模式很重要。
   :::

5. (可选) [RO_YAW_ACCEL_LIM](#RO_YAW_ACCEL_LIM)/[RO_YAW_DECEL_LIM](#RO_YAW_DECEL_LIM) [deg/s^2]: 用于限制偏航加速度/减速度。
   这可使偏航速率指令轨迹更平滑。

6. (可选) [RO_YAW_STICK_DZ](#RO_YAW_STICK_DZ) [-]: 被解释为零的偏航杆输入范围百分比，围绕杆中立值。

7. (高级) [RO_YAW_RATE_TH](#RO_YAW_RATE_TH) [deg/s]: 偏航速率测量值不被解释为零的最小阈值。
   当机体静止时，可用于消除测量噪声。

机体现已准备好以 [Acro mode](../flight_modes_rover/manual.md#acro-mode) 行驶，配置可继续进行 [姿态调节](attitude_tuning.md)。

## 机体速率控制器结构（仅信息）

本节为开发者及熟悉控制系统设计的人员提供附加信息。

速率控制器采用以下结构：

![Rover 速率控制器](../../assets/config/rover/rover_rate_controller.png)

::: info
对于阿克曼式车体，偏航速率仅在向前行驶时进行闭环控制。
向后行驶时，偏航速率设定值通过上述公式直接映射到转向角。
这是因为后轮转向（以前轮转向车辆倒车）相对于偏航速率是非最小相位系统，会导致闭环控制时出现不稳定。
:::

前馈映射通过车体运动学模型实现，将偏航速率设定值转换为标准化的转向设定值。

### 运动学模型

#### 阿克曼式

<!-- prettier-ignore -->
$$ \delta = \arctan(\frac{w_b \cdot \dot{\psi}}{v}) $$

其中

- $w_b:$ 轮距，
- $\delta:$ 转向角，
- $\dot{\psi}:$ 偏航速率
- $v:$ 前进速度。

转向设定值等于 $\delta$ 从范围 [-[RA_MAX_STR_ANG](../advanced_config/parameter_reference.md#RA_MAX_STR_ANG)，[RA_MAX_STR_ANG](../advanced_config/parameter_reference.md#RA_MAX_STR_ANG)] 到 [-1, 1] 的插值。

对于行驶而言，这意味着相同右手杆输入会根据车速产生不同的转向角。
通过限制最大偏航速率，可以基于速度限制转向角，从而防止车体侧翻。
这种模式会感觉更像“驾驶汽车”而非 [手动模式](../flight_modes_rover/manual.md#manual-mode)。

#### 差速/全向轮式

<!-- prettier-ignore -->
$$ v_{diff} = \frac{w_t \cdot \dot{\psi}}{2} $$

其中

- $v_{diff}:$ 左右轮速度差，
- $w_t:$ 轮距 ([RD_WHEEL_TRACK](../advanced_config/parameter_reference.md#RD_WHEEL_TRACK))，
- $\dot{\psi}:$ 偏航速率设定值。

转向设定值等于 $v_{diff}$ 从范围 [-[RO_MAX_THR_SPEED](../advanced_config/parameter_reference.md#RO_MAX_THR_SPEED)，[RO_MAX_THR_SPEED](../advanced_config/parameter_reference.md#RO_MAX_THR_SPEED)] 到 [-1, 1] 的插值。

基于理想运动学模型的这些映射可以通过乘法因子 [RO_YAW_RATE_CORR](../advanced_config/parameter_reference.md#RO_YAW_RATE_CORR) 进行调整，用于调节偏航速率控制器的前馈部分，以补偿轮子对齐误差、高摩擦等因素。

## 参数概览

| 参数                                                                                                   | 描述                                 | 单位         |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------ | ------------ |
| <a id="RO_YAW_RATE_LIM"></a>[RO_YAW_RATE_LIM](../advanced_config/parameter_reference.md#RO_YAW_RATE_LIM)    | 最大允许偏航速率                     | $m/s^2$     |
| <a id="RO_YAW_RATE_P"></a>[RO_YAW_RATE_P](../advanced_config/parameter_reference.md#RO_YAW_RATE_P)          | 偏航速率控制器的比例增益             | -           |
| <a id="RO_YAW_RATE_I"></a>[RO_YAW_RATE_I](../advanced_config/parameter_reference.md#RO_YAW_RATE_I)          | 偏航速率控制器的积分增益             | -           |
| <a id="RO_YAW_STICK_DZ"></a>[RO_YAW_STICK_DZ](../advanced_config/parameter_reference.md#RO_YAW_STICK_DZ)    | 偏航操纵杆死区                       | -           |
| <a id="RO_YAW_ACCEL_LIM"></a>[RO_YAW_ACCEL_LIM](../advanced_config/parameter_reference.md#RO_YAW_ACCEL_LIM) | （可选）偏航加速度限制               | $deg/s^2$   |
| <a id="RO_YAW_DECEL_LIM"></a>[RO_YAW_DECEL_LIM](../advanced_config/parameter_reference.md#RO_YAW_DECEL_LIM) | （可选）偏航减速度限制               | $deg/s^2$   |
| <a id="RO_YAW_RATE_CORR"></a>[RO_YAW_RATE_CORR](../advanced_config/parameter_reference.md#RO_YAW_RATE_CORR) | （可选）偏航速率修正系数             | -           |
| <a id="RO_YAW_RATE_TH"></a>[RO_YAW_RATE_TH](../advanced_config/parameter_reference.md#RO_YAW_RATE_TH)       | （高级）偏航速率测量阈值             | $deg/s$     |