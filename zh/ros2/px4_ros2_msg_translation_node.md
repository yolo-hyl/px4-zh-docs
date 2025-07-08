# PX4 ROS 2 消息转换节点

<Badge type="tip" text="PX4 v1.16" /> <Badge type="warning" text="Experimental" />

消息转换节点允许针对不同版本PX4消息编译的ROS 2应用与新版本PX4协同工作，反之亦然，而无需修改应用或PX4端代码。

## 概述

通过引入[消息版本管理](../middleware/uorb.md#message-versioning)，可以实现不同定义版本之间消息的转换。

转换节点可以访问PX4先前定义的所有消息版本。  
它动态监控DDS数据空间，跟踪来自PX4通过[uXRCE-DDS Bridge](../middleware/uxrce_dds.md)或ROS 2应用的发布、订阅和服务。  
在必要时，它会将消息转换为应用程序和PX4预期的当前版本，以确保兼容性。

![ROS 2 消息翻译节点概述](../../assets/middleware/ros2/px4_ros2_interface_lib/translation_node.svg)

<!-- 文档源: ../../assets/middleware/ros2/px4_ros2_interface_lib/translation_node.drawio -->

为支持ROS 2域内相同消息的不同版本共存，ROS 2的发布、订阅和服务的主题名称会包含对应的版本号作为后缀。  
该命名规则采用`<topic_name>_v<version>`的形式，如上图所示。

## 用法

### 安装

以下步骤描述了如何在你的设备上安装并运行翻译节点。

1. (可选) 创建一个新的ROS 2工作区，用于构建消息翻译节点及其依赖项：

   ```sh
   mkdir -p /path/to/ros_ws/src
   ```

1. 运行以下辅助脚本，将消息定义和翻译节点复制到你的ROS工作区目录中。

   ```sh
   cd /path/to/ros_ws
   /path/to/PX4-Autopilot/Tools/copy_to_ros_ws.sh .
   ```

1. 构建并加载工作区。

   ```sh
   colcon build
   source /path/to/ros_ws/install/setup.bash
   ```

1. 最后，运行翻译节点。

   ```sh
   ros2 run translation_node translation_node_bin
   ```

   你应该会看到类似以下的输出：

   ```sh
   [INFO] [1734525720.729530513] [translation_node]: 注册的发布/订阅主题及版本：
   [INFO] [1734525720.729594413] [translation_node]: 注册的服务及版本：
   ```

在翻译节点运行时，任何同时运行的ROS 2应用程序只要使用该节点支持的消息版本，都可以与PX4通信。如果节点遇到未知的主题版本，将会打印警告。

::: info
在PX4中修改了消息定义和/或翻译节点代码后，你需要重新运行上述第2步及之后的步骤以更新ROS工作区。
:::

### 在ROS应用程序中

在开发与PX4通信的ROS 2应用程序时，无需知道消息的具体版本。消息版本可以以通用方式添加到主题名称中，格式如下：

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

其中`T`是消息类型，例如`px4_msgs::msg::VehicleAttitude`。

例如，以下代码实现了一个使用两个带版本的PX4消息和主题的最小化订阅者和发布者节点：

:::: tabs

::: tab C++

```c++
#include <string>
#include <rclcpp/rclcpp.hpp>
#include <px4_msgs/msg/vehicle_command.hpp>
#include <px4_msgs/msg/vehicle_attitude.hpp>

// 模板函数获取消息版本后缀
// 正确的消息版本直接从消息定义中推导
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


```# 获取消息版本后缀的辅助函数# 消息的正确版本号可直接从消息定义中推断出
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


:::

::::

在 PX4 一侧，如果消息定义中包含字段 `uint32 MESSAGE_VERSION = x`，DDS 客户端会自动添加版本后缀。

::: info
主题的第 0 版表示不应添加 `_v<version>` 后缀。
:::

## 开发

### 定义

**消息**定义了通过主题或服务进行通信时使用的数据格式。因此，消息可以是通过`.msg`文件定义的**主题消息**，也可以是通过`.srv`文件定义的**服务消息**。

**版本化消息**是指其变更被跟踪且每次变更都会导致版本号递增的消息，其定义的先前状态会存储在历史记录中。每个消息的最新版本存储在`msg/versioned/`（主题）或`srv/versioned`（服务）目录中，所有旧版本存储在`msg/px4_msgs_old/msg/`（或`msg/px4_msgs_old/srv/`）。

**版本转换**定义了不同版本间一条或多条消息定义内容的双向映射。每个转换以`msg/translation_node/translations/`目录下的独立`.h`头文件形式存储。消息转换可以是**直接转换**或**通用转换**。

- **直接转换**定义了单条消息在两个版本间的双向映射。这是更简单的场景，应优先使用。
- **通用转换**定义了`n`条输入消息与`m`条输出消息在不同版本间的双向映射。可用于合并/拆分消息，或当字段从一条消息移动到另一条消息时使用。

### 文件结构

从PX4 v1.16开始，PX4-Autopilot的`msg/`和`srv/`目录结构如下：

```
PX4-Autopilot
├── ...
├── msg/
  ├── *.msg              # 非版本化主题消息文件
  ├── versioned/         # 最新版本化主题消息文件
  ├── px4_msgs_old/      # 版本化消息历史(.msg + .srv) [ROS 2包]
  └── translation_node/  # 转换节点和转换头文件 [ROS 2包]
└── srv/
  ├── *.srv              # 非版本化服务消息文件
  └── versioned/         # 最新版本化服务消息文件
```

此结构引入了新目录：`versioned/`、`px4_msgs_old/`和`translation_node/`。

#### 目录 `msg/versioned/` 和 `srv/versioned/`

- 包含每条消息的最新版本
- 这些目录中的文件必须包含`MESSAGE_VERSION`字段以标识其版本化状态
- 文件名遵循常规命名方案（无版本后缀）

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

- 归档所有版本化消息的历史记录，包括主题和消息（分别存储在`msg/`和`srv/`子目录中）
- 每个文件包含`MESSAGE_VERSION`字段
- 文件名通过后缀（如`V1`、`V2`）反映消息版本

示例目录结构（匹配上述示例）：

```
  ...
  msg/
  └── px4_msgs_old/
    ├── msg/
    │   ├── VehicleAttitudeV2.msg
    │   └── VehicleGlobalPositionV3.msg
    └── srv/
        └── VehicleCommandV2.srv
```

#### 目录 `translation_node/`

- 包含消息转换相关代码
- 示例结构：`translations/`目录存放具体转换实现

### 更新版本化消息

1. **归档旧版本**  
   将旧版本消息文件复制到`px4_msgs_old/`目录，并更新版本号。例如：

   ```bash
   cp msg/VehicleAttitude.msg msg/px4_msgs_old/VehicleAttitudeV2.msg
   ```

2. **实现版本转换**  
   创建转换头文件（如`translation_vehicle_attitude_v4.h`），实现`fromOlder()`和`toOlder()`方法。例如：

   ```c++
   // 示例代码（保留原文格式）
   ```

3. **更新转换注册**  
   在`all_translations.h`中包含新创建的转换头文件：

   ```c++
   #include "translation_vehicle_attitude_v4.h"
   ```

4. **构建验证**  
   执行构建并运行测试确保转换逻辑正确：

   ```bash
   make px4_sitl_default
   make tests
   ```

::: warning
若嵌套消息定义变更，所有包含该消息的外层消息也需更新版本。例如，若`PositionSetpointTriplet`消息版本化，其结构变更将影响所有引用该消息的模块。此规则对服务尤为重要，因其更常引用其他消息定义。
:::

> 注意事项：
> - 复杂转换（如字段迁移）需使用通用转换模板
> - 转换实现需确保正向/反向转换无信息丢失
> - 服务消息转换需特别处理请求/响应结构## 实现细节

翻译节点会动态监控主题和服务。  
根据需要，它会实例化发布和订阅的对应一方。  
例如，当某个主题的版本1存在外部发布者，而该主题的版本2存在订阅者时。

内部维护着一个所有已知主题和版本元组的图（这些元组构成图的节点）。  
图通过消息翻译进行连接。  
由于可以注册任意的消息翻译，图中可能包含循环和从一个节点到另一个节点的多条路径。  
因此在主题更新时，会使用最短路径算法遍历图。  
从一个节点移动到下一个节点时，会使用当前主题数据调用消息翻译方法。  
如果某个节点包含已实例化的发布者（因为之前检测到外部订阅者），则会发布数据。  
因此，同一主题的任意版本的多个订阅者都可以接收到对应版本的正确数据。

对于包含多个输入主题的翻译，当所有输入消息可用时翻译才会继续进行。

## 限制

- 服务消息的翻译在 ROS Humble 上不可用，但在 ROS Jazzy 上可用。
  这是因为当前实现依赖于一个尚未在 ROS Humble 中提供的服务 API。
  主题消息的翻译功能完全支持。
- 服务消息仅支持线性历史，即不支持消息拆分或合并。
- 当前翻译节点不处理同时存在两个不同版本主题的发布者和订阅者的情况，这将触发无限循环发布。
  这是指以下问题配置：
  
  ```
  应用 1: 发布 topic_v1，订阅 topic_v1
  应用 2: 发布 topic_v2，订阅 topic_v2
  ```

  实际上这种配置不太可能出现，因为与 FMU 共享的 ROS 主题旨在单向通信（例如 `/fmu/out/vehicle_status` 或 `/fmu/in/trajectory_setpoint`），因此应用程序通常不会同时对同一主题进行发布和订阅。
  如果需要，翻译节点可以扩展以处理此边界情况。

原始文档及需求: https://docs.google.com/document/d/18_RxV1eEjt4haaa5QkFZAlIAJNv9w5HED2aUEiG7PVQ/edit?usp=sharing