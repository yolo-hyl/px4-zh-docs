# PX4 系统架构

以下部分对两种"典型"PX4系统的PX4硬件和软件堆栈进行了高层概述；一种仅包含飞控，另一种包含飞控和伴飞计算机（也称为"任务计算机"）。

::: info
[PX4 Architectural Overview](../concept/architecture.md) 提供了关于飞控堆栈和中间件的信息。
离板API在[ROS](../ros/index.md) 和 [MAVSDK](https://mavsdk.mavlink.io/main/en/) 中有介绍。
:::

## 飞行控制器（仅）

下图提供了基于飞控的典型"简单"PX4系统的高层概述。

![PX4架构 - 仅飞控系统](../../assets/diagrams/px4_arch_fc.svg)

<!-- 绘图源文件: https://docs.google.com/drawings/d/1_2n43WrbkWTs1kz0w0avVEeebJbfTj5SSqvCmvSOBdU/edit -->

硬件组成包括：

- [飞行控制器](../flight_controller/index.md)（运行PX4飞控堆栈）。通常包含内部IMU、指南针和气压计。
- [电机电调](../peripherals/esc_motors.md) 连接到 [PWM输出](../peripherals/pwm_escs_and_servo.md)、[DroneCAN](../dronecan/escs.md)（DroneCAN允许双向通信，而非图示的单向）或其它总线。
- 传感器（[GPS](../gps_compass/index.md)、[指南针](../gps_compass/index.md)、距离传感器、气压计、光流、气压计、ADSB应答器等）通过I2C、SPI、CAN、UART等连接。
- [相机](../camera/index.md) 或其他载荷。相机可通过PWM输出或MAVLink连接。
- [遥测电台](../telemetry/index.md) 用于连接地面站计算机/软件。
- [遥控系统](../getting_started/rc_transmitter_receiver.md) 用于手动控制

图的左侧展示了软件堆栈，与图中的硬件部分基本水平对齐。

- 地面站计算机通常运行 [QGroundControl](../getting_started/px4_basic_concepts.md#qgc)（或其他地面站软件）。
  也可能运行机器人软件如 [MAVSDK](https://mavsdk.mavlink.io/) 或 [ROS](../ros/index.md)。
- 飞控上运行的PX4飞控堆栈包括 [驱动](../modules/modules_driver.md)、[通信模块](../modules/modules_communication.md)、[控制器](../modules/modules_controller.md)、[估计器](../modules/modules_controller.md) 和其他 [中间件及系统模块](../modules/modules_main.md)。

## 飞行控制器和伴飞计算机

下图展示了包含飞控和伴飞计算机的PX4系统（此处称为"任务计算机"）。

![PX4架构 - 飞控 + 伴飞计算机](../../assets/diagrams/px4_arch_fc_companion.svg)

<!-- 绘图源文件: https://docs.google.com/drawings/d/1zFtvA_B-BmfmxFmAd-XIvAZ-jRqOydj0aBtqSolBcqI/edit -->

飞控运行标准的PX4飞控堆栈，而伴飞计算机通过 [计算机视觉](../computer_vision/index.md) 提供高级功能。
两个系统通过高速串行或IP链路连接，通常使用 [MAVLink协议](https://mavlink.io/en/) 进行通信。
与地面站和云端的通信通常通过伴飞计算机路由（例如使用 [MAVLink Router](https://github.com/mavlink-router/mavlink-router)（来自Intel））。

PX4系统通常在伴飞计算机上运行Linux操作系统。
Linux比NuttX更适合"通用"软件开发；Linux开发者更多，且已有大量有用的软件（例如计算机视觉、通信、云集成、硬件驱动等）。
伴飞计算机有时运行Android，出于同样的原因。

::: info
图示展示了通过LTE连接云端或地面站的方案，该方法已在多个基于PX4的系统中使用。
PX4不提供专门用于LTE和/或云集成的软件（需要自定义开发）。
:::