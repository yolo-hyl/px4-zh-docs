# 无人机应用与API

无人机API允许您编写代码来控制和集成到PX4驱动的机体中，而无需了解机体和飞行栈的内部细节，也无需考虑安全关键行为。

例如，您可能希望创建新的"智能"飞行模式，或自定义地理围栏模式，或集成新硬件。无人机API允许您使用首选的编程语言中的高级指令来实现这些功能，代码可以在[伴随计算机](../companion_computer/index.md)中运行，也可以从地面站运行。在底层，API通过[MAVLink](../middleware/mavlink.md)或[uXRCE-DDS](../middleware/uxrce_dds.md)与PX4通信。

PX4支持以下SDK/机器人工具：

- [MAVSDK](../robotics/mavsdk.md)
- [ROS 2](../ros/index.md)
- [ROS 1](../ros/index.md)

## 我应该使用哪个API？

我们建议在可能的情况下使用MAVSDK，主要是因为它更直观且更容易学习，可以在更多操作系统和性能较低的硬件上运行。

如果您已经熟悉ROS或希望利用现有集成（例如计算机视觉任务），可能会更倾向于使用ROS。总体而言，对于需要非常低延迟或需要比MAVLink提供的更深度集成PX4的任务，ROS可能是更好的选择。

主要区别如下：

- **MAVSDK:**
  - 直观且针对无人机优化，学习曲线小且易于设置。
  - 您可以使用C++、Python、Swift、Java、Go等语言编写应用。
  - 可在资源受限的硬件上运行
  - 可在包括Android、Linux、Windows在内的广泛操作系统上运行。
  - 通过MAVLink通信。
    - 稳定且广泛支持。
    - 仅限于MAVLink服务-所需信息可能未公开。
    - 某些用例的延迟可能过高。
- **ROS 2**
  - 通用机器人API，已扩展以支持无人机集成：
    - 从概念上讲，不如MAVSDK针对无人机优化。
    - 学习曲线较大。
  - 许多现有的库：有助于代码重用。
  - 支持C++和Python库。
  - 在Linux上运行。
  - ROS 2是最新的版本，通过XRCE-DDS连接。
    - DDS接口层允许深度集成到PX4的任何公开为UORB主题的方面（几乎一切）。
    - 可以提供更低的延迟。
    - 仍在开发中：
      - 目前需要比ROS 1更深入的PX4理解。
      - PX4的消息接口在ROS和PX4版本间不保持稳定/维护。

## 已弃用的API

### ROS 1

虽然没有严格弃用，但ROS 1已在其最终LTS版本"Noetic Ninjemys"上，该版本将于2025年5月结束生命周期。这意味着将不再提供新功能或错误修复，甚至2025年后的安全更新也将停止。

ROS 1在PX4上仍然"有效"，因为它使用MAVROS，这是一个作为集成层的MAVLink-ROS抽象。这意味着ROS 1具有MAVLink的所有限制，如较高延迟和小API接口（以及优势，如稳定的接口）。

所有PX4对ROS的投资都集中在与ROS 2的深度集成上。这将使ROS 2应用程序几乎与在PX4本身中运行的代码无法区分。

::: tip
新项目使用ROS 2。
现有项目尽快升级到ROS 2。
:::

### DroneKit

DroneKit-Python是一个用Python编写的MAVLink API。它未针对PX4进行优化，且已多年未维护。使用PX4和DroneKit的遗留文档可在此找到：[PX4 v1.12 > DroneKit](https://docs.px4.io/v1.12/en/robotics/dronekit.html)。

::: tip
[MAVSDK](https://mavsdk.mavlink.io/)是推荐与PX4一起使用的MAVLink API。
它在几乎所有方面都更好：功能、速度、编程语言支持、维护等。
:::