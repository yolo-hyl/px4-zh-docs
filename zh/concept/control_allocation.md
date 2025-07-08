# 控制分配（混合）

::: info
控制分配替代了PX4 v1.13及更早版本中使用的传统混合方法。
如需查看PX4 v1.13文档，请参阅：[Mixing & Actuators](https://docs.px4.io/v1.13/en/concept/mixing.html), [Geometry Files](https://docs.px4.io/v1.13/en/concept/geometry_files.html) 和 [Adding a New Airframe Configuration](https://docs.px4.io/v1.13/en/dev_airframes/adding_a_new_frame.html)。
:::

PX4 从核心控制器获取期望的扭矩和推力指令，并将其转换为控制电机或舵机的执行器指令。

转换过程取决于机体的物理几何结构。例如，对于一个"向右转"的扭矩指令：

- 一个每个副翼有1个舵机的飞机，会指令一个舵机输出高值，另一个输出低值。
- 多旋翼通过调整所有电机速度来实现向右偏航。

PX4 将这种转换逻辑（称为"混合"）从姿态/速率控制器中分离出来。这确保了核心控制器无需针对每种机体几何结构进行特殊处理，并显著提高了代码复用性。

此外，PX4 将输出功能到具体硬件输出的映射进行了抽象。这意味着任何电机或舵机都可以分配到几乎任何物理输出口。

<!-- https://docs.google.com/drawings/d/1Li9YhTLc3yX6mGX0iSOfItHXvaUhevO2DRZwuxPQ1PI/edit -->

![混合概览](../../assets/diagrams/mixing_overview.png)

## 执行器控制流程

混合流程模块和uORB主题的概述（点击查看全屏）：

<!-- https://drive.google.com/file/d/1L2IoxsyB4GAWE-s82R_x42mVXW_IDlHP/view?usp=sharing -->

![流程概览](../../assets/concepts/control_allocation_pipeline.png)

说明：

- 速率控制器输出扭矩和推力设定值
- `control_allocator` 模块：
  - 根据配置参数处理不同几何结构
  - 执行混合计算
  - 处理电机故障
  - 发布电机和舵机控制信号
  - 单独发布舵机微调值，以便在[测试执行器](../config/actuators.md#actuator-testing)时作为偏移量添加（使用测试滑块）。
- 输出驱动程序：
  - 处理硬件初始化和更新
  - 使用共享库 [src/libs/mixer_module](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/mixer_module/)。
    驱动程序定义参数前缀（如 `PWM_MAIN`），库将使用该前缀进行配置。
    其主要任务是选择输入主题，并根据用户设置的 `<param_prefix>_FUNCx` 参数值将正确数据分配到输出。
    例如，如果 `PWM_MAIN_FUNC3` 设置为 **Motor 2**，则第3个输出将设置为 `actuator_motors` 中的第2个电机。
  - 输出功能在 [src/lib/mixer_module/output_functions.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/mixer_module/output_functions.yaml) 中定义。
- 如果要从MAVLink控制输出，将相关输出功能设置为 **Offboard Actuator Set x**，然后发送 [MAV_CMD_DO_SET_ACTUATOR](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ACTUATOR) MAVLink命令。

## 添加新几何结构或输出功能

参考 [此提交](https://github.com/PX4/PX4-Autopilot/commit/5cdb6fbd8e1352dcb94bd58918da405f8ff930d7) 了解如何添加新几何结构。
当 [CA_AIRFRAME](../advanced_config/parameter_reference.md#CA_AIRFRAME) 设置为新几何结构时，QGC UI 将自动显示正确的配置界面。

[此提交](https://github.com/PX4/PX4-Autopilot/commit/a65533b46986e32254b64b7c92469afb8178e370) 展示了如何添加新输出功能。
任何uORB主题都可以订阅并分配给某个功能。

请注意，控制分配的参数在 [src/modules/control_allocator/module.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/control_allocator/module.yaml) 中定义。
该文件的模式定义在 [此处](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml#L440=)（特别注意搜索键 `mixer:`）。

## 设置默认机体几何结构

在[添加新机体配置](../dev_airframes/adding_a_new_frame.md)时，设置适当的 [CA_AIRFRAME](../advanced_config/parameter_reference.md#CA_AIRFRAME) 和其他默认混合值。

例如，可在机体配置文件 [13200_generic_vtol_tailsitter](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/airframes/13200_generic_vtol_tailsitter) 中查看：

```sh
...
param set-default CA_AIRFRAME 4
param set-default CA_ROTOR_COUNT 2
param set-default CA_ROTOR0_KM -0.05
param set-default CA_ROTOR0_PY 0.2
...
```

## 几何结构与输出设置

机体选择时，QGroundControl 会从框架配置文件中设置机体的总体几何结构和默认参数：[基本配置 > 机体](../config/airframe.md)。

特定机体和飞控硬件的几何参数和输出映射，则通过 QGroundControl **执行器**设置界面配置：[基本配置 > 执行器配置与测试](../config/actuators.md)。