# ROS 2

[ROS 2](https://docs.ros.org/en/humble/#) 是一个强大的通用机器人库，可与 PX4 自动驾驶仪结合使用，创建强大的无人机应用。

:::warning 提示
PX4 开发团队强烈建议您使用/迁移至此版本的 ROS！

这是 [ROS](http://www.ros.org/)（机器人操作系统）的新版本。  
它显著改进了 ROS "1"，特别是与 PX4 的集成深度和延迟更低。
:::

ROS 拥有一个活跃的开发者生态系统，解决常见的机器人问题，并可访问为 Linux 编写的其他软件库。  
例如，可用于 [计算机视觉](../computer_vision/index.md) 解决方案。

ROS 2 能够与 PX4 深度集成，以至于您可以在 ROS 2 中创建与内部 PX4 模式不可区分的飞行模式，并以高频率直接读取和写入内部 uORB 主题。  
当需要从伴飞计算机进行控制和通信（低延迟很重要），利用 Linux 的现有库，或编写新的高级飞行模式时，推荐使用此版本。

ROS 2 与 PX4 之间的通信使用了实现了 [XRCE-DDS 协议](../middleware/uxrce_dds.md) 的中间件。  
该中间件将 PX4 [uORB 消息](../msg_docs/index.md) 曝光为 ROS 2 消息和类型，从而允许 ROS 2 工作流和节点直接访问 PX4。  
该中间件使用 uORB 消息定义生成代码，用于序列化和反序列化进入和离开 PX4 的消息。  
这些相同的消息定义在 ROS 2 应用程序中被使用，以允许消息被解析。

::: info
ROS 2 也可以通过 [MAVROS](https://github.com/mavlink/mavros/tree/ros2/mavros) 而不是 XRCE-DDS 连接到 PX4。  
此选项由 MAVROS 项目支持（此处未文档化）。
:::

要有效使用 [ROS 2](../ros2/user_guide.md) 经过 XRCE-DDS，您（在撰写本文时）必须对 PX4 内部架构和约定有合理了解，这些与 ROS 使用的架构和约定不同。  
在不久的将来，我们计划提供 ROS 2 API 以抽象 PX4 约定，并提供示例来演示其用法。

## 话题

本节主要话题包括：

- [ROS 2 用户指南](../ros2/user_guide.md)：以 PX4 为中心的 ROS 2 概述，涵盖安装、设置以及如何构建与 PX4 通信的 ROS 2 应用。
- [ROS 2 机外控制示例](../ros2/offboard_control.md)：C++ 教程示例，演示如何通过 ROS 2 节点在 [机外模式](../flight_modes/offboard.md) 下进行位置控制。
- [ROS 2 多机模拟](../ros2/multi_vehicle.md)：通过单个 ROS 2 代理连接到多实例 PX4 模拟的说明。
- [PX4 ROS 2 接口库](../ros2/px4_ros2_interface_lib.md)：一个 C++ 库，简化了从 ROS 2 与 PX4 的交互。  
  可用于创建和注册使用 ROS2 编写的飞行模式，并从 ROS2 应用程序（如 VIO 系统）发送位置估计。
- [ROS 2 消息转换节点](../ros2/px4_ros2_msg_translation_node.md)：一个 ROS 2 消息转换节点，启用在 PX4 和使用不同消息版本集编译的 ROS 2 应用之间通信。

## 更多信息

- [XRCE-DDS（PX4-ROS 2/DDS 桥接）](../middleware/uxrce_dds.md)：PX4 中用于连接到 ROS 2 的中间件。