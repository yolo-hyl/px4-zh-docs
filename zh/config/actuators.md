# 执行器配置与测试

<Badge type="tip" text="PX4 v1.14" />

_Actuators Setup_ 视图用于定制机体的特定几何结构，将执行器和电机分配到飞控输出，并测试执行器和电机的响应。

## 概述

在 _QGroundControl_ 中打开视图：**"Q" (应用菜单) > 机体设置 > 执行器** (标签页)。  
显示内容取决于 [选定的机体](../config/airframe.md)，默认映射方式可参考 [机体参考手册](../airframes/airframe_reference.md)。

该视图包含三个部分：

- [几何结构](#geometry): 配置 [选定机体](../config/airframe.md) 的几何参数。  
  包含 [电机](#motor-geometry) 的数量、位置及属性，以及 [控制面](#control-surfaces-geometry) 和 [电机倾斜舵机](#motor-tilt-servo-geometry) 的数量和属性。
- [执行器输出](#actuator-outputs): 将电机、控制面和其他执行器分配到特定输出。
- [执行器测试](#actuator-testing): 测试电机和执行器是否按预期方向/速度移动。

四旋翼无人机的设置界面可能如下所示。  
该设置定义了一个采用 X 型几何结构的四旋翼无人机。  
它将 4 个电机映射到 AUX1 至 AUX4 输出，并指定连接 DShot1200 电调。  
同时将 PWM400 辅助输出映射用于控制降落伞和起落架。

![执行器 MC (QGC)](../../assets/config/actuators/qgc_actuators_mc_aux.png)

::: info
默认仅显示最常用设置。  
点击右上角 **Advanced** 复选框可显示所有设置。
:::Here's a structured and practical explanation of the key concepts in configuring motor tilt servos and control surfaces for PX4 Autopilot systems, tailored for developers and hobbyists:

---

### **1. Motor Tilt Servo Configuration (Tiltrotor Aircraft)**
**Purpose**: Tiltrotor aircraft (e.g., quadtiltrotors) transition between vertical takeoff/landing (hover) and forward flight by tilting their motors. This section defines how to map and control the servos that tilt the motors.

#### **Key Parameters to Configure**
- **Number of Tilt Servos**: Matches the number of motors that can tilt (e.g., 4 for a quadtiltrotor).
- **Angle at Min Tilt**: The smallest tilt angle (closest to vertical) the servo can reach.  
  - Example: A servo tilting from 0° (vertical) to 90° (horizontal) would have `min=0` and `max=90`.
- **Angle at Max Tilt**: The largest tilt angle (furthest from vertical).  
  - If tilting symmetrically (e.g., -45° to +45°), set `min=-45` and `max=45`.
- **Tilt Direction**:  
  - `Towards front` (positive X-axis): Motors tilt forward (common for quadtiltrotors).  
  - `Towards right` (positive Y-axis): Motors tilt sideways (less common but used in some designs).
- **Use for Control**:  
  - `None`: Servos only tilt motors for flight mode transitions.  
  - `Yaw`/`Pitch`/`Both`: Use servo tilting to actively control the aircraft’s yaw or pitch (useful for bicopters or asymmetric airframes).  

#### **Coordinate System for Tilt Angles**
- **Reference**: 0° is straight up (vertical).  
- **Positive Angles**: Tilt toward the front (X-axis) or right (Y-axis).  
- **Negative Angles**: Tilt toward the back (negative X) or left (negative Y).  
- **Example**:  
  - A motor tilting forward from 0° to 90°: `min=0`, `max=90`, `Tilt direction=front`.  
  - A motor tilting backward from 0° to -90°: `min=-90`, `max=0`, `Tilt direction=front`.

---

### **2. Control Surface Configuration**
Control surfaces (ailerons, elevons, rudders, etc.) are adjusted to control the aircraft’s roll, pitch, and yaw. Proper configuration ensures predictable behavior.

#### **Control Surface Deflection Convention**
- **Positive Input = Positive Deflection**:  
  - **Horizontal surfaces** (ailerons, elevons): Upward movement.  
  - **Vertical surfaces** (rudders): Rightward movement.  
  - **Mixed surfaces** (V-Tails): Upward/rightward.  
- **One-Way Deflection** (e.g., flaps, spoilers):  
  - Positive input = full deflection from neutral (e.g., flaps down, spoilers raised).  
  - No response to negative inputs.  

#### **Scaling for Effectiveness**
- **Roll/Pitch/Yaw Scale**:  
  - These values (e.g., 0.5–1.0) determine how much a control surface contributes to rotation.  
  - **Higher scale = less deflection needed** to achieve the same effect.  
  - **Tuning Tip**: Adjust only for complex setups (e.g., elevons controlling both pitch and roll). Default values are usually sufficient.  

#### **Flap and Spoiler Scale**
- **Flap Scale**: Controls how much each surface (e.g., flaps, ailerons, elevators) contributes to "Flap-control" (used for landing or slow flight).  
  - Example: Flaps with `Flap Scale=1` fully deploy when Flap-control is set to 1.  
- **Spoiler Scale**: Controls how much surfaces contribute to "Spoiler-control" (used to reduce airspeed).  
  - Example: Ailerons with `Spoiler Scale=0.5` deflect 50% upward when Spoiler-control is active.  

---

### **3. Practical Configuration Steps**
1. **Motor Tilt Servos**:  
   - Measure the physical range of your tilt servo (e.g., 0° to 90° forward tilt).  
   - Set `Angle at min tilt` and `Angle at max tilt` accordingly.  
   - Choose `Tilt direction` based on the servo’s movement (front/right).  

2. **Control Surfaces**:  
   - Ensure servos move in the correct direction for positive inputs (use the diagram convention).  
   - For flaps/spoilers, set `Flap Scale` and `Spoiler Scale` to match their role in flight modes.  

3. **Testing**:  
   - Use QGroundControl to manually test servo movements.  
   - Validate that tilting motors and control surfaces behave as expected in simulation or real-world tests.  

---

### **4. Common Issues & Solutions**
- **Incorrect Tilt Angles**:  
  - If the aircraft doesn’t transition properly, double-check `min`/`max` tilt angles and servo direction.  
- **Unresponsive Control Surfaces**:  
  - Verify that control scales are set correctly and servos are calibrated.  
- **Unstable Flight During Transitions**:  
  - Adjust `slew rates` (e.g., flaps/spoilers) to smooth out movements.  

---

### **Example Configuration for a Quadtiltrotor**
```plaintext
Motor Tilt Servos:
- Number of tilt servos: 4
- Angle at min tilt: 0°
- Angle at max tilt: 90°
- Tilt direction: Towards front
- Use for control: None

Control Surfaces:
- Ailerons:
  - Roll scale: 1.0
  - Spoiler scale: 0.5
- Flaps:
  - Flap scale: 1.0
  - Slew rate: 0.5s (2s to fully deploy)
```

---

This setup ensures the aircraft transitions smoothly between hover and forward flight while maintaining stable control during all phases. Always validate configurations in simulation before real-world testing!## 执行器输出

_执行器输出_部分用于将机体的电机、控制面舵机和其他执行器分配到飞控的物理输出，并设置这些输出的参数。

![执行器输出 - 多旋翼图示](../../assets/config/actuators/qgc_actuators_mc_outputs.png)

对于连接的飞控所支持的每个输出总线，会显示单独的标签页：PWM MAIN（I/O板输出）、PWM AUX（FMU板输出）、UAVCAN。

电机和执行器（称为"[功能](#output-functions)"）可以分配到任何可用总线上的物理输出。

::: info
建议使用PWM AUX输出而非PWM MAIN输出来控制电机（因为它们的延迟更低）。
:::

PWM输出根据硬件定时器组进行分组。这意味着同一组内的所有输出必须以相同的协议和频率运行（例如某一组的所有输出都以400Hz的PWM信号工作）。因此，同一输出组内无法同时映射舵机和电机，因为它们通常工作频率不同。

PWM AUX标签页中的CAP输出通常用作[相机捕获/触发输入](../camera/fc_connected_camera.md#trigger-configuration)。但也可以将CAP输出映射到其他输出功能，其他AUX输出也可用作相机捕获/触发输入。

::: info
配置相机捕获/触发输入需要重启才能生效
:::

应根据电机和舵机的实际接线分配功能，并使用以下[执行器测试](#actuator-testing)部分确定合适的输出参数值。这些步骤详见[输出分配与配置](#output-assignment-and-configuration)。

### 输出功能

输出功能用于将机体的"逻辑功能"（如`Motor 1`或`Landing gear`）映射到物理输出（如FMU输出引脚2）。这使得特定输出引脚可以几乎用于任何用途。

某些功能仅适用于特定机体或输出类型，其他类型不会提供。

功能包括：

- `Disabled`：输出无分配功能。
- `Constant_Min`：输出设置为恒定最小值（-1）。
- `Constant_Max`：输出设置为恒定最大值（+1）。
- `Motor 1`到`Motor 12`：输出对应电机。仅显示机体允许的电机。
- `Servo 1`到`Servo 8`：舵机输出。这些会根据机体进一步分配特定含义，如"倾斜舵机"、"左副翼"。
- `Peripheral via Acutator Set 1`到`Peripheral via Acutator Set 6`：[通过MAVLink的通用执行器控制](../payloads/generic_actuator_control.md#generic-actuator-control-with-mavlink)。
- `Landing Gear`：输出对应起落架。
- `Parachute`：输出对应降落伞。正常使用时发送最小值，触发安全机制时发送最大值。
- `RC Roll`：输出通过RC的滚转信号（[RC_MAP_ROLL](../advanced_config/parameter_reference.md#RC_MAP_ROLL)将RC通道映射到此输出）。
- `RC Pitch`：输出通过RC的俯仰信号（[RC_MAP_PITCH](../advanced_config/parameter_reference.md#RC_MAP_PITCH)将RC通道映射到此输出）。
- `RC Throttle`：输出通过RC的油门信号（[RC_MAP_THROTTLE](../advanced_config/parameter_reference.md#RC_MAP_THROTTLE)将RC通道映射到此输出）。
- `RC Yaw`：输出通过RC的偏航信号（[RC_MAP_YAW](../advanced_config/parameter_reference.md#RC_MAP_YAW)将RC通道映射到此输出）。
- `RC Flaps`：输出通过RC的襟翼信号（[RC_MAP_FLAPS](../advanced_config/parameter_reference.md#RC_MAP_FLAPS)将RC通道映射到此输出）。
- `RC AUXn`到`RC AUX1`：用于[通过RC触发的任意载荷](../payloads/generic_actuator_control.md#generic-actuator-control-with-rc)的输出。
- `Gimbal Roll`：输出控制云台滚转。
- `Gimbal Pitch`：输出控制云台俯仰。
- `Gimbal Yaw`：输出控制云台偏航。
- `Gripper`<Badge type="tip" text="PX4 v1.14" />：输出控制夹爪开合。
- `Landing_Gear_Wheel`<Badge type="tip" text="PX4 v1.14" />：输出控制起落架轮子展开

以下功能仅适用于FMU输出：

- `Camera_Trigger`：输出触发相机。在 [`TRIG_MODE==0`](../advanced_config/parameter_reference.md#TRIG_MODE) 时启用。通过 `TRIG_*` 参数配置。
- `Camera_Capture`：输入获取图像捕获通知。在 [CAM_CAP_FBACK==0](../advanced_config/parameter_reference.md#CAM_CAP_FBACK) 时启用。通过 `CAM_CAP_*` 参数配置。
- `PPS_Input`：每秒脉冲输入捕获。用于GPS同步。在 [`PPS_CAP_ENABLE==0`](../advanced_config/parameter_reference.md#PPS_CAP_ENABLE) 时启用

::: info
功能定义在源码中：[/src/lib/mixer_module/output_functions.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/mixer_module/output_functions.yaml)
该列表在PX4 v1.15时有效。
:::

## 执行器测试

右下角的 _Actuator Testing_ 部分提供滑块，可用于测试（并确定）执行器和电机的设置。  
每个在 [Actuator Outputs](#actuator-outputs) 部分定义的输出都有一个对应的滑块。  
下图示例展示了典型 VTOL Tiltrotor 机体的滑块区域。

![Actuator Testing Slider](../../assets/config/actuators/vtol_tiltrotor_sliders_example.png)

该部分包含一个必须切换的 **启用滑块** 开关，切换后滑块方可使用。  
滑块可使电机/舵机在整个运动范围内工作，并能锁定在未激活和最小位置。

::: info
切换 **启用滑块** 开关后，执行器/电机不会有任何动作，直到对应的滑块被移动。  
这是安全功能，用于防止开关启用后电机突然运动。
:::

滑块可用于验证以下内容：

1. 执行器（电机、舵面等）已分配到预期的输出端口  
1. 电机在未激活 PWM 输出值时不会旋转  
1. 电机在最小 PWM 输出值时仅轻微旋转  
1. 电机在预期方向上产生正向推力  
1. 舵面在未激活输出值时处于正确的空闲位置  
1. 舵面的运动方向符合 [舵面偏转约定](#control-surface-deflection-convention) 的定义  
1. 电机倾转舵机在未激活输出值时处于正确的空闲位置  
1. 电机倾转舵机的运动方向符合 [倾转舵机坐标系](#tilt-servo-coordinate-system) 的定义输出分配与配置  

在分配输出或进行任何测试前，请移除螺旋桨。  
紧急停止按钮必须按下后才允许执行器测试。  
即使启用了紧急停止开关，电机仍可立即停止。  
舵机不会实际移动，直到对应的滑块被更改。  
参数 [COM_MOT_TEST_EN](../advanced_config/parameter_reference.md#COM_MOT_TEST_EN) 可完全禁用执行器测试。  
在终端中，[actuator_test](../modules/modules_command.md#actuator-test) 也可用于执行器测试。  
VTOL在**固定翼飞行**期间会自动关闭朝上的电机：  
- 标准 VTOL：定义为多旋翼电机的电机将被关闭  
- 倾转旋翼：没有关联倾转舵机的电机将关闭  
- 尾坐式飞行器在固定翼飞行时不会关闭任何电机  

### 电机反转  

电机必须按配置几何中定义的方向旋转（"**Direction CCW**" 复选框）。  
如果任何电机未按正确方向旋转，必须进行反转。  

有以下几种方法：  

- 如果 ESC 配置为 [DShot](../peripherals/dshot.md)，可通过 UI 永久反转方向。  
  **设置旋转方向** 按钮显示在执行器滑块下方（如果使用 DShot 电机）。  
  这些按钮弹出一个对话框，您可选择要应用方向的电机。  

  ![设置旋转方向按钮](../../assets/config/actuators/reverse_dshot.png)  

  注意：当前方向无法查询，因此可能需要尝试两种选项。  

- 交换三根电机线中的两根（交换哪两根无关紧要）。  

  ::: info  
  如果电机不是通过子弹头连接器连接的，需要重新焊接（这也是为什么更推荐使用 DShot ESC 的原因之一）。  
  :::