# 事件接口

<Badge type="tip" text="PX4 v1.13" />

_事件接口_ 提供了一个系统级API用于事件通知，这些事件通过_MAVLink Events Service_ 发布到地面站（GCS）和其他组件，同时记录在[系统日志](../dev_log/logging.md)中。

该接口可用于发布状态变化或任何类型的事件，包括诸如武装就绪、校准完成和达到目标起飞高度等事件。

::: info
事件接口将取代PX4代码中`mavlink_log_*`调用（以及MAVLink中的`STATUS_TEXT`消息）在PX4 v1.13及后续版本中的事件通知功能。
在迁移期间[两种方式都将支持](#backward-compatibility)。
:::

## 使用方法

### 基础用法

要使用该API，添加以下包含：

```cpp
#include <px4_platform_common/events.h>
```

然后在所需代码位置定义并发送事件：

```cpp
events::send(events::ID("mymodule_test"), events::Log::Info, "Test Message");
```

#### 向后兼容性

对于不支持事件接口的旧版GCS，PX4当前会将所有事件同时作为`mavlink_log_*` `STATUSTEXT`消息发送。
此外，消息必须带有附加的制表符（`\t`）以便新版GCS可以忽略并仅显示事件。

因此每当添加事件时，必须同时添加`mavlink_log_`调用。例如：

```cpp
mavlink_log_info(mavlink_log_pub, "Test Message\t");
events::send(events::ID("mymodule_test"), events::Log::Info, "Test Message");
```

所有此类`mavlink_log_`调用将在下一个版本后移除。

### 详细用法

上述是极简示例，这是一个更全面的示例：

```cpp
uint8_t arg1 = 0;
float arg2 = -1.f;
/* EVENT
 * @description
 * 这是详细的事件描述。
 *
 * - arg1的值: {1}
 * - arg2的值: {2:.1}
 *
 * <profile name="dev">
 * (此段落仅供开发者查看)。
 * 此行为可通过参数<param>COM_EXAMPLE</param>进行配置。
 * </profile>
 *
 * 链接至文档: <a>https://docs.px4.io</a>
 */
events::send<uint8_t, float>(events::ID("event_name"),
	{events::Log::Error, events::LogInternal::Info}, "Event Message", arg1, arg2);
```

解释和要求：

- `/* EVENT`: 此标签表示注释定义了后续事件的元数据。
- **event_name**: 事件名称（`events::ID(event_name)`）。
  - 必须在整个PX4源代码中唯一。
    通常约定以模块名或大模块的源文件名作为前缀。
  - 必须是有效的变量名，即不能包含空格、冒号等。
  - 从此名称通过哈希函数派生出24位事件ID。
    只要事件名称不变，ID也会保持不变。
- **日志级别**:

  - 有效日志级别与MAVLink [MAV_SEVERITY](https://mavlink.io/en/messages/common.html#MAV_SEVERITY)枚举相同。
    按重要性降序排列如下：

    ```plain
    Emergency,
    Alert,
    Critical,
    Error,
    Warning,
    Notice,
    Info,
    Debug,
    Disabled,
    ```

  - 上述我们指定了外部和内部日志级别，分别对应GCS用户显示和日志文件中的级别：`{events::Log::Error, events::LogInternal::Info}`。
    对于大多数情况，您可以传递单个日志级别，这将同时用于外部和内部情况。
    在某些情况下设置不同的日志级别是有意义的。
    例如RTL紧急降落动作：用户应看到警告/错误，而在日志中它是预期的系统响应，因此可以设置为`Info`。

- **事件消息**:
  - 单行，简短的事件消息。
    可以包含模板占位符参数（例如`{1}`）。更多信息请参见下文。
- **事件描述**:
  - 详细且可选的事件描述。
  - 可以是多行/段落。
  - 可以包含模板占位符参数（例如`{2}`）和受支持的标签（见下文）

#### 参数和枚举

事件可以包含一组固定的参数，这些参数可以通过模板占位符插入消息或描述中（例如`{2:.1m}` - 见下一节）。

有效类型：`uint8_t`，`int8_t`，`uint16_t`，`int16_t`，`uint32_t`，`int32_t`，`uint64_t`，`int64_t`和`float`。

您还可以使用枚举作为参数：

- PX4特定/自定义事件枚举应定义在[src/lib/events/enums.json](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/events/enums.json)中，然后可以作为事件参数使用，形式为`events::send<events::px4::enums::my_enum_t>(...)`。
- MAVLink "common" 事件定义在[mavlink/libevents/events/common.json](https://github.com/mavlink/libevents/blob/master/events/common.json)中，可以作为事件参数使用，形式为`events::send<events::common::enums::my_enum_t>(...)`。

#### 文本格式

事件消息描述的文本格式：

- 字符可以通过`\\`转义

  需要转义的字符：`\\\\`，`\\<`，`\\{`。

- 受支持的标签：

  - 配置文件：`<profile name="[!]NAME">CONTENT</profile>`

    `CONTENT`仅在名称与配置的配置文件匹配时显示。
    可用于例如隐藏开发者信息给最终用户。

  - URL：`<a [href="URL"]>CONTENT</a>`。
    如果未设置`href`，则使用`CONTENT`作为`URL`（即`<a>https://docs.px4.io</a>`将被解释为`<a href="https://docs.px4.io">https://docs.px4.io</a>`）
  - 参数：`<param>PARAM_NAME</param>`。
  - 标题：`<h1>...<h6>`。

- 模板占位符使用`{数字}`表示参数位置。

#### 日志记录

事件记录在系统日志中，格式为`[事件ID] [消息]`。

#### 实现细节

事件接口通过预定义的宏实现，这些宏在编译时处理事件注册和消息生成。具体实现细节见[src/lib/events/events.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/events/events.cpp)。