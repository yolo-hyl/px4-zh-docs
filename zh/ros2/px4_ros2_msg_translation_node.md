

# PX4 ROS 2消息转换节点

<Badge type="tip" text="PX4 v1.16" /> <Badge type="warning" text="Experimental" />

该消息转换节点允许针对不同版本的PX4消息编译的ROS 2应用程序与新版本的PX4互操作，反之亦然，而无需修改应用程序或PX4端代码。

## 概述

通过引入[消息版本控制](../middleware/uorb.md#message-versioning)，可以实现消息从一个定义版本到另一个版本的转换。  
转换节点可以访问PX4先前定义的所有消息版本。它动态监控DDS数据空间，跟踪来自PX4通过[uXRCE-DDS Bridge](../middleware/uxrce_dds.md)或ROS 2应用程序发布的消息、订阅和服务。在必要时，它会将消息转换为应用程序和PX4预期的当前版本，从而确保兼容性。  

![ROS 2 消息转换节点概述](../../assets/middleware/ros2/px4_ros2_interface_lib/translation_node.svg)  

<!-- doc source: ../../assets/middleware/ros2/px4_ros2_interface_lib/translation_node.drawio -->  

为了支持在ROS 2域内相同消息的不同版本共存，ROS 2中用于消息发布、订阅和服务的主题名称包含了各自消息版本作为后缀。该命名规则采用`<topic_name>_v<version>`的形式，如上图所示。

## 使用

### 安装

以下步骤描述了如何在您的设备上安装并运行翻译节点。

1. (可选) 创建一个新的ROS 2工作空间以构建消息翻译节点及其依赖项：

   ```sh
   mkdir -p /path/to/ros_ws/src
   ```

1. 运行以下辅助脚本将消息定义和翻译节点复制到您的ROS工作空间目录中。

   ```sh
   cd /path/to/ros_ws
   /path/to/PX4-Autopilot/Tools/copy_to_ros_ws.sh .
   ```

1. 构建并加载工作空间。

   ```sh
   colcon build
   source /path/to/ros_ws/install/setup.bash
   ```

1. 最后，运行翻译节点。

   ```sh
   ros2 run translation_node translation_node_bin
   ```

   您应该会看到类似以下的输出：

   ```sh
   [INFO] [1734525720.729530513] [translation_node]: Registered pub/sub topics and versions:
   [INFO] [1734525720.729594413] [translation_node]: Registered services and versions:
   ```

在翻译节点运行时，任何同时运行的ROS 2应用程序只要使用该节点识别的消息版本，都可以与PX4通信。如果遇到未知主题版本，翻译节点将打印警告信息。

::: info
在对PX4的消息定义和/或翻译节点代码进行修改后，您需要从第2步重新运行上述步骤以更新ROS工作空间。
:::

### 在 ROS 应用中

在开发与 PX4 通信的 ROS 2 应用时，无需了解所使用消息的具体版本。  
消息版本可以按如下方式添加到主题名称中：

:::: tabs

::: tab C++

```c++
topic_name + "_v" + std::to_string(T::MESSAGE_VERSION)
```

:::

::: tab Python

```python
topic_name + "_v" + VehicleAttitude.MESSAGE_VERSION
```

:::

::::

其中 `T` 是消息类型，例如 `px4_msgs::msg::VehicleAttitude`。

例如，以下实现了一个使用两个带版本号的 PX4 消息和主题的最小订阅者和发布者节点：

:::: tabs

::: tab C++

```c++
#include <string>
#include <rclcpp/rclcpp.hpp>
#include <px4_msgs/msg/vehicle_command.hpp>
#include <px4_msgs/msg/vehicle_attitude.hpp>

// 模板函数用于获取消息版本后缀
// 正确的消息版本直接从消息定义中推断
template <typename T>
std::string getMessageNameVersion() {
    if (T::MESSAGE_VERSION == 0) return "";
    return "_v" + std::to_string(T::MESSAGE_VERSION);
}

class MinimalPubSub : public rclcpp::Node {
  public:
    MinimalPubSub() : Node("minimal_pub_sub") {
      // 使用模板函数自动定义正确的主题
      const std::string sub_topic = "/fmu/out/vehicle_attitude" + getMessageNameVersion<px4_msgs::msg::VehicleAttitude>();
      const std::string pub_topic = "/fmu/in/vehicle_command" + getMessageNameVersion<px4_msgs::msg::VehicleCommand>();

      _subscription = this->create_subscription<px4_msgs::msg::VehicleAttitude>(
          sub_topic, 10,
          std::bind(&MinimalPubSub::attitude_callback, this, std::placeholders::_1));
      _publisher = this->create_publisher<px4_msgs::msg::VehicleCommand>(pub_topic, 10);
    }

  private:
    void attitude_callback(const px4_msgs::msg::VehicleAttitude::SharedPtr msg) {
      RCLCPP_INFO(this->get_logger(), "Received attitude message.");
    }

    rclcpp::Publisher<px4_msgs::msg::VehicleCommand>::SharedPtr _publisher;
    rclcpp::Subscription<px4_msgs::msg::VehicleAttitude>::SharedPtr _subscription;
};
```

:::

::: tab Python

```python
import rclpy
from rclpy.node import Node
from px4_msgs.msg import VehicleCommand, VehicleAttitude

# 辅助函数：获取消息版本后缀# 正确的消息版本直接从消息定义中推断

def get_message_name_version(msg_class):
    if msg_class.MESSAGE_VERSION == 0:
        return ""
    return f"_v{msg_class.MESSAGE_VERSION}"

class MinimalPubSub(Node):
    def __init__(self):
        super().__init__('minimal_pub_sub')

        # 使用辅助函数自动定义正确的主题
        sub_topic = f"/fmu/out/vehicle_attitude{get_message_name_version(VehicleAttitude)}"
        pub_topic = f"/fmu/in/vehicle_command{get_message_name_version(VehicleCommand)}"

        self._subscription = self.create_subscription(
            VehicleAttitude,
            sub_topic,
            self.attitude_callback,
            10
        )

        self._publisher = self.create_publisher(
            VehicleCommand,
            pub_topic,
            10
        )

    def attitude_callback(self, msg):
        self.get_logger().info("接收到姿态消息。")
```

:::

::::

在PX4端，如果消息定义中包含字段`uint32 MESSAGE_VERSION = x`，DDS客户端会自动添加版本后缀。

::: info
主题的0版本表示不应添加`_v<版本>`后缀。
:::

## 开发

### 定义

**消息**定义了通过主题或服务进行通信时所使用的数据格式。因此，消息可以是通过`.msg`文件定义的_主题_消息，也可以是通过`.srv`文件定义的_服务_消息。

**版本化消息**是指对其变更进行追踪的消息，每次变更都会导致版本号递增，且定义的先前状态会被存储在历史记录中。每条消息的最新版本存储在`msg/versioned/`中（服务消息则存储在`srv/versioned`中），所有旧版本则存储在`msg/px4_msgs_old/msg/`（服务消息存储在`msg/px4_msgs_old/srv/`）。

**版本翻译**定义了在不同版本之间一个或多个消息定义内容的双向映射。每个翻译以单独的`.h`头文件形式存储在`msg/translation_node/translations/`目录下。消息翻译可以是_直接_的或_通用_的。

- **直接翻译**定义了同一消息的两个版本之间内容的双向映射。这是更简单的情况，如果可能应优先采用。
- **通用翻译**定义了在不同版本之间`n`个输入消息到`m`个输出消息内容的双向映射。可用于消息的合并/拆分，或当需要将某个字段从一个消息移动到另一个消息时。

### 文件结构

从 PX4 v1.16 开始，PX4-Autopilot 的 `msg/` 和 `srv/` 目录结构如下：

```
PX4-Autopilot
├── ...
├── msg/
  ├── *.msg              # 非版本化主题消息文件
  ├── versioned/         # 最新版本化主题消息文件
  ├── px4_msgs_old/      # 版本化消息历史记录 (.msg + .srv) [ROS 2 package]
  └── translation_node/  # 转换节点和转换头文件 [ROS 2 package]
└── srv/
  ├── *.srv              # 非版本化服务消息文件
  └── versioned/         # 最新版本化服务消息文件
```

此结构引入了新的目录：`versioned/`、`px4_msgs_old/` 和 `translation_node/`。

#### 目录 `msg/versioned/` 和 `srv/versioned/`

- 包含每条消息的当前最新版本。
- 这些目录中的文件必须包含 `MESSAGE_VERSION` 字段以表明其版本化状态。
- 文件名遵循常规命名方案（无版本后缀）。

示例目录结构：

```
PX4-Autopilot
├── ...
├── msg/
  └── versioned/
    ├── VehicleAttitude.msg        # 例如 MESSAGE_VERSION = 3
    └── VehicleGlobalPosition.msg  # 例如 MESSAGE_VERSION = 2
└── srv/
  └── versioned/
    └── VehicleCommand.srv         # 例如 MESSAGE_VERSION = 2
```

#### 目录 `px4_msgs_old/`

- 存档所有版本化消息的历史记录，包括主题消息和服务消息（分别位于 `msg/` 和 `srv/` 子目录下）。
- 每个文件包含 `MESSAGE_VERSION` 字段。
- 文件名通过后缀反映消息版本（例如 `V1`、`V2`）。

示例目录结构（与上述示例对应）：

```
  ...
  msg/
  └── px4_msgs_old/
    ├── msg/
      ├── VehicleAttitudeV1.msg
      ├── VehicleAttitudeV2.msg
      └── VehicleGlobalPositionV1.msg
    └── srv/
      └── VehicleCommandV1.srv
```

#### 目录 `translation_node/`

- 包含用于在所有不同版本消息之间进行转换的头文件。
- 每个转换（直接或通用）为一个单独的 `.h` 头文件。
- 头文件 `all_translation.h` 作为主头文件，包含所有后续转换头文件。

示例目录结构（与上述示例对应）：

```
  ...
  msg/
  └── translation_node/
    └── translations/
      ├── all_translations.h                        # 主头文件
      ├── translation_vehicle_attitude_v1.h         # 直接转换 v0 <-> v1
      ├── translation_vehicle_attitude_v2.h         # 直接转换 v1 <-> v2
      ├── translation_vehicle_attitude_v3.h         # 直接转换 v2 <-> 最新版本(v3)
      ├── translation_vehicle_global_position_v1.h  # 直接转换 v0 <-> v1
      ├── translation_vehicle_global_position_v2.h  # 直接转换 v1 <-> 最新版本(v2)
      ├── translation_vehicle_command_v1.h          # 直接转换 v0 <-> v1
      └── translation_vehicle_command_v2.h          # 直接转换 v1 <-> 最新版本(v2)
```

### 更新版本化消息

本节提供一个逐步操作指南及基础示例，说明修改版本化消息的完整流程。

示例描述了将 `VehicleAttitude` 消息定义更新为包含新增的 `new_field` 字段，将消息版本从 `3` 升级为 `4`，并在此过程中创建新的直接翻译。

1. **创建当前版本化消息的归档定义**

   将版本化的 `.msg` 主题消息文件（或 `.srv` 服务消息文件）复制到 `px4_msgs_old/msg/`（或 `px4_msgs_old/srv/`），并在文件名后追加消息版本号。

   例如：<br>
   复制 `msg/versioned/VehicleAttitude.msg` → `msg/versioned/px4_msgs_old/msg/VehicleAttitudeV3.msg`

1. **更新归档定义的翻译引用**

   更新现有的翻译头文件 `msg/translation_node/translations/*.h`，使其引用新归档的消息定义。

   例如，更新这些文件中的引用：<br>

   - 将 `px4_msgs::msg::VehicleAttitude` 替换为 `px4_msgs_old::msg::VehicleAttitudeV3`
   - 将 `#include <px4_msgs/msg/vehicle_attitude.hpp>` 替换为 `#include <px4_msgs_old/msg/vehicle_attitude_v3.hpp>`

1. **更新版本化定义**

   用所需修改更新版本化的 `.msg` 主题消息文件（或 `.srv` 服务消息文件）。

   首先递增 `MESSAGE_VERSION` 字段。
   然后更新导致版本变更的消息字段。

   例如，将 `msg/versioned/VehicleAttitude.msg` 从：

   ```txt
   uint32 MESSAGE_VERSION = 3
   uint64 timestamp
   ...
   ```

   更新为：

   ```txt
   uint32 MESSAGE_VERSION = 4  # 递增
   uint64 timestamp
   float32 new_field           # 修改定义
   ...
   ```

1. **添加新的翻译头文件**

   通过创建新的翻译头文件，添加新的版本翻译以桥接归档版本和更新后的当前版本。

   例如，创建直接翻译头文件 `translation_node/translations/translation_vehicle_attitude_v4.h`：

   ```c++
   // 翻译 VehicleAttitude v3 <--> v4
   #include <px4_msgs_old/msg/vehicle_attitude_v3.hpp>
   #include <px4_msgs/msg/vehicle_attitude.hpp>

   class VehicleAttitudeV4Translation {
   public:
     using MessageOlder = px4_msgs_old::msg::VehicleAttitudeV3;
     static_assert(MessageOlder::MESSAGE_VERSION == 3);

     using MessageNewer = px4_msgs::msg::VehicleAttitude;
     static_assert(MessageNewer::MESSAGE_VERSION == 4);

     static constexpr const char* kTopic = "fmu/out/vehicle_attitude";

     static void fromOlder(const MessageOlder &msg_older, MessageNewer &msg_newer) {
       msg_newer.timestamp = msg_older.timestamp;
       msg_newer.timestamp_sample = msg_older.timestamp_sample;
       msg_newer.q[0] = msg_older.q[0];
       msg_newer.q[1] = msg_older.q[1];
       msg_newer.q[2] = msg_older.q[2];
       msg_newer.q[3] = msg_older.q[3];
       msg_newer.delta_q_reset = msg_older.delta_q_reset;
       msg_newer.quat_reset_counter = msg_older.quat_reset_counter;

       // 用某个值填充 `new_field`
       msg_newer.new_field = -1;
     }

     static void toOlder(const MessageNewer &msg_newer, MessageOlder &msg_older) {
       msg_older.timestamp = msg_newer.timestamp;
       msg_older.timestamp_sample = msg_newer.timestamp_sample;
       msg_older.q[0] = msg_newer.q[0];
       msg_older.q[1] = msg_newer.q[1];
       msg_older.q[2] = msg_newer.q[2];
       msg_older.q[3] = msg_newer.q[3];
       msg_older.delta_q_reset = msg_newer.delta_q_reset;
       msg_older.quat_reset_counter = msg_newer.quat_reset_counter;

       // 丢弃 MessageNewer 中的 `new_field`
     }
   };

   REGISTER_TOPIC_TRANSLATION_DIRECT(VehicleAttitudeV4Translation);
   ```

   版本翻译模板可参考以下链接：

   - [直接主题消息翻译模板](https://github.com/PX4/PX4-Autopilot/blob/main/msg/translation_node/translations/example_translation_direct_v1.h)
   - [通用主题消息翻译模板](https://github.com/PX4/PX4-Autopilot/blob/main/msg/translation_node/translations/example_translation_multi_v2.h)
   - [直接服务消息翻译模板](https://github.com/PX4/PX4-Autopilot/blob/main/msg/translation_node/translations/example_translation_service_v1.h)

1. **在 `all_translations.h` 中包含新头文件**

   将所有新创建的头文件添加到 [`translations/all_translations.h`](https://github.com/PX4/PX4-Autopilot/blob/main/msg/translation_node/translations/all_translations.h)，以便翻译节点可以找到它们。

   例如，向 `all_translation.h` 追加以下行：

   ```c++
   #include "translation_vehicle_attitude_v4.h"
   ```

注意，在上述示例及大多数情况下，第4步仅需开发者为定义变更创建直接翻译。
这是因为变更仅涉及单个消息。
在更复杂的拆分、合并和/或移动定义的情况下，必须创建通用翻译。

例如当将一个字段从一个消息移动到另一个消息时，应添加一个包含两个旧消息版本作为输入、两个新版本作为输出的通用翻译。
这确保了在正向或反向翻译时不会丢失任何信息。

这正是[通用主题消息翻译模板](https://github.com/PX4/PX4-Autopilot/blob/main/msg/translation_node/translations/example_translation_multi_v2.h)展示的方法，仅省略了在 `fromOlder()` 和 `toOlder()` 方法中实际修改字段的代码。

::: warning
如果嵌套消息定义发生变更，则所有包含该消息的消息也必须更新版本。
例如，如果 [PositionSetpointTriplet](../msg_docs/PositionSetpointTriplet.md) 消息已版本化，则会如此。
这对于更可能引用其他消息定义的服务尤为重要。
:::

## 实现细节

翻译节点会动态监控主题和服务。  
它会根据需要实例化对应的发布者和订阅者。  
例如，当存在外部发布者对应某个主题的版本1和订阅者对应版本2时。

在内部，它维护所有已知主题和版本元组的图（即图中的节点）。  
该图通过消息翻译进行连接。  
由于可以注册任意的消息翻译，图中可能存在循环和从一个节点到另一个节点的多条路径。  
因此在主题更新时，会使用最短路径算法遍历图。  
当从一个节点移动到下一个节点时，会调用消息翻译方法并传入当前主题数据。  
如果一个节点包含已实例化的发布者（因为之前检测到外部订阅者），则会发布数据。  
因此，可以为该主题的任意版本的多个订阅者更新数据的正确版本。

对于包含多个输入主题的翻译，会在所有输入消息可用后继续执行翻译。

## 限制

- 服务消息的翻译在ROS Humble上无法工作，但在ROS Jazzy上可以正常工作。  
  这是因为当前实现依赖于一个在ROS Humble中尚未可用的服务API。  
  主题消息的翻译完全受支持。  
- 服务消息仅支持线性历史（即不支持消息拆分或合并）。  
- 当前翻译节点无法处理同一主题的两个不同版本同时存在发布者和订阅者的情况，这将触发无限循环发布。  
  此配置的典型问题如下：  
  
  ```
  应用1: 发布topic_v1，订阅topic_v1  
  应用2: 发布topic_v2，订阅topic_v2  
  ```  
  
  实际上这种配置很少见，因为与FMU共享的ROS主题通常是单向的（例如`/fmu/out/vehicle_status`或`/fmu/in/trajectory_setpoint`），因此应用程序通常不会同时向同一主题发布和订阅。  
  如果需要，翻译节点可以扩展以处理这个边界情况。  
  
原始文档要求：https://docs.google.com/document/d/18_RxV1eEjt4haaa5QkFZAlIAJNv9w5HED2aUEiG7PVQ/edit?usp=sharing