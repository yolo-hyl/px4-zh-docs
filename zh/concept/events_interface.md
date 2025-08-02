

# 事件接口

<Badge type="tip" text="PX4 v1.13" />

_事件接口_ 提供了系统级事件通知API，通过_MAVLink 事件服务_ 向地面站（GCS）及其他组件发布事件，并存储在 [系统日志](../dev_log/logging.md) 中。

该接口可用于发布状态变化或其他任何类型事件的通知，包括诸如武装准备就绪、校准完成以及达到目标起飞高度等事件。

::: info
事件接口将替代PX4代码中`mavlink_log_*`调用（以及MAVLink中的`STATUS_TEXT`消息）用于事件通知，适用于PX4 v1.13及后续版本。
在[过渡时期](#向后兼容性)内两种方法将同时支持。
:::

## 使用

### 基本

要使用此API，请添加以下包含：

```cpp
#include <px4_platform_common/events.h>
```

然后在所需的代码位置定义并发送事件：

```cpp
events::send(events::ID("mymodule_test"), events::Log::Info, "Test Message");
```

#### 向后兼容性

对于不支持事件接口的旧版GCS，PX4目前会将所有事件同时作为`mavlink_log_*` `STATUSTEXT`消息发送。  
此外，消息必须附加制表符(`\t`)，以便新版GCS可以忽略该部分并仅显示事件。

因此每次添加事件时，都要确保同时添加`mavlink_log_`调用。例如：

```cpp
mavlink_log_info(mavlink_log_pub, "Test Message\t");
events::send(events::ID("mymodule_test"), events::Log::Info, "Test Message");
```

所有此类`mavlink_log_`调用将在下一个版本中被删除。

### 详细

以上是最小示例，这是一个更完整的示例：

```cpp
uint8_t arg1 = 0;
float arg2 = -1.f;
/* EVENT
 * @description
 * 这是详细的事件描述。
 *
 * - arg1 的值: {1}
 * - arg2 的值: {2:.1}
 *
 * <profile name="dev">
 * (该段落仅面向开发者显示)。
 * 该行为可通过参数 <param>COM_EXAMPLE</param> 配置。
 * </profile>
 *
 * 文档链接: <a>https://docs.px4.io</a>
 */
events::send<uint8_t, float>(events::ID("event_name"),
	{events::Log::Error, events::LogInternal::Info}, "Event Message", arg1, arg2);
```

解释和要求:

- `/* EVENT`: 该标签表示注释定义了后续事件的元数据。
- **事件名称**: (`events::ID(event_name)`)
  - 必须在整个 PX4 源代码中唯一。
    通常约定以模块名或大型模块的源文件名作为前缀。
  - 必须是有效的变量名，即不能包含空格、冒号等。
  - 通过哈希函数从该名称派生出 24 位事件 ID。
    这意味着只要事件名称保持不变，ID 也会保持不变。
- **日志级别**:

  - 有效日志级别与 MAVLink [MAV_SEVERITY](https://mavlink.io/en/messages/common.html#MAV_SEVERITY) 枚举相同。
    按重要性降序排列如下:

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

  - 上述代码中指定了外部和内部日志级别，分别是显示给地面站用户和日志文件中的级别：`{events::Log::Error, events::LogInternal::Info}`。
    多数情况下可以传递单一日志级别，该级别将同时用于外部和内部。
    在某些情况下使用两种不同的日志级别是有意义的。
    例如 RTL 失效保护动作：用户应看到的是警告/错误级别，而在日志中，它是预期的系统响应，因此可以设置为 `Info`。

- **事件消息**:
  - 单行的事件简短消息。
    可包含参数占位符（如 `{1}`）。更多详情请参见下文。
- **事件描述**:
  - 详细的、可选的事件描述。
  - 可以包含多行/段落。
  - 可以包含参数占位符（如 `{2}`）和受支持的标签（见下文）

#### 参数和枚举

事件可以包含一组固定的参数，这些参数可以通过模板占位符插入消息或描述中（例如 `{2:.1m}` - 请参见下一节）。

有效类型：`uint8_t`、`int8_t`、`uint16_t`、`int16_t`、`uint32_t`、`int32_t`、`uint64_t`、`int64_t` 和 `float`。

您也可以将枚举类型用作参数：

- PX4 特定/自定义的枚举类型应在 [src/lib/events/enums.json](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/events/enums.json) 中定义，之后可通过 `events::send<events::px4::enums::my_enum_t>(...)` 形式作为事件参数使用。
- MAVLink "common" 事件在 [mavlink/libevents/events/common.json](https://github.com/mavlink/libevents/blob/master/events/common.json) 中定义，可通过 `events::send<events::common::enums::my_enum_t>(...)` 形式作为事件参数使用。

#### 文本格式

事件消息描述的文本格式：

- 可通过 \\ 转义字符  
  需要转义的字符: '\\\\', '\\<', '\\{'

- 支持的标签:  
  - 配置文件: `<profile name="[!]NAME">CONTENT</profile>`  
    只有当名称与配置的配置文件匹配时，才会显示CONTENT。  
    可用于例如从最终用户界面隐藏开发者信息。  

  - 超链接: `<a [href="URL"]>CONTENT</a>`。  
    如果未设置`href`，则将`CONTENT`用作`URL`（例如`<a>https://docs.px4.io</a>`会被解释为`<a href="https://docs.px4.io">https://docs.px4.io</a>`）  
  - 参数: `<param>PARAM_NAME</param>`  
  - 不允许嵌套相同类型的标签  

- 参数: 遵循Python语法的模板占位符，采用1开始的索引（而不是0）  
  - 通用格式: `{ARG_IDX[:.NUM_DECIMAL_DIGITS][UNIT]}`  
    单位:  
    - m: 水平距离（米）  
    - m_v: 垂直距离（米）  
    - m^2: 面积（平方米）  
    - m/s: 速度（米/秒）  
    - C: 温度（摄氏度）  
  - `NUM_DECIMAL_DIGITS`仅对实数参数有意义

## 日志记录

事件根据内部日志级别进行记录，[飞行回顾](../log/flight_review.md)会显示这些事件。

::: info
飞行回顾会基于PX4主分支下载元数据，因此如果定义尚未在主分支上，则只能显示事件ID。
:::

## 实现

在 PX4 构建过程中，只有代码会被编译器直接添加到二进制文件中（即事件 ID、日志级别和任何参数）。

所有事件的元数据会构建到一个独立的 JSON 元数据文件中（使用 Python 脚本扫描整个源代码中的事件调用生成）。

### 发布事件元数据到地面站

事件元数据 JSON 文件被编译到固件中（和/或托管在互联网上），并通过 [MAVLink组件元数据服务](https://mavlink.io/en/services/component_information.html) 提供给地面站。  
这确保了元数据始终与在机体上运行的代码保持同步。  

该过程与 [参数元数据](../advanced/parameters_and_configurations.md#publishing-parameter-metadata-to-a-gcs) 的发布方式相同。  
欲了解更多详情，请参阅 [PX4 Metadata (Translation & Publication)](../advanced/px4_metadata.md)