# 基础设置

## 配置无人车机架和输出

1. 通过将[PX4无人车编译版本](../config_rover/index.md#flashing-the-rover-build)烧录到飞控设备来启用无人车支持。
   注意这是一个特殊编译版本，包含无人车专用模块。

2. 在[Airframe](../config/airframe.md)配置中选择你的无人车类型：_Generic Rover Ackermann_/_Generic Rover Differential_/_Generic Rover Mecanum_:

   ![QGC截图展示选择机架类型'Generic ackermann rover'](../../assets/config/airframe/airframe_generic_rover_ackermann.png)

   选择 **Apply and Restart** 按钮。

   ::: info
   如果该机架类型未显示且你确认使用的是无人车固件(非默认)，可通过直接设置[SYS_AUTOSTART](../advanced_config/parameter_reference.md#SYS_AUTOSTART)参数来启用该机架，值如下：

   | 无人车类型   | 值     |
   | ------------ | ------- |
   | Ackermann    | `51000` |
   | Differential | `50000` |
   | Mecanum      | `52000` |

   :::

3. 打开[执行器配置与测试](../config/actuators.md)将电机/舵机功能映射到飞控输出。

这就是在[Manual mode](../flight_modes_rover/manual.md#manual-mode)中使用无人车的最小配置。

## 几何参数

手动模式也会受到(可选)加速度/减速度限制的影响，这些限制通过下方描述的几何参数设置。
这些限制对于所有其他模式都是必需的。

![几何参数](../../assets/config/rover/geometric_parameters.png)

在QGroundControl中导航到[Parameters](../advanced_config/parameters.md)并设置对应机架类型的参数组。

### Ackermann

1. [RA_WHEEL_BASE](#RA_WHEEL_BASE) [m]: 测量后轮到前轮的距离。
2. [RA_MAX_STR_ANG](#RA_MAX_STR_ANG) [deg]: 测量最大转向角。
3. (可选) [RA_STR_RATE_LIM](#RA_STR_RATE_LIM) [deg/s]: 你希望允许的最大转向速率。

   :::tip
   该值取决于你的无人车和使用场景。
   对于大型无人车，可能存在可通过静止转向并逐渐增加
   [RA_STR_RATE_LIM](#RA_STR_RATE_LIM)直到转向速率不再受参数限制来识别的机械限制。
   对于小型无人车，你可能会发现转向过于激进。将[RA_STR_RATE_LIM](#RA_STR_RATE_LIM)设为低值并静止转向无人车。
   逐渐增加参数直到达到你舒适的最高速率。
   :::

   :::warning
   低最大转向速率会降低无人车追踪转向设定值的能力，可能导致后续模式性能下降。
   :::

### Differential

1. [RD_WHEEL_TRACK](#RD_WHEEL_TRACK) [m]: 测量右轮中心到左轮中心的距离。

### Mecanum

1. [RM_WHEEL_TRACK](#RM_WHEEL_TRACK) [m]: 测量右轮中心到左轮中心的距离。

## 速度参数

1. [RO_MAX_THR_SPEED](#RO_MAX_THR_SPEED) [m/s]: 以全油门驾驶无人车，并将此参数设为观察到的地面速度值。

   :::info
   该参数也用于闭环速度控制的前馈项。
   它将在[velocity tuning](velocity_tuning.md)步骤中进一步调整。
   :::

2. (可选) [RO_ACCEL_LIM](#RO_ACCEL_LIM) [m/s^2]: 你希望允许的最大加速度。

   <a id="RO_ACCEL_LIM_CONSIDERATIONS"></a>

   :::tip
   你的无人车有一个由电机最大扭矩决定的最大可能加速度。
   这可能与你的机体和使用场景是否匹配。

   确定合适值的方法之一是：

   1. 从静止状态全油门加速直到达到最大速度。
   2. 断开无人车电源并绘制[RoverVelocityStatus](../msg_docs/RoverVelocityStatus.md)中的`measured_speed_body_x`。
   3. 用最大速度除以达到该速度所需时间，将结果设为[RO_ACCEL_LIM](#RO_ACCEL_LIM)的值。

   某些遥控无人车在未限制最大加速度时可能会腾空。
   如果出现这种情况：

   1. 将[RO_ACCEL_LIM](#RO_ACCEL_LIM)设为低值，从静止全油门加速并观察行为。
   2. 逐渐增加[RO_ACCEL_LIM](#RO_ACCEL_LIM)直到无人车开始加速腾空。
   3. 将[RO_ACCEL_LIM](#RO_ACCEL_LIM)设为不会导致腾空的最大值。
      :::

3. (可选) [RO_DECEL_LIM](#RO_DECEL_LIM) [m/s^2]: 你希望允许的最大减速度。

   :::tip
   [RO_ACCEL_LIM](#RO_ACCEL_LIM)的[考虑因素](#RO_ACCEL_LIM_CONSIDERATIONS)同样适用于此配置。
   :::

   :::info
   该参数还用于[position controlled](position_tuning.md)模式中的速度设定值计算。
   :::

你现在可以继续进行[rate tuning](rate_tuning.md)的配置过程。

## 参数概览

| 参数                                                                                                   | 描述                                | 单位    |
| ------------------------------------------------------------------------------------------------------- | ----------------------------------- | ------- |
| <a id="RO_MAX_THR_SPEED"></a>[RO_MAX_THR_SPEED](../advanced_config/parameter_reference.md#RO_MAX_THR_SPEED) | 机体全油门行驶的速度 | $m/s$ |
| <a id="RO_ACCEL_LIM"></a>[RO_ACCEL_LIM](../advanced_config/parameter_reference.md#RO_ACCEL_LIM) | 机体允许的最大加速度 | $m/s^2$ |
| <a id="RO_DECEL_LIM"></a>[RO_DECEL_LIM](../advanced_config/parameter_reference.md#RO_DECEL_LIM) | 机体允许的最大减速度 | $m/s^2$ |

### Ackermann专用参数

| 参数                                                                                                   | 描述                                | 单位    |
| ------------------------------------------------------------------------------------------------------- | ----------------------------------- | ------- |
| <a id="RA_WHEEL_BASE"></a>[RA_WHEEL_BASE](../advanced_config/parameter_reference.md#RA_WHEEL_BASE) | 测量后轮到前轮的距离 | $m$ |
| <a id="RA_MAX_STR_ANG"></a>[RA_MAX_STR_ANG](../advanced_config/parameter_reference.md#RA_MAX_STR_ANG) | 测量最大转向角 | $deg$ |
| <a id="RA_STR_RATE_LIM"></a>[RA_STR_RATE_LIM](../advanced_config/parameter_reference.md#RA_STR_RATE_LIM) | 机体允许的最大转向速率 | $deg/s$ |

### Differential专用参数

| 参数                                                                                                   | 描述                                | 单位    |
| ------------------------------------------------------------------------------------------------------- | ----------------------------------- | ------- |
| <a id="RD_WHEEL_TRACK"></a>[RD_WHEEL_TRACK](../advanced_config/parameter_reference.md#RD_WHEEL_TRACK) | 测量右轮中心到左轮中心的距离 | $m$ |

### Mecanum专用参数

| 参数                                                                                                   | 描述                                | 单位    |
| ------------------------------------------------------------------------------------------------------- | ----------------------------------- | ------- |
| <a id="RM_WHEEL_TRACK"></a>[RM_WHEEL_TRACK](../advanced_config/parameter_reference.md#RM_WHEEL_TRACK) | 测量右轮中心到左轮中心的距离 | $m$ |