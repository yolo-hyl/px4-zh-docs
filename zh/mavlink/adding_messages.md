# 添加标准 MAVLink 定义（消息/命令）

本主题解释如何添加 PX4 标准构建中预期包含的新 MAVLink 消息和命令。

## 标准 MAVLink 消息

PX4/PX4-Autopilot 源代码仅使用已被 MAVLink 标准化的消息。  
也就是说，在发布版本中使用 [common.xml](https://mavlink.io/en/messages/common.html) 中的定义，在开发期间使用 [development.xml](https://mavlink.io/en/messages/development.html)。  
这些消息至少存在于一个重要的飞行堆栈中，并且其他飞行堆栈成员已接受其作为合理设计，若需要相同功能很可能会采用。

::: tip
[自定义 MAVLink 消息](../mavlink/custom_messages.md) 不属于标准定义。  
这些消息需在您 PX4 的分叉中自行定义 XML 文件。  
若您使用 [自定义 MAVLink 消息](../mavlink/custom_messages.md)，需要在 PX4、地面站及任何与其通信的 SDK 中维护定义。  
通常建议尽可能使用（或添加）标准定义以减少维护负担。
:::

新标准定义首先添加到 `development.xml`，经审查、原型开发和 MAVLink 团队接受后，再移入 `common.xml`。

若希望您的消息成为 PX4 默认构建的一部分，需向 MAVLink 社区提交对 [development.xml](https://github.com/mavlink/mavlink/blob/master/message_definitions/v1.0/development.xml) 的拉取请求（PR）。  
[MAVLink 开发者指南](https://mavlink.io/en/getting_started/) 解释了如何在 [如何定义 MAVLink 消息和枚举](https://mavlink.io/en/guide/define_xml_element.html) 中定义新消息。

## 生成消息头文件

开发期间可将定义添加到 `PX4-Autopilot/src/modules/mavlink/mavlink/message_definitions/v1.0/development.xml`（或从 MAVLink 拉取）。

构建 PX4 时，这些消息定义的头文件会生成在构建目录 (`/build/<build target>/mavlink/`)。  
若消息未生成头文件，可能是格式错误或 ID 冲突。请检查构建日志获取信息。

## 实现消息发送器/接收器

当 PX4 构建中生成了您的消息定义头文件后，可在代码中使用它们发送和接收消息：

- [流式传输 MAVLink 消息](../mavlink/streaming_messages.md)
- [接收 MAVLink 消息](../mavlink/receiving_messages.md)

## 测试

调试的第一步是确认您创建的消息是否按预期发送/接收。

首先使用 `uorb top [<message_name>]` 命令实时验证消息是否发布及速率（参见 [uORB 消息](../middleware/uorb.md#uorb-top-command)）。  
此方法也可用于测试发布 uORB 主题的入站消息（其他消息可能需要代码中使用 `printf` 并在 SITL 中测试）。

有几种方法可用于查看 MAVLink 流量：

- 为您的方言创建 [Wireshark MAVLink 插件](https://mavlink.io/en/guide/wireshark.html)。  
  这允许您检查 IP 接口上的 MAVLink 流量，例如在 _QGroundControl_ 或 MAVSDK 与您的 PX4 实体（真实或模拟）之间。

  :::tip
  生成 Wireshark 插件并在此工具中检查流量，比分发 QGroundControl 并使用 MAVLink Inspector 更简单。
  :::

- [记录 uORB 主题](../dev_log/logging.md) 关联您的 MAVLink 消息。
- 在 QGroundControl [MAVLink Inspector](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html) 中查看接收消息。  
  您需要 [重新构建 QGroundControl 以包含新消息定义](#更新地面站)。

### 通过 Shell 设置流式传输速率

测试时，有时需要增加单个主题在运行时的流式传输速率（例如用于 QGC 中的检查）。  
可通过 [QGC MAVLink 控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)（或其它 Shell）调用 [mavlink](../modules/modules_communication.md#mavlink) 模块实现：

```sh
mavlink stream -u <port number> -s <mavlink topic name> -r <rate>
```

可通过 `mavlink status` 获取端口号，输出中会显示 `transport protocol: UDP (<port number>)`。  
示例：

```sh
mavlink stream -u 14556 -s CA_TRAJECTORY -r 300
```

## 更新地面站

最终您希望通过对应的地面站或 MAVSDK 实现使用新 MAVLink 接口。

需要记住的是，MAVLink 要求您使用与相同定义（XML 文件）构建的库版本。  
因此，若您创建了 PX4 中的自定义消息，除非使用相同定义构建 QGC 或 MAVSDK，否则无法使用。

### 更新 QGroundControl

您需要 [构建 QGroundControl](https://docs.qgroundcontrol.com/master/en/qgc-dev-guide/getting_started/index.html) 包含预构建的 C 库，其中包含您的自定义消息。

QGC 使用必须位于源码中 [/qgroundcontrol/libs/mavlink/include/mavlink](https://github.com/mavlink/qgroundcontrol/tree/master/libs/mavlink/include/mavlink) 的预构建 C 库。

默认情况下，该库作为子模块从 <https://github.com/mavlink/c_library_v2> 预包含，但您可 [生成自己的 MAVLink 库](https://mavlink.io/en/getting_started/generate_libraries.html)。

QGC 默认使用 **all.xml** 方言，其中包含 **common.xml**。  
您可以在任一文件中包含消息。

请注意，若您使用自定义方言，则需包含 **ArduPilotMega.xml**（否则会遗漏所有现有消息），并通过设置 [`MAVLINK_CONF`](https://github.com/mavlink/qgroundcontrol/blob/master/QGCExternalLibs.pri#L52) 修改运行 _qmake_ 时的方言。

### 更新 MAVSDK

有关如何处理 [MAVLink 头文件和方言](https://mavsdk.mavlink.io/main/en/cpp/guide/build.html) 的信息，请参阅 MAVSDK 文档。