# MAVLink 消息

[MAVLink](https://mavlink.io/en/) 是一种为无人机生态系统设计的非常轻量级消息协议。

PX4 使用 _MAVLink_ 与地面站和 MAVLink SDK（如 _QGroundControl_ 和 [MAVSDK](https://mavsdk.mavlink.io/)）进行通信，并作为集成机制连接飞行控制器以外的无人机组件：伴飞计算机、支持 MAVLink 的相机等。

本主题简要概述了基础的 MAVLink 概念，如消息、命令和微服务。
它还提供了以下 PX4 支持的添加说明链接：

- [添加标准消息](../mavlink/adding_messages.md)
- [流式传输 MAVLink 消息](../mavlink/streaming_messages.md)
- [处理传入的 MAVLink 消息（并写入 uORB 话题）](../mavlink/receiving_messages.md)
- [自定义 MAVLink 消息](../mavlink/custom_messages.md)
- [协议/微服务](../mavlink/protocols.md)

::: info
我们尚未涵盖 _command_ 的处理和发送，或如何实现自己的微服务。
:::

## MAVLink 概述

MAVLink 是一种轻量级协议，设计用于通过不可靠的低带宽无线电链路高效发送消息。

**消息**是 MAVLink 中最简单且最“基础”的定义，由名称（例如 [ATTITUDE](https://mavlink.io/en/messages/common.html#ATTITUDE)）、ID 和包含相关数据的字段组成。它们刻意保持轻量特性，具有受限的大小，并且不包含重传和确认的语义。独立消息通常用于流式遥测或状态信息，以及发送无需确认的命令（如以高频率发送的设定点命令）。

[Microservices](../mavlink/protocols.md) 是基于 MAVLink 消息构建的“元协议”。它们用于通信无法通过单个消息发送的信息。

例如，[Command Protocol](https://mavlink.io/en/services/command.html) 是一种服务质量协议，用于发送可能需要确认和重传的命令。具体命令被定义为 [MAV_CMD](https://mavlink.io/en/messages/common.html#mav_commands) 枚举值，如起飞命令 [MAV_CMD_NAV_TAKEOFF](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_TAKEOFF)，并包含最多 7 个数字“参数”值。该协议通过将参数值打包到 `COMMAND_INT` 或 `COMMAND_LONG` 消息中发送命令，并等待带有结果的 `COMMAND_ACK` 确认响应。若未收到确认响应，命令会自动重传若干次。注意 [MAV_CMD](https://mavlink.io/en/messages/common.html#mav_commands) 定义也用于定义任务动作，但并非所有定义都支持在 PX4 命令/任务中使用。

其他服务包括 [File Transfer Protocol](https://mavlink.io/en/services/ftp.html)、[Camera Protocol](https://mavlink.io/en/services/camera.html)、[Parameter Protocol](https://mavlink.io/en/services/parameter.html) 和 [Mission Protocol](https://mavlink.io/en/services/mission.html)。关于 PX4 支持的更多细节请参见 [Microservices](../mavlink/protocols.md)。

MAVLink 消息、命令和枚举在 [XML 定义文件](https://mavlink.io/en/guide/define_xml_element.html) 中定义。MAVLink 工具链包含代码生成器，这些生成器根据定义创建特定编程语言的库以发送和接收消息。注意大多数生成的库不会创建实现微服务的代码。

MAVLink 项目通过以下定义文件标准化消息、命令、枚举和微服务（注意上层文件会包含下层文件的定义）：

- [development.xml](https://mavlink.io/en/messages/development.html) — 提议成为标准一部分的定义。通过测试后定义会移至 `common.xml`。
- [common.xml](https://mavlink.io/en/messages/common.html) — 满足多种常见 UAV 应用场景的“定义库”。这些定义被多数飞控栈、地面站和 MAVLink 外设支持，使用这些定义的飞控栈更可能实现互操作。
- [standard.xml](https://mavlink.io/en/messages/standard.html) — 实际已成为标准的定义。它们几乎全部飞行栈均以相同方式实现。
- [minimal.xml](https://mavlink.io/en/messages/minimal.html) — 最小 MAVLink 实现所需的基本定义。

该项目还托管 [方言 XML 定义](https://mavlink.io/en/messages/#dialects)，其中包含特定于某个飞控栈或其他利益相关方的 MAVLink 定义。

协议要求通信双方对发送的消息具有共享的定义。这意味着通信双方必须使用基于相同 XML 定义生成的库才能实现通信。

<!--
消息通过 [MAVLink 数据包](https://mavlink.io/en/guide/serialization.html#mavlink2_packet_format) 的“载荷”传输。为减少传输数据量，数据包不包含消息元数据（如字段构成等）。字段按预定义顺序（基于数据大小和 XML 定义顺序）序列化，MAVLink 依赖通信双方共享消息定义。消息身份通过消息 ID 和基于名称、ID、字段名及类型的 CRC（"CRC_EXTRA"）唯一标识。接收端会丢弃消息 ID 与 CRC_EXTRA 不匹配的数据包。
-->

## PX4 和 MAVLink

PX4 发行版默认构建 `common.xml` MAVLink 定义，以确保与 MAVLink 地面站、库和外部组件（如 MAVLink 摄像机）的最大兼容性。  
在 `main` 分支中，这些定义在 SITL 上从 `development.xml` 引入，其他板子则使用 `common.xml`。

::: info
任何要包含在 PX4 发行版中的 MAVLink 定义，必须位于 `common.xml` 中（或包含的文件如 `standard.xml` 和 `minimal.xml`）。  
在开发期间，可以使用 `development.xml` 中的定义。  
你需要与 [MAVLink 团队](https://mavlink.io/en/contributing/contributing.html) 合作来定义和贡献这些定义。
:::

PX4 在 [/src/modules/mavlink](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mavlink) 下将 [mavlink/mavlink](https://github.com/mavlink/mavlink) 仓库作为子模块引入。  
该仓库包含 XML 定义文件在 [/mavlink/messages/1.0/](https://github.com/mavlink/mavlink/blob/master/message_definitions/v1.0/)。

构建工具链会在构建时生成 MAVLink 2 C 头文件。  
XML 文件的定义在 [PX4 kconfig 板级配置](../hardware/porting_guide_config.md#px4-board-configuration-kconfig) 中按板子基础进行定义，使用变量 `CONFIG_MAVLINK_DIALECT`：

- 对于 SITL，`CONFIG_MAVLINK_DIALECT` 在 [boards/px4/sitl/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/sitl/default.px4board#L36) 中设置为 `development`。  
  你可以将其更改为其他定义文件，但该文件必须包含 `common.xml`。
- 对于其他板子，`CONFIG_MAVLINK_DIALECT` 默认未设置，PX4 默认构建 `common.xml` 中的定义（这些定义默认构建到 [mavlink 模块](../modules/modules_communication.md#mavlink) —— 在 [src/modules/mavlink/Kconfig](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/Kconfig#L10) 中搜索 `menuconfig MAVLINK_DIALECT`）。

生成的文件位于构建目录中：`/build/<build target>/mavlink/`。