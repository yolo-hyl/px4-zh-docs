# 直升机配置

本节包含与[直升机](../frames_helicopter/index.md)配置和调校相关的内容。

## 支持的配置

支持的直升机配置：

- 单主旋翼，通过最多4个总距板伺服电机控制总距板，并通过ESC驱动机械解耦的尾桨。
- 单主旋翼，通过最多4个总距板伺服电机控制总距板，并通过伺服控制机械耦合的尾部。

支持的飞行操作/功能：

- 与多旋翼相同。
- 截至撰写时，尚不支持具有负推力的自主/引导式3D飞行。

## 设置

要设置和配置直升机：

1. 在 QGroundControl 中选择直升机 [机身](../config/airframe.md)。
   当前仅在 Helicopter 分组中提供 _通用直升机（尾部电调）_ 选项。
   这将配置为机械解耦尾部的直升机机身 ([CA_AIRFRAME](../advanced_config/parameter_reference.md#CA_AIRFRAME): `10: Helicopter (tail ESC)`).

   ![QGC - 直升机机身](../../assets/config/airframe/airframe_helicopter_generic.png)

   ::: info
   没有为带有尾舵的直升机单独设计机身。
   要选择此配置，将参数 [CA_AIRFRAME](../advanced_config/parameter_reference.md#CA_AIRFRAME) 设置为 _Helicopter (tail Servo)_。
   执行器配置界面将随之改变以支持此机身类型。
   :::

1. 在 **Vehicle Setup > Actuators** 中配置直升机执行器几何。

   ::: info
   大多数机身的执行器设置和测试在 [Actuators](../config/actuators.md) 中有说明。
   虽然下文有所参考，但本部分是直升机设置的主要说明。
   :::

   [通用直升机 - 带尾部电调](../airframes/airframe_reference.md#copter_helicopter_generic_helicopter_%28tail_esc%29) 的几何结构如下所示。

   ![几何结构: 直升机](../../assets/config/actuators/qgc_geometry_helicopter.png)

   电机没有可配置的几何结构：

   - `Rotor (Motor 1)`: 主旋翼
   - `Yaw tail motor (Motor 2)`: 尾旋翼

   变距环舵机: `3` | `4` <!-- 4 提供额外稳定性 -->

   对于每个舵机设置：

   - `Angle`: 在变距环圆周上舵机臂的顺时针角度（以 `0` 指向前方为起点）。
     典型三舵机等间距控制变距环的示例（360° / 3 =) 每个舵机间隔 120°，对应角度为：

     | #       | Angle |
     | ------- | ----- |
     | Servo 1 | 60°   |
     | Servo 2 | 180°  |
     | Servo 3 | 300°  |

     <img width="700" alt="警告和要求" src="../../assets/airframes/helicopter/swash_plate_servo_angles.png">

   - `Arm Length (relative to each other)`: 从变距环中心到舵机臂的半径（俯视图）。较短的臂长意味着相同的舵机运动会使变距环移动更多，这允许自动驾驶仪进行补偿。
   - `Trim`: 偏移单个舵机位置。只有在变距环不水平的情况下所有舵机都居中时才需要。

   其他设置：

   - `Yaw compensation scale based on collective pitch`: 根据当前总距进行偏航前馈补偿的量。
   - `Main rotor turns counter-clockwise`: `Disabled` (顺时针旋转) | `Enabled`
   - `Throttle spoolup time`: 设置值（以秒为单位）大于实际可达到的最小电机加速时间。
     较大值可能会改善用户体验。

1. 移除旋翼桨叶和螺旋桨
1. 分配电机和舵机到输出并测试（参见 [执行器配置](../config/actuators.md)）:

   1. [将电机和舵机分配到输出](../config/actuators.md#actuator-outputs)。
   1. 使用电池为机体供电，并通过 [执行器测试滑块](../config/actuators.md#actuator-testing) 验证舵机和电机的分配及方向是否正确。

1. 使用遥控器在 [Acro 模式](../flight_modes_mc/acro.md) 下验证变距环的正确运动。对于大多数机身，需要观察以下情况：

   - 向右移动滚转杆应使变距环向右倾斜。
   - 向前移动俯仰杆应使变距环向前倾斜。

   如果机身需要任何相位滞后角度偏移，可以简单地添加到所有变距环舵机角度中。请参考机身制造商的文档。

1. 武器化机体并检查主旋翼缓慢加速。
   根据需要使用参数 [COM_SPOOLUP_TIME](../advanced_config/parameter_reference.md#COM_SPOOLUP_TIME) 调整油门加速时间。
   还可以使用参数 [CA_HELI_THR_Cx](../advanced_config/parameter_reference.md#CA_HELI_THR_C0) 调整油门曲线。
   默认值为恒定最大油门（适合大多数设置）。
1. 再次解除武装并断电。
1. 安装旋翼桨叶并为机体供电。
1. 使用参数 [CA_HELI_PITCH_Cx](../advanced_config/parameter_reference.md#CA_HELI_PITCH_C0) 配置总距曲线。
   根据所需的最小和最大桨叶角度设置最小值和最大值。
   确保最小值足够低，以便机体仍能下降。
   不要一开始就设置过低值。
   默认值略带负值，因此可以作为良好的起点。

## 调整

完成上述步骤后，您可以安装旋翼并准备解锁。

首先调整[速率控制器](#rate-controller)和[偏航补偿](#yaw-compensation)（如下部分所示，这些是直升机专用的）。

姿态、速度和位置控制器的调整与多旋翼的[相同方式](../config_mc/index.md)进行。

请注意，自动调参目前不支持/未经过测试（撰写时）。

### 偏航补偿

由于偏航扭矩补偿对直升机稳定悬停至关重要，因此需要先进行大致配置。在速率控制器按预期工作后，可以重新访问本节进行精确调参。

最重要的是设置主旋翼的旋转方向，默认情况下从机体上方看是顺时针旋转。如果您的主旋翼为逆时针旋转，请将[CA_HELI_YAW_CCW](../advanced_config/parameter_reference.md#CA_HELI_YAW_CCW)设为1。

主旋翼的总距和油门补偿有以下两个参数：
[CA_HELI_YAW_CP_S](../advanced_config/parameter_reference.md#CA_HELI_YAW_CP_S)
[CA_Hel_YAW_TH_S](../advanced_config/parameter_reference.md#CA_HELI_YAW_TH_S)

当尾桨的正推力导致机体与主旋翼旋转方向相反时，需要使用负值。

### 速率控制器

速率控制器应在[Acro模式](../flight_modes_mc/acro.md)下进行调整，但如果无法使用Acro模式，也可以在[Stabilized模式](../flight_modes_mc/manual_stabilized.md)下进行调整。

1. 初始阶段禁用速率控制器增益，仅设置小的前馈值：

   ```sh
   param set MC_ROLLRATE_P 0
   param set MC_ROLLRATE_I 0
   param set MC_ROLLRATE_D 0
   param set MC_ROLLRATE_FF 0.1
   param set MC_PITCHRATE_P 0
   param set MC_PITCHRATE_I 0
   param set MC_PITCHRATE_D 0
   param set MC_PITCHRATE_FF 0.1
   ```

2. 缓慢起飞并进行一定量的横滚和杆位移动作。
   使用QGC调参界面查看响应：

   ![QGC 速率控制器调参界面](../../assets/mc_pid_tuning/qgc_mc_pid_tuning_rate_controller.png)

   逐步增加横滚和俯仰前馈增益[MC_ROLLRATE_FF](../advanced_config/parameter_reference.md#MC_ROLLRATE_FF)、[MC_PITCHRATE_FF](../advanced_config/parameter_reference.md#MC_PITCHRATE_FF)，直到阶跃输入时响应达到设定值。

3. 然后启用PID增益。
   初始设置建议以下值：

   - [MC_ROLLRATE_P](../advanced_config/parameter_reference.md#MC_ROLLRATE_P)、[MC_PITCHRATE_P](../advanced_config/parameter_reference.md#MC_PITCHRATE_P) 取前一步骤中前馈值的四分之一。`P = FF / 4`

   ```sh
   param set MC_ROLLRATE_I 0.2
   param set MC_PITCHRATE_I 0.2
   param set MC_ROLLRATE_D 0.001
   param set MC_PITCHRATE_D 0.001
   ```

   根据需要逐步增加`P`和`D`增益，直到跟踪效果良好。
   通常`P`增益会明显小于`FF`增益。