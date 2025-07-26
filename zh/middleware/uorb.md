# uORB 消息传递

## 简介

uORB 是一个用于线程间/进程间通信的异步 `publish()` / `subscribe()` 消息 API。

uORB 实现于 [`uorb` 模块](../modules/modules_communication.md#uorb)。
它在 PX4 引导序列的早期会自动启动（通过 `uorb start`），因为许多应用程序依赖于此模块。
单元测试可以通过 `uorb_tests` 启动。

本文档说明了如何添加 uORB 消息定义及其对应的主题，如何在代码中引用主题，以及如何在 PX4 中观察主题的变化。
[第一个应用程序教程 (Hello Sky)](../modules/hello_sky.md) 提供了更全面的 C++ 中使用主题的指导。

## 添加新主题

新 uORB 主题可以在主 PX4/PX4-Autopilot 仓库内添加，也可以通过[外部消息定义](../advanced/out_of_tree_modules.md#out-of-tree-uorb-message-definitions)添加。

要添加新主题，需要创建一个遵循 CamelCase 命名规范的 **.msg** "消息定义文件"。文件应添加到 [msg/](https://github.com/PX4/PX4-Autopilot/tree/main/msg/) 目录（如果需要版本化则添加到 [msg/versioned](https://github.com/PX4/PX4-Autopilot/tree/main/msg/versioned)）并列在 `msg/CMakeLists.txt` 文件中。

::: tip
当消息需要暴露给 ROS 2 并需要在多个 ROS 和 PX4 版本间保持兼容时，消息需要进行版本化。更多信息请参见[消息版本化](#消息版本机制)。
:::

消息定义文件可以定义一个或多个_主题_，它们共享相同的字段和结构。默认情况下，定义会映射到单个主题，其名称为消息定义文件名的 snake_case 版本（例如，`TopicName.msg` 会定义一个名为 `topic_name` 的主题）。你也可以在消息定义中指定多个主题，这在需要多个具有相同字段和结构的主题时很有用（见下文的[多主题消息](#多话题消息)）。

下文的[消息定义](#消息定义)部分描述了消息格式。

从消息定义中，所需的 C/C++ 代码会自动生成。

在代码中使用该主题时，首先包含生成的头文件，其名称为消息定义文件名（CamelCase）的 snake_case 版本。例如，对于名为 `VelocityLimits` 的消息，应包含 `velocity_limits.h`，如以下示例所示：

```cpp
#include <uORB/topics/velocity_limits.h>
```

在代码中，通过其 id 引用主题，本例中为：`ORB_ID(velocity_limits)`。

## 消息定义

消息定义应以描述性_注释_开头，说明其用途（注释以`#`符号开头并延续至行尾）。
消息将定义一个或多个字段，通过_类型_（如`bool`、`uint8`和`float32`）和_名称_进行定义。
按照惯例，每个字段后应跟随描述性_注释_，即从`#`符号到行尾的任意文本。

::: 警告
所有消息定义**必须**包含`uint64_t timestamp`字段，并在发布相关话题时填充该字段。
此字段对于记录器记录UORB话题是必需的。
:::

::: 信息
所有_版本化_的消息定义必须包含`uint32 MESSAGE_VERSION`字段。
更多信息请参考[消息版本控制](#消息版本机制)章节。
:::

例如，下面展示的[VelocityLimits](../msg_docs/VelocityLimits.md)消息定义包含描述性注释，后接多个带有注释的字段。

```text
# 多旋翼位置慢速模式下的速度和偏航率限制

uint64 timestamp # [us] 系统启动以来的时间

# 绝对速度，NAN 表示使用默认限制
float32 horizontal_velocity # [m/s] 水平速度。
float32 vertical_velocity # [m/s] 垂直速度。
float32 yaw_rate # [rad/s] 偏航速率。

```
默认情况下，此消息定义将编译为一个主题，其 id 为 `velocity_limits`，这是从驼峰命名法直接转换为蛇形命名法的版本。

这是消息的最简单形式。
查看现有的 [`msg`](../msg_docs/index.md) 文件，了解更多关于消息定义的示例。

### 多话题消息

有时需要将相同的消息定义用于多个话题。
这可以通过在消息末尾添加一行以 `# TOPICS ` 开头的行来指定，后接空格分隔的话题 id。
例如，[ActuatorOutputs](../msg_docs/ActuatorOutputs.md) 消息定义中的话题 id 定义如下：

```text
# TOPICS actuator_outputs actuator_outputs_sim actuator_outputs_debug
```

### 嵌套消息

消息定义可以通过嵌套在其他消息中来创建复杂的数据结构。

要嵌套消息，只需在父消息定义中包含嵌套的消息类型。例如，[`PositionSetpoint.msg`](../msg_docs/PositionSetpoint.md) 被用作 [`PositionSetpointTriplet.msg`](../msg_docs/PositionSetpointTriplet.md) 主题消息定义中的嵌套消息。


```text
# Global position setpoint triplet in WGS84 coordinates.
#
# This are the three next waypoints (or just the next two or one).

uint64 timestamp # [us] Time since system start.

PositionSetpoint previous
PositionSetpoint current
PositionSetpoint next
```

### uORB 缓冲区长度 (ORB_QUEUE_LENGTH)

uORB 消息默认使用单消息缓冲区，如果消息发布率过高可能会被覆盖。  
在大多数情况下这并不重要：我们通常只关心某个主题的最新样本（如传感器值或设定点），或者丢失几个样本也不是大问题。  
对于极少数情况（如机体命令），必须确保不会丢失主题。

为了降低消息丢失的可能性，可以使用命名常量 `ORB_QUEUE_LENGTH` 创建指定长度的缓冲区。  
例如，要创建一个四消息队列，需在消息定义中添加以下行：

```sh
uint8 ORB_QUEUE_LENGTH = 4
```

只要订阅者能足够快地从缓冲区读取消息，就不会达到队列长度（由发布者触发），从而能够接收所有发送的消息。  
当队列已满时，发布的消息仍会丢失。

注意队列长度值必须是2的幂（如2、4、8等）。

### 消息/字段弃用 {#deprecation}

由于外部工具（如 [Flight Review](https://github.com/PX4/flight_review)）会使用日志文件中的 uORB 消息，因此在更新现有消息时需要考虑以下事项：

- 如果有充分理由，修改外部工具依赖的现有字段或消息通常是可接受的。  
  特别是对于 _Flight Review_ 的破坏性变更，必须在代码合并到 `master` 之前更新 _Flight Review_。
- 为使外部工具可靠区分消息版本，必须遵循以下步骤：
  - 被删除或重命名的消息需添加到 [msg/CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/c5a6a60903455c3600f47e3c45ecaa48614559c8/msg/CMakeLists.txt#L189) 中的 `deprecated_msgs` 列表，并删除对应的 **.msg** 文件。
  - 被删除或重命名的字段需注释并标记为弃用。  
    例如 `uint8 quat_reset_counter` 将变为 `# DEPRECATED: uint8 quat_reset_counter`。  
    此操作可防止弃用的字段（或消息）在未来被重新添加。
  - 如果发生语义变更（如单位从度数更改为弧度），字段也必须重命名，并将旧字段标记为弃用，如上所述。

## 消息版本机制

<Badge type="tip" text="PX4 v1.16" />

消息版本机制在 PX4 v1.16 中被引入，目的是简化 PX4 与不同消息定义编译的 ROS 2 版本之间的兼容性维护。  
版本化消息相比非版本化消息在时间上更稳定，因为它们设计用于跨多个 PX4 和外部系统的版本，确保更长时间的兼容性。

版本化消息包含一个额外字段 `uint32 MESSAGE_VERSION = x`，其中 `x` 对应消息的当前版本号。

版本化消息和非版本化消息在文件系统中分离存储：

- 非版本化主题消息文件和 [服务消息](../ros2/user_guide.md#px4-ros-2-service-servers) 文件仍分别位于 [`msg/`](https://github.com/PX4/PX4-Autopilot/tree/main/msg) 和 [`srv/`](https://github.com/PX4/PX4-Autopilot/tree/main/srv) 目录。
- 当前（最高）版本消息文件位于 `versioned` 子目录中 ([`msg/versioned`](https://github.com/PX4/PX4-Autopilot/tree/main/msg/versioned) 和 [`srv/versioned`](https://github.com/PX4/PX4-Autopilot/tree/main/srv/versioned))。
- 旧版本消息存储在嵌套的 `msg/px4_msgs_old/` 子目录中 ([`msg/px4_msgs_old/msg/`](https://github.com/PX4/PX4-Autopilot/tree/main/msg/px4_msgs_old/msg) 和 [`msg/px4_msgs_old/srv/`](https://github.com/PX4/PX4-Autopilot/tree/main/msg/px4_msgs_old/srv))，文件名通过后缀标注版本号。

::: tip
文件结构详情请参考 [文件结构（ROS 2 消息转换节点）](../ros2/px4_ros2_msg_translation_node.md#file-structure)。
:::

[ROS 2 消息转换节点](../ros2/px4_ros2_msg_translation_node.md) 使用上述消息定义，无缝转换在不同消息版本编译的 PX4 与 ROS 2 应用之间发送的消息。

更新版本化消息相比非版本化消息需要更多步骤。更多信息请参考 [更新版本化消息](../ros2/px4_ros2_msg_translation_node.md#updating-a-versioned-message)。

查看完整的版本化与非版本化消息列表，请参考：[uORB 消息参考](../msg_docs/index.md)。

关于 PX4 与 ROS 2 通信的更多信息，请参考 [PX4-ROS 2 桥接](../ros/ros2_comm.md)。

::: info
ROS 2 计划未来原生支持消息版本机制，但该功能尚未实现。  
请参阅相关 ROS 增强提案 ([REP 2011](https://github.com/ros-infrastructure/rep/pull/358))。  
另请参阅 Foxglove 的这篇 [文章](https://foxglove.dev/blog/sending-ros2-message-types-over-the-wire)，了解消息哈希和类型获取。

## 发布

系统中的任何位置（包括中断上下文（由 `hrt_call` API 调用的函数））都可以发布主题。  
但是，该主题需要先在中断上下文之外进行通告和发布（至少一次），之后才能在中断上下文中发布。

### 多实例

uORB 提供了一种机制，用于发布相同主题的多个独立实例。  
例如，当系统包含多个相同类型的传感器时，这种情况非常有用。

::: info
这与[多主题消息](#多话题消息)不同，后者创建的是结构相同但主题不同的消息。
:::

发布者可以通过调用 `orb_advertise_multi` 创建新主题实例并获取其实例索引。  
订阅者则需要使用 `orb_subscribe_multi` 选择订阅哪个实例（`orb_subscribe` 会订阅第一个实例）。

请确保不要将同一主题的 `orb_advertise_multi` 和 `orb_advertise` 混合使用！

完整的 API 文档请参见 [platforms/common/uORB/uORBManager.hpp](https://github.com/PX4/PX4-Autopilot/blob/main/platforms/common/uORB/uORBManager.hpp)。

## 列出主题和监听

::: info
大多数板载FMUv4之后可用的`listener`命令。
可通过搜索[kconfig](../hardware/porting_guide_config.md)板配置中的`CONFIG_SYSTEMCMDS_TOPIC_LISTENER`键来检查特定板（例如查看FMUv6的[default.px4board](https://github.com/PX4/PX4-Autopilot/blob/release/1.15/boards/px4/fmu-v6x/default.px4board#L100)文件）。
:::

要列出所有主题，列出文件句柄：

```sh
ls /obj
```

要监听一个主题的5条消息内容，运行监听器：

```sh
listener sensor_accel 5
```

输出是n次主题内容：

```sh
TOPIC: sensor_accel #3
timestamp: 84978861
integral_dt: 4044
error_count: 0
x: -1
y: 2
z: 100
x_integral: -0
y_integral: 0
z_integral: 0
temperature: 46
range_m_s2: 78
scaling: 0

TOPIC: sensor_accel #4
timestamp: 85010833
integral_dt: 3980
error_count: 0
x: -1
y: 2
z: 100
x_integral: -0
y_integral: 0
z_integral: 0
temperature: 46
range_m_s2: 78
scaling: 0
```

:::tip
在基于NuttX的系统（Pixhawk、Pixracer等）上，`listener`命令可通过_QGroundControl_的MAVLink控制台调用以检查传感器和其他主题的值。
这是一个强大的调试工具，即使QGC通过无线链路连接时（例如机体飞行时）也可使用。
更多信息请参见：[传感器/主题调试](../debug/sensor_uorb_topic_debugging.md)。
:::

### uorb top 命令

`uorb top`命令实时显示每个主题的发布频率：

```sh
update: 1s, num topics: 77
TOPIC NAME                        INST #SUB #MSG #LOST #QSIZE
actuator_armed                       0    6    4     0 1
actuator_controls_0                  0    7  242  1044 1
battery_status                       0    6  500  2694 1
commander_state                      0    1   98    89 1
control_state                        0    4  242   433 1
ekf2_innovations                     0    1  242   223 1
ekf2_timestamps                      0    1  242    23 1
estimator_status                     0    3  242   488 1
mc_att_ctrl_status                   0    0  242     0 1
sensor_accel                         0    1  242     0 1
sensor_accel                         1    1  249    43 1
sensor_baro                          0    1   42     0 1
sensor_combined                      0    6  242   636 1
```

各列含义：主题名称、多实例索引、订阅者数量、发布频率（Hz）、每秒丢失消息数（所有订阅者合计）、队列大小。

## 绘制主题变化

主题变化可以使用 PlotJuggler 和 PX4 ROS 2 集成实时绘制（注意：这实际上绘制的是与 uORB 主题对应的 ROS 主题，但效果相同）。

更多信息请参见：[使用 PlotJuggler 实时绘制 uORB 主题数据](../debug/plotting_realtime_uorb_data.md)

<video src="../../assets/debug/realtime_debugging/realtime_debugging.mp4" width="720" controls></video>

## 另请参见

- _PX4 uORB 解释_ 博客系列
  - [第1部分](https://px4.io/px4-uorb-explained-part-1/)
  - [第2部分](https://px4.io/px4-uorb-explained-part-2/)
  - [第3部分（深入内容）](https://px4.io/px4-uorb-explained-part-3-the-deep-stuff/)