# MAVSDK

[MAVSDK](https://mavsdk.mavlink.io/main/en/index.html) 是一个 MAVLink SDK，允许您与无人机、摄像头或地面系统等 MAVLink 系统进行通信。

该 SDK 为多种编程语言提供了接口库，并提供了用于管理一个或多个机体的简单 API。  
它提供了对机体信息和遥测数据的程序访问，并对任务、移动和其他操作进行控制。  
它还可用于与 MAVLink 组件通信，例如摄像头、云台和其他硬件。

MAVSDK 库可用于无人机上的协处理器，也可用于地面站或移动设备，可运行在 Linux、macOS、Windows、Android 和 iOS 系统上。

::: info
MAVSDK 比 [ROS 2](../ros2/index.md) 更容易学习，且具有更稳定的 API。  
推荐用于地面站通信、协处理器的低带宽命令/控制，以及与对带宽要求不高的机载组件集成，例如摄像头和云台。

推荐使用 ROS 进行高带宽的机载通信，以提供避障等功能，这些功能需要更高频率的消息，并可利用现有的 ROS 和计算机视觉库。
:::

该 SDK 还可用于编写 MAVLink "服务器"代码：通常由自动驾驶仪、MAVLink 摄像头或其他组件实现的 MAVLink 通信端。  
这可用于为运行在协处理器上的组件创建 MAVLink API，例如为连接到计算机的原生摄像头创建摄像头 API 接口。