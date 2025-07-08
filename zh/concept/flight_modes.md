# 飞行模式（开发者）

飞行模式（"flight modes"、"drive modes" 等）是控制自动驾驶仪如何响应用户输入和控制机体运动的特殊操作状态。  
根据自动驾驶仪提供的控制级别/类型，它们大致分为 _手动_、_辅助_ 和 _自动_ 模式。  
飞行员通过遥控器上的开关或地面控制站切换飞行模式。

飞行模式可以实现为运行在飞控上的 [PX4 内部模式](#px4-internal-modes)，或运行在伴飞计算机上的 [PX4 外部（ROS2）模式](#px4-external-modes)。  
从地面站（MAVLink）的角度来看，模式的来源是不可区分的。

本主题链接到支持模式的文档，比较 PX4 内部模式和外部模式，提供实现建议，并提供如何通过 MAVLink 使用 PX4 模式的链接。

## 支持的模式

并非所有模式在所有机体类型上都可用（或有意义），某些模式在不同机体类型上的行为可能不同。

PX4 内部模式的模式文档如下：

- [飞行模式（多旋翼）](../flight_modes_mc/index.md)  
- [飞行模式（固定翼）](../flight_modes_fw/index.md)  
- [飞行模式（垂直起降）](../flight_modes_vtol/index.md)  
- [驱动模式（Rover）](../flight_modes_rover/index.md)  
- [基本配置 > 飞行模式](../config/flight_mode.md)

## 内部模式与外部模式

在某些例外情况下，模式可以实现在飞控或伴飞计算机上。  
主要考虑因素如下：

PX4 外部模式不能在以下情况下使用：

- 需要在没有伴飞计算机的机体上运行的模式。  
- 需要低级访问、严格定时和/或高更新率要求的模式。  
  例如，实现直接电机控制的多旋翼模式。  
- 安全关键模式，如 [Return 模式](../flight_modes_mc/return.md)。  
- 当你无法使用 ROS（出于任何原因）时。

其他所有情况下都应考虑外部模式。它们具有以下优势：

- 更容易实现，因为无需处理低级嵌入式约束和要求（如受限的堆栈大小）。  
- 更容易维护，因为集成 API 小、定义明确且稳定。  
- 在 PX4 版本之间移植飞控上的自定义 PX4 模式可能更困难，因为飞行模式通常使用被视为内部的接口，这些接口可能会更改。  
- ROS 2 模式的进程终止会导致回退到内部飞行模式（而内部模式的终止可能会导致机体坠毁）。  
- 它们可以覆盖现有模式以提供更高级的功能。  
  甚至可以覆盖安全关键模式并用更优版本替代：如果 ROS 2 模式崩溃，原始模式将被激活。  
- 提供高级功能，包括更好的特性编程环境，以及许多有用的 Linux 和 ROS 库。  
- 提供更多计算资源以进行更高级的处理（例如计算机视觉）。

请注意，用于创建外部模式的 [PX4 ROS 2 控制接口](../ros2/px4_ros2_control_interface.md) 首次出现在 PX4 v1.15 中，仍被视为实验性功能。  
目前仍有一些限制，但预计会有持续改进。

## PX4 外部模式

PX4 外部模式使用 [PX4 ROS 2 控制接口](../ros2/px4_ros2_control_interface.md)（见链接获取说明）编写在 ROS 2 中。

## PX4 内部模式

<!--
在任何时刻，模式的具体控制行为由 [飞行任务](../concept/flight_tasks.md) 决定。  
一种模式可能会定义一个或多个任务，这些任务定义了模式行为的变体，例如输入是被视为加速度还是速度设定点。

使用的任务通常由参数定义，并在 [src/modules/flight_mode_manager/FlightModeManager.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/flight_mode_manager/FlightModeManager.cpp#L266-L285) 中选择。


命名与飞行模式相关的代码直接相关的模块。  
列出模式必须/应继承的基类。  
解释使模式正常工作所需的核心操作。  
非常高的架构概述。  
-->

### 模式限制

某些模式仅在特定预飞和飞行条件下方有意义。  
例如，手动控制模式在系统没有手动控制器时不应使用。

PX4 模式可以将这些条件指定为 _限制_。  
内部模式的限制类型在 [FailsafeFlags](../msg_docs/FailsafeFlags.md) uORB 主题的 "Per mode requirements" 下列出（如下重复）：

```text
# 每种模式的要求
mode_req_angular_velocity
mode_req_attitude
mode_req_local_alt
mode_req_local_position
mode_req_local_position_relaxed
mode_req_global_position
mode_req_mission
mode_req_offboard_signal
mode_req_home_position
mode_req_wind_and_flight_time_compliance # 如果设置，风速或飞行时间限制超过时无法进入该模式
mode_req_prevent_arming    # 如果设置，在此模式下无法解锁
mode_req_manual_control
mode_req_other             # 其他要求，上述未涵盖（适用于外部模式）
```

如果限制条件不满足：

- 在模式选择期间不允许解锁  
- 已解锁的情况下，无法选择该模式  
- 已解锁且选择该模式时，将触发相关安全措施（例如，手动控制要求的遥控器丢失）。  
  查看 [安全（安全措施）配置](../config/safety.md) 了解如何配置安全措施行为。

这是手动控制标志（`mode_req_manual_control`）的对应流程图：

![模式要求图示](../../assets/middleware/ros2/px4_ros2_interface_lib/mode_requirements_diagram.png)

所有模式的要求在 [src/modules/commander/ModeUtil/mode_requirements.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp#L46) 中的 `getModeRequirements()` 设置。  
添加新模式时，需要在此方法中添加适当的要求。

::: tip
读者可能注意到此图来自 [PX4 ROS2 控制接口 > 安全措施和模式要求](../ros2/px4_ros2_control_interface.md#failsafes-and-mode-requirements)。  
要求和概念相同（尽管定义位置不同）。  
主要区别在于 ROS 2 模式 _推断_ 使用的正确要求，而 PX4 源代码中的模式必须显式指定要求。
:::

## MAVLink 集成

从 PX4 v1.15 开始，PX4 实现了 MAVLink [标准模式协议](../mavlink/standard_modes.md)。  
可用于发现所有模式和当前模式，并设置当前模式。