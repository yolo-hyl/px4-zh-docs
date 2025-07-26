

# 执行器配置与测试

<Badge type="tip" text="PX4 v1.14" />

_Actuators Setup_ 视图用于自定义机体的具体几何结构，将执行器和电机分配到飞控输出，并测试执行器和电机响应。

## 概览

在 _QGroundControl_ 中打开视图：**"Q" (应用程序菜单) > 机体设置 > 执行器** (标签页)。  
显示的元素取决于 [选定的机体类型](../config/airframe.md)，默认映射方式如 [机体参考手册](../airframes/airframe_reference.md) 所示。

该视图包含三个部分：

- [几何结构](#几何结构): 为 [选定的机体](../config/airframe.md) 配置几何参数。  
  包括 [电机](#电机几何) 的数量、位置和属性，以及 [控制面](#控制面几何设置) 和 [电机倾斜舵机](#电机倾斜伺服几何结构) 的数量和属性。
- [执行器输出](#执行器输出): 将电机、控制面和其他执行器分配到特定输出。
- [执行器测试](#执行器测试): 测试电机和执行器是否按预期方向/速度移动。

四旋翼飞行器的设置界面可能与下图类似。  
这定义了一个四旋翼飞行器，采用X型布局。  
它将4个电机映射到AUX1到AUX4输出，并指定连接到DShot1200电调。  
同时将PWM400 AUX输出用于控制降落伞和起落架。

![执行器 MC (QGC)](../../assets/config/actuators/qgc_actuators_mc_aux.png)

::: info
默认仅显示最常用的设置。  
点击右上角的 **Advanced** 复选框以显示所有设置。
:::

## 几何结构

几何结构部分用于为所选[机体](../config/airframe.md)设置任何可配置的几何相关参数。  
这包括[电机](#电机几何)的数量和位置，以及[控制面](#控制面几何设置)的数量、功能和属性。  
对于垂直起降倾转旋翼机体，还包括[倾转舵机](#电机倾斜伺服几何结构)的数量和属性。

::: info  
用户界面根据所选机体类型进行了定制：  

- 仅显示所选机体类型可配置的字段；机体不可配置的字段将被隐藏。  
- 电机位置图目前仅针对多旋翼框架显示。  
:::

### 电机几何

电机几何部分可让您设置电机数量、相对位置以及每个电机的其他属性。

大多数电机属性适用于所有机架。少数属性仅适用于特定机架。例如，`Tilted-by` 和 `axis` 仅适用于 [倾转旋翼VTOL](#motor-geometry-vtol-tiltrotor) 和 [标准VTOL](#motor-geometry-standard-vtol) 机体。

多旋翼机架的几何配置会提供一个图表，显示每个电机的相对x,y位置。有关其他机架电机位置的总体了解，请参阅[Airframe Reference](../airframes/airframe_reference.md)。

关于核心几何概念以及多种不同机架的配置，请参阅以下各节。

#### 电机几何结构：多旋翼

下图展示了四旋翼多旋翼机架的几何结构设置（含/不含高级设置）。

![Geometry MC (QGC)](../../assets/config/actuators/qgc_actuators_mc_geometry_marked.png)

首先，**电机**下拉设置允许你选择电机数量（上述示例中为4个）。

对于每个电机，你可以设置以下参数：

- `坐标X`: [坐标X](#电机位置坐标系统)，单位为米。
- `坐标Y`: [坐标Y](#电机位置坐标系统)，单位为米。
- `坐标Z`: [坐标Z](#电机位置坐标系统)，单位为米。
- (高级) `方向逆时针`: 复选框，指示电机逆时针旋转（未选中表示顺时针）。
- (高级) `双向`: 复选框，指示电机为[双向](#双向电机)
- (高级) `输出斜率`: 设置电机输出达到最大值所需的最小时间。
  更多信息请参阅[舵面几何结构](#控制面几何设置)部分

::: info
`X`、`Y`、`Z`坐标基于[FRD坐标系，相对于_重心_](#电机位置坐标系统)。
注意，这可能与飞控器的位置不同！
:::

#### 电机几何结构：垂直起降四旋翼尾座机

[垂直起降四旋翼尾座机](../airframes/airframe_reference.md#vtol-tailsitter)的电机几何结构如下所示（配置其他尾座机垂直起降机体的方法将类似）。

电机具有与[多旋翼几何结构](#motor-geometry-multicopter)相同的配置字段。

![Geometry motor: tailsitter vtol](../../assets/config/actuators/qgc_geometry_tailsitter_motors.png)

#### 电机几何：VTOL倾斜旋翼

[通用四旋翼垂直起降倾斜旋翼机](../airframes/airframe_reference.md#vtol_vtol_tiltrotor_generic_quadplane_vtol_tiltrotor)的电机几何结构如下所示（配置其他[VTOL倾斜旋翼机](../airframes/airframe_reference.md#vtol_vtol_tiltrotor_generic_quadplane_vtol_tiltrotor)的方法类似）。

![电机几何：倾斜旋翼VTOL](../../assets/config/actuators/qgc_geometry_tiltrotor_motors.png)

- `倾斜角度`：用于倾斜电机的相关舵机。
  该舵机的属性在[Motor Tilt Servo Geometry](#电机倾斜伺服几何结构)中定义。

#### 电机布局：标准垂直起降飞行器（VTOL）

[常规标准垂直起降飞行器](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)的电机布局如下所示（其他"标准垂直起降飞行器"的配置方法类似）。

![电机布局：标准垂直起降飞行器](../../assets/config/actuators/qgc_geometry_standard_vtol_motors.png)

电机的大部分配置字段与[多旋翼布局](#motor-geometry-multicopter)相同。新增一个字段用于指示电机如何移动机体（对于标准垂直起降飞行器，悬停电机通常设置为"向上"，推进电机设置为"向前"）。

- `轴`: 可选 `向上`、`向下`、`向前`、`向后`、`向左`、`向右`、`自定义`
  - 如果选择`自定义`，界面会显示三个额外字段用于设置电机方向

#### 电机布局：其他机体类型

其他机体类型会根据其机身类型定义合适的电机布局。同样，这些电机通常具有与上述类似的属性。

例如，固定翼机体可能只有一个推进电机，而配备差速转向的rover则会有一个油门电机和一个转向电机。

#### 电机位置坐标系统

电机位置的坐标系统为 FRD（机体坐标系），其中 X 轴指向机体前方，Y 轴指向机体右侧，Z 轴指向机体下方。

坐标原点为机体的 **重心 (COG)**。
这可能 **与自动驾驶仪的位置不同**。

![执行器重心参考示意图](../../assets/config/actuators/quadcopter_actuators_cg_reference.png)

#### 双向电机

某些机体可能使用双向电机（即支持双向旋转的电机）。例如，需要前进和后退的地面车辆，或具有可双向旋转推进电机的垂直起降（VTOL）机体。

如果使用双向电机，请确保为这些电机选中**可逆**复选框（该选项显示为"高级"选项）。

![Reversible](../../assets/config/actuators/qgc_geometry_reversible_marked.png)

请注意，还需确保双向电机对应的电调配置正确（例如为DShot电调启用3D模式，可通过[DShot命令](../peripherals/dshot.md#commands)实现）。

### 控制面几何设置

控制面几何设置部分允许您设置机体上存在的控制面数量和类型。在某些情况下，您可能还需要设置中立值和速率限制值。高级用户还可以配置滚转缩放、偏航缩放和俯仰缩放（通常默认值即可接受，因此通常不需要设置）。下面展示了一个配备两个副翼的机体控制面设置示例。请注意，副翼仅影响滚转，因此俯仰和偏航字段被禁用。

![控制面设置示例](../../assets/config/actuators/control_surfaces_geometry.png)

::: info
默认情况下仅显示最常见的设置。点击视图右上角的 **Advanced** 复选框以显示所有设置。
:::

字段说明：

- `Control Surfaces`: 控制面数量（请首先设置此值！）
- `Type`: 每个控制面的类型：`LeftAileron`、`RightAileron`、`Elevator`、`Rudder`、`Left Elevon`、`Right Elevon`、`Left V-Tail`、`Right V-Tail`、`Left Flap`、`Right Flap`、`Airbrakes`、`Custom`。
- `Roll Torque`: 执行器围绕滚转轴的效能（归一化值：-1 到 1）。
  [通常应使用默认执行器值](#actuator-roll-pitch-and-yaw-scaling)。
- `Pitch Torque`: 执行器围绕俯仰轴的效能（归一化值：-1 到 1）。
  [通常应使用默认执行器值](#actuator-roll-pitch-and-yaw-scaling)。
- `Yaw Torque`: 执行器围绕偏航轴的效能（归一化值：-1 到 1）。
  [通常应使用默认执行器值](#actuator-roll-pitch-and-yaw-scaling)。
- `Trim`: 添加到执行器的偏移量，使其在无输入时居中。
  该值可能需要通过试验确定。
- <a id="slew_rate"></a>(高级) `Slew Rate`: 限制电机/舵机信号通过全输出范围的最小时间（单位：秒）。
  - 该设置限制执行器的速率变化（若未指定则无速率限制）。适用于快速移动可能导致损坏或飞行扰动的执行器，例如倾转旋翼VTOL机体的倾转执行器或快速襟翼。
  - 例如，设置为2.0表示电机/舵机在2秒内不会从0到1完成移动（对于可逆电机，范围为-1到1）。
- (高级) `Flap Scale`: 该执行器在"全襟翼配置"下的偏转量 [0, 1]（见下文[Flap Scale and Spoiler Scale Configuration](#襟翼比例和扰流板比例配置)）。
  可用于将气动面配置为襟翼，或补偿主襟翼产生的扭矩。
- (高级) `Spoiler Scale`: 该执行器在"全扰流板配置"下的偏转量 [0, 1]（见下文[Flap Scale and Spoiler Scale Configuration](#襟翼比例和扰流板比例配置)）。
  可用于将气动面配置为扰流板，或补偿主扰流板产生的扭矩。
- (VTOL专用) `Lock control surfaces in hover`:
  - `Enabled`: 大多数机体在悬停时不使用控制面。启用此设置可锁定它们，使其不影响机体动力学。
  - `Disabled`: 适用于悬停时使用控制面的机体（如双尾座无翼机使用升降副翼进行俯仰和偏航控制），或在高速移动或强风中悬停模式下使用控制面提供额外稳定性的机体。

#### 襟翼比例和扰流板比例配置

"襟翼控制"和"扰流板控制"是气动配置，可以由飞行员手动指令（如通过遥控器），或由控制器自动设定。  
例如，飞行员或着陆系统可能会启用"扰流板控制"以在着陆前降低空速。

这些配置是控制器告知分配器如何调整机翼气动特性的一种_抽象_方式，相对于"全襟翼"或"全扰流板"配置（范围在`[0,1]`之间，其中"1"表示完全展开）。  
分配器随后使用任何可用的控制面（通常是襟翼、副翼和升降舵）来实现所需的配置。

在执行器界面中，`襟翼比例`和`扰流板比例`设置告知分配器副翼、升降舵、襟翼、扰流板和其他控制面在"襟翼控制"和/或"扰流板控制"指令中的贡献程度。  
具体来说，它们指示当控制器要求"全襟翼"或"全扰流板"时，每个控制面应偏转多少。

在以下示例中，机体具有两个副翼、一个升降舵、一个方向舵和两个襟翼作为控制面：

![襟翼和扰流板执行器配置示例](../../assets/config/actuators/qgc_actuators_tab_flaps_spoiler_setup.png)

- 襟翼的`襟翼比例`均设置为1，表示当襟翼控制为1时将完全偏转。  
  它们的[偏转速率](#slew_rate)设为0.5/s，因此完全偏转需要2秒（通常建议在襟翼上设置偏转速率以减少其运动产生的干扰）。
- 副翼主要负责提供指令的滚转力矩。  
  它们的`扰流板比例`设为0.5，当控制器要求全扰流板配置时，副翼会额外向上偏转50%。  
  因此副翼偏转是滚转力矩的（非对称）偏转与扰流板目标点的（对称）偏转之和。
- 升降舵主要负责提供俯仰力矩。  
  它的`襟翼比例`和`扰流板比例`字段也有非零值。  
  这些值表示为补偿襟翼和扰流板执行器产生的俯仰力矩而增加的升降舵偏转。  
  在此示例中，当襟翼完全展开时升降舵会向上偏转0.3以抵消襟翼引起的下俯力矩。

#### 执行器翻滚、俯仰和偏航缩放

::: info
对于大多数机身配置，每种控制面类型的默认值不应更改。
:::

`翻滚缩放`、`俯仰缩放`和`偏航缩放`值表示执行器绕相应轴的归一化有效性。

调整这些值属于低级/高级主题，通常仅在调整耦合控制面（如同时控制俯仰和翻滚的升降副翼elevon）时才需要。在这种情况下，您需要了解以下内容：

- 输入的数值会直接写入分配矩阵，该矩阵随后被求逆以从期望力矩（归一化）转换为控制信号。
- 增加缩放值会_减少_控制面的偏转量（因为求逆过程）。

<!-- For more information see: []() (PX4 Dev Summit, 2022) -->

#### 舵面偏转约定

可从中立位置双向移动的舵面包括：副翼、升降副翼、V型尾翼、A型尾翼、方向舵。

为了确保这些舵面在控制器正输入或负输入时始终按预期移动，需要定义一个与舵机物理设置无关的偏转方向标准。

正输入导致正偏转。
下图显示了正输入的移动方向：

![舵面偏转](../../assets/config/actuators/plane_control_surface_convention.png)

总结而言，正输入会导致：

- **水平舵面：** 向上移动。
  包括副翼和升降副翼。
- **垂直舵面：** 向右移动。
  包括方向舵。
- **混合舵面：** 向上/向右移动。
  包括V型尾翼、A型尾翼

::: tip
只能从中立点单向偏转的舵面包括：减速板、扰流板和襟翼。

对于这些舵面，正输入始终表示从中立点偏转（0:无效果，1:完全效果），无论舵面本身移动方向如何。
它们不会响应负输入。
:::

<!-- 参见此注释：https://github.com/PX4/PX4-Autopilot/blob/96b03040491e727752751c0e0beed87f0966e6d4/src/modules/control_allocator/module.yaml#L492 -->

### 电机倾斜伺服几何结构

[VTOL 倾斜旋转翼机体](../frames_vtol/tiltrotor.md)可以通过倾斜电机在悬停和前飞之间转换。
本节定义倾斜伺服的属性。
这些属性在倾斜旋转翼的电机几何结构中被映射到具体电机。

以下示例展示了[上述倾斜旋转翼电机几何结构](../config/actuators.md#motor-geometry-vtol-tiltrotor)的倾斜伺服设置。

![倾斜伺服几何结构配置示例](../../assets/config/actuators/tilt_servo_geometry_config.png)

可配置的参数包括：

- `Tilt servos`：伺服的数量（可倾斜电机）。
- `Angle at min tilt`：[最大倾斜角度](#倾斜伺服坐标系统)，单位为度，相对于z轴。
- `Angle at max tilt`：[最小倾斜角度](#倾斜伺服坐标系统)，单位为度，相对于z轴。
- `Tilt direction`：`Towards front`（正x方向）或`Towards right`（正y方向）。
- `Use for control`：[倾斜伺服用于偏航/俯仰控制](#tilt-servos-for-yaw-pitch-control)
  - `None`：不使用扭矩控制。
  - `Yaw`：使用倾斜伺服控制偏航。
  - `Pitch`：使用倾斜伺服控制俯仰。
  - `Both Yaw and Pitch`：使用倾斜伺服同时控制偏航和俯仰。

#### 倾斜伺服坐标系统

倾斜转子角度的坐标系统如下所示。
倾斜角度的参考方向为垂直向上（0度）。
机体前方或右侧方向的倾斜角度为正值，后方或左侧方向为负值。

![Tilt Axis](../../assets/config/actuators/tilt_axis.png)

`Angle at min tilt` 和 `Angle at max tilt` 表示倾斜伺服的运动范围。
最小倾斜角度是两个角度中数值较小的那个（非绝对值）。

如果最大/最小倾斜向量为 **P<sub>0</sub>** 和 **P<sub>1</sub>**（如上图所示），两个倾斜角度均为正但 **θ<sub>0</sub>** 更小：

- `Angle at min tilt` = **θ<sub>0</sub>**
- `Angle at max tilt` = **θ<sub>1</sub>**

::: info
如果图示被镜像使得 **P<sub>0</sub>** 和 **P<sub>1</sub>** 倾斜到 -x, -y 象限，则两个倾斜角度均为负值。
由于 **θ<sub>1</sub>** 比 **θ<sub>0</sub>** 更负（更小），它将成为 `Angle at min tilt`。

同样，一个伺服从：

- 直立到前倾位置时，`min=0` 且 `max=90`。
- 直立位置对称45度时，`min=-45` 且 `max=45`
- 直立到后倾位置时，`min=-90` 且 `max=0`。
  :::

`Tilt direction` 表示伺服在机体 `Front`（前方）或 `Right`（右侧）平面的倾斜方向。
在图示中这由 **α** 表示，其值只能为 0（前方）或 90（右侧）。

#### 倾斜舵机用于偏航/俯仰控制

倾斜舵机可以在一个或多个轴上提供扭矩，用于实现机体的偏航或俯仰：

- 偏航通常通过这种方式设置，尽管四轴或更多电机的机体更常用电机控制。
- 俯仰更常通过差分电机推力控制。
  使用倾斜舵机控制在无法使用差分推力的机体上非常有用，例如[Bicopter](https://www.youtube.com/watch?v=hfss7nCN40A)。

是否启用此功能由 `Use for control` 设置配置。

## 执行器输出

_Actuator Outputs_ 部分用于将电机、控制面舵机以及其他用于特定机体的执行器分配到飞控器的物理输出，并设置这些输出的参数。

![执行器输出 - 多旋翼示意图](../../assets/config/actuators/qgc_actuators_mc_outputs.png)

为连接的飞控器支持的每个输出总线显示单独的标签页：PWM MAIN（I/O板输出）、PWM AUX（FMU板输出）、UAVCAN。

电机和执行器（称为"[functions](#输出功能)"）可以分配到任何可用总线上的任意物理输出。

::: info
使用PWM AUX输出控制电机比PWM MAIN输出更优（它们延迟更低）。
:::

PWM输出根据硬件定时器组进行分组。这意味着同一组中的所有输出必须以相同协议和速率运行（例如，一组中所有输出均为400Hz的PWM信号）。因此无法将舵机和电机映射到同一输出组，因为它们通常以不同速率运行。

PWM AUX标签页包含CAP输出，通常用作[相机捕获/触发输入](../camera/fc_connected_camera.md#trigger-configuration)。但你可以将CAP输出映射到其他输出功能，其他AUX输出也可用作相机捕获/触发输入。

::: info
配置相机捕获/触发输入需要重启才能生效
:::

你应该将功能分配给与电机和舵机物理接线匹配的输出，并使用下面描述的[执行器测试](#执行器测试)部分来确定合适的输出参数值。这些步骤在[输出分配与配置](#输出分配与配置)中进行了说明。

### 输出功能

输出功能用于将机体的逻辑功能（如 `Motor 1` 或 `Landing gear`）映射到物理输出（如 FMU 输出引脚 2）。这使得几乎任何用途都可以灵活使用特定输出引脚。

某些功能仅适用于特定机体或输出类型，其他类型将不显示这些功能。

功能包括：

- `Disabled`: 输出未分配功能。
- `Constant_Min`: 输出设置为恒定最小值 (-1)。
- `Constant_Max`: 输出设置为恒定最大值 (+1)。
- `Motor 1` 至 `Motor 12`: 指定电机输出。仅显示机体允许的电机。
- `Servo 1` 至 `Servo 8`: 舵机输出。根据机体类型进一步分配具体含义，如 "倾斜舵机"、"左副翼"。
- `Peripheral via Acutator Set 1` 至 `Peripheral via Acutator Set 6`: [通过MAVLink的通用执行器控制](../payloads/generic_actuator_control.md#generic-actuator-control-with-mavlink)。
- `Landing Gear`: 输出用于起落架。
- `Parachute`: 输出用于降落伞。正常使用时发送最小值，触发紧急保护时发送最大值。
- `RC Roll`: 输出通过RC传递的滚转（[RC_MAP_ROLL](../advanced_config/parameter_reference.md#RC_MAP_ROLL) 将RC通道映射到此输出）。
- `RC Pitch`: 输出通过RC传递的俯仰（[RC_MAP_PITCH](../advanced_config/parameter_reference.md#RC_MAP_PITCH) 将RC通道映射到此输出）。
- `RC Throttle`: 输出通过RC传递的油门（[RC_MAP_THROTTLE](../advanced_config/parameter_reference.md#RC_MAP_THROTTLE) 将RC通道映射到此输出）。
- `RC Yaw`: 输出RC的偏航（[RC_MAP_YAW](../advanced_config/parameter_reference.md#RC_MAP_YAW) 将RC通道映射到此输出）。
- `RC Flaps`: 输出RC的襟翼（[RC_MAP_FLAPS](../advanced_config/parameter_reference.md#RC_MAP_FLAPS) 将RC通道映射到此输出）。
- `RC AUXn` 至 `RC AUX1`: 用于[通过RC传递的任意载荷](../payloads/generic_actuator_control.md#generic-actuator-control-with-rc)的输出。
- `Gimbal Roll`: 输出控制云台滚转。
- `Gimbal Pitch`: 输出控制云台俯仰。
- `Gimbal Yaw`: 输出控制云台偏航。
- `Gripper`<Badge type="tip" text="PX4 v1.14" />: 输出控制夹爪开合。
- `Landing_Gear_Wheel`<Badge type="tip" text="PX4 v1.14" />: 输出控制起落架轮子展开

以下功能仅适用于 FMU 输出：

- `Camera_Trigger`: 输出用于触发相机。当 [`TRIG_MODE==0`](../advanced_config/parameter_reference.md#TRIG_MODE) 时启用。通过 `TRIG_*` 参数配置。
- `Camera_Capture`: 输入用于接收图像捕获通知。当 [CAM_CAP_FBACK==0](../advanced_config/parameter_reference.md#CAM_CAP_FBACK) 时启用。通过 `CAM_CAP_*` 参数配置。
- `PPS_Input`: 每秒脉冲输入捕获。用于GPS同步。当 [`PPS_CAP_ENABLE==0`](../advanced_config/parameter_reference.md#PPS_CAP_ENABLE) 时启用

::: info
功能定义在源代码中的 [/src/lib/mixer_module/output_functions.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/mixer_module/output_functions.yaml)。
此列表在 PX4 v1.15 中有效。
:::

## 执行器测试

右下角的 _执行器测试_ 部分提供了滑块，可用于测试（并确定）执行器和电机的设置。
每个在 [执行器输出](#执行器输出) 部分定义的输出都有一个对应的滑块。
下图示例展示了典型VTOL倾转旋翼机身结构的测试部分。

![执行器测试滑块](../../assets/config/actuators/vtol_tiltrotor_sliders_example.png)

该部分有一个 **启用滑块** 开关，必须切换此开关后滑块才能使用。
滑块可以让电机/舵机在整个运动范围内通电，并且可以"锁定"在解除武装和最小位置。

::: info
切换 **启用滑块** 开关后，执行器/电机不会立即动作，直到对应的滑块被_移动_。
这是一个安全特性，用于防止开关启用后电机突然移动。
:::

滑块可用于验证以下内容：

1. 执行器（电机、控制面等）已分配到预期的输出
1. 在解除武装的PWM输出值时电机不会旋转
1. 在最小PWM输出值时电机仅有微弱旋转
1. 电机在预期方向产生 **正向推力**
1. 控制面在解除武装输出值时处于正确的空闲位置
1. 控制面按 [控制面偏转约定](#舵面偏转约定) 的定义方向移动
1. 电机倾转舵机在解除武装输出值时处于正确的空闲位置
1. 电机倾转舵机按 [倾转舵机坐标系](#倾斜伺服坐标系统) 的定义方向移动

## 输出分配与配置

输出功能的分配与配置在[执行器输出](#执行器输出)部分完成，而[执行器测试](#执行器测试)滑块通常用于确定合适的配置值：

- 已通过PWM输出连接电机的多旋翼机体可使用[识别并分配电机](#multicopter-pwm-motor-assignment)按钮进行半自动电机分配。
- 电机和执行器的输出分配可通过滑块完成/检查（参见[输出分配(手动)](#output-assignment-manual)）。
- 所有输出的禁用、最小值和最大值设置也可通过滑块确定。
  这部分内容在[电机配置](#电机配置)、[控制面设置](#控制面设置)、[倾转舵机设置](#倾斜舵机设置)中均有展示

### 多旋翼PWM：电机分配

您可以使用 **识别并分配电机** 按钮，通过半自动流程将电机分配到PWM输出。

::: info
这是分配电机最简单的方法，但目前仅支持连接到PWM输出的 **多旋翼机体**（UAVCAN输出和其他机架类型暂不支持此功能）。  
对于其他机架类型，可以参考[手动输出分配](#output-assignment-manual)中的说明。
:::

:::warning
在分配输出或进行任何测试前，请卸下所有螺旋桨。
:::

![识别电机按钮](../../assets/config/actuators/identify_motors_button.png)

点击按钮后，地面站会向电机发送指令使其旋转。您只需在屏幕上选择对应的电机即可完成分配。地面站会依次旋转每个电机供您分配。

操作步骤：

1. 配置电机几何结构以匹配机体上的电机布局。
1. 选择希望分配电机的PWM选项卡。
1. 点击 **识别并分配电机** 按钮。
1. 一个电机将开始旋转（若旋转过快来不及记录，可点击 **再次旋转电机** ）。

   在几何结构部分选择对应的电机。

   ![展示如何识别/分配电机的截图](../../assets/config/actuators/identify_motors_in_progress.png)

1. 完成所有电机分配后，工具将自动设置输出的电机映射关系并退出。

### 输出分配（手动）

:::warning
在分配输出或进行任何测试之前，请取下电机上的螺旋桨。
:::

电机和舵机的执行器输出可以_手动_通过[执行器测试](#执行器测试)部分中的滑块进行分配。

分配执行器的步骤：

1. 首先在“执行器输出”部分中为输出分配功能，这些功能是你认为可能正确的。
1. 在“执行器测试”部分中切换 **启用滑块** 开关。
1. 移动要测试的执行器的滑块：
   - 电机应移动到最小推力位置。
   - 舵机应移动到中间位置附近。
1. 检查机体上哪个执行器移动。
   这应与您的几何结构的执行器位置相匹配（[机架参考](../airframes/airframe_reference.md) 显示了多个标准机架的电机位置）。
   - 如果正确的执行器移动，则进入下一步。
   - 如果错误的执行器移动，请交换输出分配。
   - 如果没有任何移动，请将滑块调整到范围中间位置，如需要，再调高。
     如果之后仍然没有移动，输出可能未连接，电机可能未供电，或者输出可能配置错误。
     需要进行故障排除（可以尝试其他执行器输出以查看是否有任何移动）。
1. 将滑块返回到“解除武装”位置（电机滑块的底部，舵机滑块的中心）。
1. 对所有执行器重复上述步骤

### 电机配置

::: info
如果使用 PWM 或 OneShot 电调，需要先执行 [电调校准](../advanced_config/esc_calibration.md)（此主题也涵盖 PWM 特定电机配置）。

[DShot](../peripherals/dshot.md) 电调不需要配置命令限制，只需配置旋转方向。
:::

:::warning
移除螺旋桨！
:::

电机配置设置输出值，以确保电机：

- 在解除武装时不会转动（在 `disarmed` PWM 输出值时）。
- 在 `minimum` PWM 输出值时缓慢但可靠地开始转动。
- 使用 _最低_ `maximum` PWM 输出值，使电机以 _最高_ 转速运行。
- 在预期方向上产生 **正向推力**。

对于每个电机：

1. 将电机滑块向下滑动至底部位置，使其固定在底部。
   在此位置下，电机输出设置为 `disarmed` 值。
   - 确认电机在此位置不转动。
   - 如果电机仍在转动，请在 [执行器输出](#执行器输出) 部分将对应的 PWM `disarmed` 值调整为低于其仍能转动的水平。
2. 缓慢向上移动滑块，直至其固定在 _minimum_ 位置。
   在此位置，电机输出设置为 `minimum` 值。
   - 确认电机在此位置缓慢旋转。
   - 如果电机未旋转或旋转过快，需要在 [执行器输出](#执行器输出) 中调整对应的 PWM `minimum` 值，使电机仅轻微旋转。
   
     ![PWM 最小输出](../../assets/config/actuators/pwm_minimum_output.png)
     ::: info
     对于 DShot 输出，此步骤不需要。
     :::

3. 将滑块值调高至可验证电机在正确方向旋转，并在预期方向上产生正向推力的水平。
   - 预期推力方向因机体类型而异。
     例如，多旋翼的推力应始终指向上方，而固定翼机体的推力则会向前推动机体。
   - 对于 VTOL，当 [倾转舵机](#倾斜舵机设置) 的角度为 0 度时（根据 [倾转舵机坐标系](#倾斜伺服坐标系统) 的定义），推力应指向上方。
     [倾转舵机](#倾斜舵机设置) 的测试也将在下文进行说明。
   - 如果推力方向错误，可能需要 [反转电机](#电机反转)。

4. 将滑块值调至最大，使电机快速旋转。
   将 PWM 输出的 `maximum` 值调低至略低于默认值。
   以极小（25 微秒）的增量逐步增加该值，并注意电机的音调变化。
   “最佳”最大值是最后一次听到音调变化时的值。

### 控制面设置

首先设置每个输出组中伺服电机的_脉冲频率_。
通常应设置为您的伺服电机支持的最大值。
以下我们展示如何将其设置为PWM50（最常见值）。

![Control Surface Disarmed 1500 Setting](../../assets/config/actuators/control_surface_disarmed_1500.png)

::: info
您很可能需要将脉冲频率从默认的400Hz更改（若不支持该频率，伺服电机通常会发出"奇怪"的噪音）。
如果您使用PWM伺服电机，PWM50要常见得多。
如果确实需要高频伺服电机，DShot方案更具优势。
:::

#### 关于中性点双向运动的控制面

关于中性点双向运动的控制面包括：副翼（ailerons）、升降副翼（elevons）、V形尾翼（V-tails）、A形尾翼（A-tails）和方向舵（rudders）。

设置步骤如下：

1. 设置 `未武装` (Disarmed) 值，确保控制面在未武装状态下保持中性位置。
   对于PWM舵机，通常设置在 `1500`（接近舵机范围的中心值）。

   ![控制面未武装1500设置](../../assets/config/actuators/control_surface_aileron_setup.png)

2. 将控制面滑块向**上**移动（正向指令），验证其运动方向是否符合[控制面运动方向规范](#舵面偏转约定)：

   - 副翼、升降副翼、V形尾翼、A形尾翼等水平面应**向上**运动。
   - 方向舵等"纯垂直"面应**向右**运动。

   ::: tip
   滑块运动方向必须符合控制面运动规范，以统一不同舵机安装方式的控制逻辑（滑块向上可能实际发送给舵机的输出值反而降低）。
   :::

   如果控制面运动方向相反，点击 `Rev Range` 复选框以反转范围。

3. 将滑块再次移回中位，检查控制面是否与机翼中性位置对齐：

   - 若未对齐，可以设置控制面的 **Trim** 值。

     ::: info
     这在Geometry面板的 `Trim` 设置中完成，通常需要"试错法"进行调整。
     ![控制面微调](../../assets/config/actuators/control_surface_trim.png)
     :::

   - 设置Trim值后，将滑块移出中心、释放并返回未武装（中位）位置。
     确认控制面处于中性位置。

::: info
另一种无需使用滑块的测试方法是将 [`COM_PREARM_MODE`](../advanced_config/parameter_reference.md#COM_PREARM_MODE) 参数设为 `Always`：

- 这将在机体未武装时持续启用舵机控制，并始终应用Trim设置到控制面
- 可尝试设置不同的Trim值检查对齐效果，最终确定满意值即可。

#### 从中性位置移动到完全偏转的控制面

仅从中性位置单向移动的控制面包括：减速板、扰流板和襟翼。

对于这些控制面，您应根据控制面的完整活动范围设置最小值和最大PWM值。
`Disarmed`值应对应控制面处于"中性"位置时的值（最大值或最小值）。
对于襟翼而言，中性位置是襟翼完全收回并与机翼齐平的状态。

设置这些控制面的一种方法为：

1. 将`Disarmed`设为`1500`，`Min`设为`1200`，`Max`设为`1700`，使这些值大致位于舵机活动范围的中点。
2. 向上移动对应滑块，检查控制面是否移动并处于展开状态（远离disarmed位置）。
   如果未展开，请点击`Rev Range`复选框以反转活动范围。
3. 在disarmed位置启用滑块，然后调整`Disarmed`信号值直到控制面完全收回/与机翼齐平。
   这可能需要增加或减少`Disarmed`值：
   - 如果值减小到接近`Min`，则将`Min`设为与`Disarmed`相同。
   - 如果值增加到接近`Max`，则将`Max`设为与`Disarmed`相同。
4. 您未设置为与`Disarmed`相同的值将控制控制面最大可展开程度。
   将滑块调至控制范围顶端，然后调整值（`Max`或`Min`）使滑块在顶端时控制面完全展开。

::: info 关于襟翼的特殊说明
在某些机体结构中，襟翼可能配置为通过单个输出控制两个襟翼。
在这种情况下，您需要确保提升对应滑块时两个襟翼都能展开。
如果情况并非如此，一个舵机正确展开而另一个未展开，您需要使用第三方舵机编程器修改舵机方向。
或者，您可以将未以正确方向偏转的舵机移动到自己的舵机输出通道，然后通过`Rev range`复选框反转其方向。
:::

### 倾斜舵机设置

首先为每组输出中使用的舵机设置信号频率。  
通常应设置为您的舵机支持的最大值。  
以下示例中设置为PWM50（最常用值）。  
注意，此设置部分与上方控制面的设置相同。

![Tilt Servo Setup](../../assets/config/actuators/tilt_servo_setup.png)

对于每个倾斜舵机：

1. 设置 `Disarmed` 值（例如 PWM 舵机使用 `1000` 或 `2000`），以便在未上锁时舵机处于预期方向。
2. 将舵机滑块置于最低位置，并验证正值增加会指向 `Angle at Min Tilt`（在几何部分中定义）。

   ![Tilt Servo Geometry Setup](../../assets/config/actuators/tilt_servo_geometry_config.png)

3. 将舵机滑块置于最高位置，并验证正向电机推力会指向 `Angle at Max Tilt`（如几何部分中定义）。

### 其他注意事项

- 如果使用了安全按钮，在允许执行器测试之前必须按下它。
- 杀伤开关仍可用于立即停止电机。
- 舵机在对应的滑块被更改之前实际上不会移动。
- 参数 [COM_MOT_TEST_EN](../advanced_config/parameter_reference.md#COM_MOT_TEST_EN) 可用于完全禁用执行器测试。
- 在命令行中，[actuator_test](../modules/modules_command.md#actuator-test) 也可用于执行器测试。
- VTOL会在**固定翼飞行**期间自动关闭朝上的电机：
  - 标准VTOL：定义为多旋翼电机的电机将被关闭
  - 倾转旋翼机：没有关联倾转舵机的电机将被关闭
  - 垂直起降固定翼飞机在固定翼飞行中不会关闭任何电机

### 电机反转

电机必须按照配置的几何方向旋转（"**方向 CCW**" 复选框）。
如果有电机未按正确方向旋转，必须进行反转。

有以下几种方法：

- 如果电调配置为 [DShot](../peripherals/dshot.md)，可以通过 UI 永久反转方向。
  **设置旋转方向** 按钮显示在执行器滑块下方（如果使用 DShot 电机）。
  点击这些按钮会弹出一个对话框，您可以选择要应用方向的电机。

  ![设置旋转方向按钮](../../assets/config/actuators/reverse_dshot.png)

  注意当前方向无法查询，因此可能需要尝试两种选项。

- 交换三根电机线缆中的两根（任意交换均可）。

  ::: info
  如果电机未通过 bullet-connectors 连接，则需要重新焊接（这也是偏好使用 DShot 电调的原因之一）。
  :::