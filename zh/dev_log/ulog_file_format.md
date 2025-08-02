

# ULog 文件格式

ULog 是用于记录消息的文件格式。该格式是自描述的，即它包含记录的格式和 [uORB](../middleware/uorb.md) 消息类型。
本文档旨在作为 ULog 文件格式规范文档。
特别是为任何希望编写 ULog 解析器/序列化器并需要解码/编码文件的人而设计。

PX4 使用 ULog 记录与 uORB 主题相关的消息（但不仅限于以下来源）：

- **设备输入：** 传感器、遥控器输入等。
- **内部状态：** CPU负载、姿态、EKF状态等。
- **字符串消息：** `printf` 语句，包括 `PX4_INFO()` 和 `PX4_ERR()`。

该格式对所有二进制类型使用 [小端序](https://en.wikipedia.org/wiki/Endianness) 内存布局（数据类型的最低有效字节（LSB）位于最低内存地址）。

## 数据类型

日志记录中使用了以下二进制类型。它们对应于C语言中的类型。

| 类型              | 字节数 |
| ----------------- | ------------- |
| int8_t, uint8_t   | 1             |
| int16_t, uint16_t | 2             |
| int32_t, uint32_t | 4             |
| int64_t, uint64_t | 8             |
| float             | 4             |
| double            | 8             |
| bool, char        | 1             |

这些类型还可以作为固定大小的数组使用：例如 `float[5]`。

字符串（`char[length]`）的末尾不包含终止符NULL字符`'\0'`。

::: info
字符串比较区分大小写，这一点在[添加订阅](#a-subscription-message)时比较消息名称时需要特别注意。
:::

## ULog 文件结构

ULog 文件包含以下三个部分：

```
----------------------
|       Header       |
----------------------
|    Definitions     |
----------------------
|        Data        |
----------------------
```

每个部分的描述如下。

### 头部分

头部分是一个固定大小的部分，其格式如下（16 字节）：

```plain
----------------------------------------------------------------------
| 0x55 0x4c 0x6f 0x67 0x01 0x12 0x35 | 0x01         | uint64_t       |
| 文件魔数 (7B)                    | 版本 (1B)    | 时间戳 (8B)    |
----------------------------------------------------------------------
```

- **文件魔数 (7 字节):** 文件类型标识符，显示为 "ULogXYZ"（其中 XYZ 是魔数字节序列 `0x01 0x12 0x35`）
- **版本 (1 字节):** 文件格式版本（当前为 1）
- **时间戳 (8 字节):** `uint64_t` 整数，表示日志开始时间（单位：微秒）

### 定义和数据部分消息头

"定义和数据"部分包含多个**消息**。每个消息前都有如下头部结构：

```c
struct message_header_s {
  uint16_t msg_size;
  uint8_t msg_type;
};
```

- `msg_size` 是消息大小（单位：字节），不包含头部。
- `msg_type` 定义内容，为单字节。

::: info
下方消息部分以对应其 `msg_type` 的字符作为前缀。
:::

### 定义部分

定义部分包含软件版本、消息格式、初始参数值等基本信息。

本部分的消息类型包括：

1. [标志位](#b-flag-bits-message)
2. [格式定义](#f-format-message)
3. [信息](#i-information-message)
4. [多信息](#m-multi-information-message)
5. [参数](#p-parameter-message)
6. [默认参数](#q-default-parameter-message)

#### 'B': 标志位消息

::: info
此消息必须紧跟在文件头部分之后，作为第一个消息，以便相对于文件开头具有固定的常量偏移量！
:::

此消息为日志解析器提供日志是否可解析的信息。

```c
struct ulog_message_flag_bits_s {
  struct message_header_s header; // msg_type = 'B'
  uint8_t compat_flags[8];
  uint8_t incompat_flags[8];
  uint64_t appended_offsets[3]; // 附加数据的文件偏移量（如果设置了附加位）
};
```

- `compat_flags`: 兼容标志位

  - 这些标志位表示日志文件中包含的特性是否与任何ULog解析器兼容。
  - `compat_flags[0]`: _DEFAULT_PARAMETERS_ (位0): 如果设置，日志包含[默认参数消息](#q-default-parameter-message)

  其余位当前未定义，必须设置为0。
  这些位可用于未来ULog的兼容性变更。例如，通过定义新位表示新消息类型时，现有解析器会忽略新消息类型。
  这意味着如果设置了未知位，解析器可以忽略这些位。

- `incompat_flags`: 不兼容标志位

  - `incompat_flags[0]`: _DATA_APPENDED_ (位0): 如果设置，日志包含附加数据，且至少一个`appended_offsets`非零。

  其余位当前未定义，必须设置为0。
  可用于引入现有解析器无法处理的破坏性变更。例如，旧版ULog解析器读取包含_DATA_APPENDED概念的新版ULog时，会因包含超出规范的消息/概念而停止解析。
  如果解析器发现设置了未指定的位，必须拒绝解析该日志。

- `appended_offsets`: 附加数据的文件偏移量（0基）。
  如果没有附加数据，所有偏移量必须为零。
  可用于可靠地附加日志中止于消息中间的数据。例如，崩溃转储。

  附加数据的进程应执行以下操作：
  - 设置相关的`incompat_flags`位
  - 将当前为零的第一个`appended_offsets`设置为不包含附加数据的日志文件长度，因为新数据将从此处开始
  - 附加任何属于数据部分的有效消息类型。

未来ULog规范可能会在此消息末尾追加更多字段。
这意味着解析器不能假设此消息的固定长度。
如果`msg_size`大于预期值（当前为40），任何额外字节必须被忽略/丢弃。

#### 'F': 格式消息

格式消息通过单个字符串定义一个消息名及其内部字段。

```c
struct message_format_s {
  struct message_header_s header; // msg_type = 'F'
  char format[header.msg_size];
};
```

- `format` 是一个纯文本字符串，格式为：`message_name:field0;field1;`
  - 字段数量可以是任意的（最少1个），用 `;` 分隔。
  - `message_name`：可以是任意非空字符串，允许字符范围为 `a-zA-Z0-9_-/`（且不能与任何 [基本类型](#数据类型) 相同）。

`field` 的格式为：`type field_name`，对于数组则使用 `type[array_length] field_name`（仅支持固定大小数组）。
`field_name` 必须由字符集 `a-zA-Z0-9_` 组成。

`type` 可以是 [基本二进制类型](#数据类型) 或另一个格式定义的 `message_name`（支持嵌套使用）。

- 类型可以在定义前使用。
  - 例如：消息 `MessageA:MessageB[2] msg_b` 可以在 `MessageB:uint_8[3] data` 之前出现。
- 可以有任意嵌套但 **不能有循环依赖**。
  - 例如：`MessageA:MessageB[2] msg_b` 与 `MessageB:MessageA[4] msg_a` 之间存在循环依赖。

部分字段名具有特殊含义：

- `timestamp`：每个 [订阅消息](#a-subscription-message) 的消息格式必须包含 timestamp 字段（例如仅作为其他格式嵌套定义部分使用的消息格式可以不包含 timestamp 字段）
  - 其类型必须为 `uint64_t`。
  - 单位为微秒。
  - 对于相同 `msg_id` 的消息序列，timestamp 必须始终单调递增（同一订阅）。
- `_padding{}`：以 `_padding` 开头的字段名（如 `_padding[3]`）不应显示，读取器必须忽略其数据。
  - 写入器可以插入此类字段以确保正确对齐。
  - 如果填充字段是最后一个字段，则该字段可能不会被记录，以避免写入冗余数据。
  - 这意味着 `message_data_s.data` 的大小将减少填充部分的大小。
  - 但消息在嵌套定义中使用时仍需保留填充。

通常情况下，消息字段不一定对齐（即消息内的字段偏移量不一定是其数据大小的倍数），因此读取器必须始终使用适当的内存复制方法访问单个字段。

#### 'I': 信息消息

信息消息定义了任意信息的字典类型定义`键`:`值`对，包括但不限于硬件版本、软件版本、软件构建工具链等。

```c
struct ulog_message_info_header_s {
  struct message_header_s header; // msg_type = 'I'
  uint8_t key_len;
  char key[key_len];
  char value[header.msg_size-1-key_len]
};
```

- `key_len`: 键值的长度
- `key`: 以`类型 名称`形式包含键字符串，例如`char[value_len] sys_toolchain_ver`。名称的有效字符为`a-zA-Z0-9_-/`。类型可为[基本类型及数组](#数据类型)
- `value`: 包含与`键`对应的值数据（长度为`value_len`），例如`9.4.0`

::: info
信息消息中定义的键必须唯一。即不能有相同键值的多个定义。
:::

解析器可将信息消息存储为字典。

预定义的信息消息如下：

| 键                                 | 描述                                 | 值示例  |
| ----------------------------------- | ----------------------------------- | ------- |
| `char[value_len] sys_name`          | 系统名称                          | "PX4"   |
| `char[value_len] ver_hw`            | 硬件版本（板载）                  | "PX4FMU_V4" |
| `char[value_len] ver_hw_subtype`    | 板载子版本（变体）                | "V2"    |
| `char[value_len] ver_sw`            | 软件版本（git标签）               | "7f65e01" |
| `char[value_len] ver_sw_branch`     | git分支                           | "master" |
| `uint32_t ver_sw_release`           | 软件版本（见下文）                | 0x010401ff |
| `char[value_len] sys_os_name`       | 操作系统名称                      | "Linux" |
| `char[value_len] sys_os_ve`r        | 操作系统版本（git标签）           | "9f82919" |
| `uint32_t ver_os_release`           | 操作系统版本（见下文）            | 0x010401ff |
| `char[value_len] sys_toolchain`     | 工具链名称                        | "GNU GCC" |
| `char[value_len] sys_toolchain_ver` | 工具链版本                        | "6.2.1" |
| `char[value_len] sys_mcu`           | 芯片名称及版本                    | "STM32F42x, rev A" |
| `char[value_len] sys_uuid`          | 机体的唯一标识符（例如MCU ID）    | "392a93e32fa3"... |
| `char[value_len] log_type`          | 日志类型（未指定时为完整日志）    | "mission" |
| `char[value_len] replay`            | 回放模式下的日志文件名            | "log001.ulg" |
| `int32_t time_ref_utc`              | UTC时间偏移（秒）                 | -3600   |

::: info
`value_len`表示`值`的数据大小，这在`键`中已有描述。
:::

- `ver_sw_release`和`ver_os_release`的格式为：0xAABBCCTT，其中AA表示**主版本**，BB表示**次版本**，CC表示补丁，TT表示**类型**
  - **类型**定义如下：`>= 0`: 开发版，`>= 64`: alpha版本，`>= 128`: beta版本，`>= 192`: RC版本，`== 255`: 正式版本
  - 例如，`0x010402FF`表示正式版本v1.4.2

此消息也可用于数据部分（但这不是首选部分）。

#### 'M': 多信息消息

多信息消息的作用与信息消息相同，但用于长消息或多条具有相同键的消息。

```c
struct ulog_message_info_multiple_header_s {
  struct message_header_s header; // msg_type = 'M'
  uint8_t is_continued; // 可用于数组
  uint8_t key_len;
  char key[key_len];
  char value[header.msg_size-2-key_len]
};
```

- `is_continued` 可用于分割消息：如果设置为1，则表示它是具有相同键的前一条消息的延续部分。

解析器可以将所有信息多消息存储为二维列表，使用与日志中消息出现顺序相同的顺序。

有效的名称和类型与信息消息相同。

#### 'P': 参数消息

_Parameter_ 消息在 _Definitions_ 部分中定义了机体开始记录时的参数值。它使用与 [信息消息](#i-information-message) 相同的格式。

```c
struct message_info_s {
  struct message_header_s header; // msg_type = 'P'
  uint8_t key_len;
  char key[key_len];
  char value[header.msg_size-1-key_len]
};
```

如果某个参数在运行时动态变化，该消息也可以在 [数据部分](#与定义部分共享的消息) 使用。

数据类型仅限于 `int32_t` 和 `float`。名称的有效字符：`a-zA-Z0-9_-/`。

#### 'Q': 默认参数消息

默认参数消息定义了给定机体和配置下的参数默认值。

```c
struct ulog_message_parameter_default_header_s {
  struct message_header_s header; // msg_type = 'Q'
  uint8_t default_types;
  uint8_t key_len;
  char key[key_len];
  char value[header.msg_size-2-key_len]
};
```

- `default_types` 是一个位域，用于定义该值所属的组别。
  - 至少需要设置一个位：
    - `1<<0`: 系统级默认值
    - `1<<1`: 当前配置的默认值（例如机架）

日志文件可能不包含所有参数的默认值。  
在这些情况下，默认值等于参数值，且不同默认类型将被独立处理。

此消息也可用于数据部分，其数据类型和命名规则与参数消息相同。

本节在第一个 [Subscription Message](#a-subscription-message) 或 [Logging](#l-logged-string-message) 消息开始前结束，以先出现者为准。

### 数据部分

_Data_ 部分的消息类型包括：

1. [订阅](#a-subscription-message)
2. [取消订阅](#r-unsubscription-message)
3. [记录的数据](#d-logged-data-message)
4. [记录的字符串](#l-logged-string-message)
5. [带标签的记录字符串](#c-tagged-logged-string-message)
6. [同步](#s-synchronization-message)
7. [断开标记](#o-dropout-message)
8. [信息](#i-information-message)
9. [多信息](#m-multi-information-message)
10. [参数](#p-parameter-message)
11. [默认参数](#q-default-parameter-message)

#### `A`: 订阅消息

通过名称订阅消息并为其分配一个在[记录数据消息](#d-logged-data-message)中使用的ID。
这必须在第一个对应的[记录数据消息](#d-logged-data-message)之前出现。

```c
struct message_add_logged_s {
  struct message_header_s header; // msg_type = 'A'
  uint8_t multi_id;
  uint16_t msg_id;
  char message_name[header.msg_size-3];
};
```

- `multi_id`: 相同的消息格式可以有多个实例，例如系统包含两个相同类型的传感器时。默认且第一个实例必须为0。
- `msg_id`: 用于匹配[记录数据消息](#d-logged-data-message)数据的唯一ID。首次使用必须设为0，然后递增。
  - 不同的订阅不得重复使用相同的`msg_id`。
- `message_name`: 要订阅的消息名称。
  必须匹配其中一个[格式消息](#f-format-message)定义。

#### `R`: 取消订阅消息

取消订阅一条消息，表示该消息将不再被记录（目前未使用）。

```c
struct message_remove_logged_s {
  struct message_header_s header; // 消息类型 = 'R'
  uint16_t msg_id;
};
```

#### 'D'：记录数据消息

```c
struct message_data_s {
  struct message_header_s header; // msg_type = 'D'
  uint16_t msg_id;
  uint8_t data[header.msg_size-2];
};
```

- `msg_id`: 如 [订阅消息](#a-subscription-message) 中定义  
- `data` 包含由 [格式消息](#f-format-message) 定义的二进制记录数据  

请参见上文关于填充字段的特殊处理。

#### 'L': 日志字符串消息

日志字符串消息，即`printf()`输出。

```c
struct message_logging_s {
  struct message_header_s header; // msg_type = 'L'
  uint8_t log_level;
  uint64_t timestamp;
  char message[header.msg_size-9]
};
```

- `timestamp`: 单位为微秒
- `log_level`: 与Linux内核相同：

| 名称    | 级别值 | 含义                          |
| ------- | ----------- | -------------------------------- |
| EMERG   | '0'         | 系统无法使用               |
| ALERT   | '1'         | 必须立即采取行动 |
| CRIT    | '2'         | 严重条件              |
| ERR     | '3'         | 错误条件                 |
| WARNING | '4'         | 警告条件               |
| NOTICE  | '5'         | 正常但重要条件 |
| INFO    | '6'         | 信息性消息                    |
| DEBUG   | '7'         | 调试级别消息             |

#### 'C'：带标签的日志字符串消息

```c
struct message_logging_tagged_s {
  struct message_header_s header; // msg_type = 'C'
  uint8_t log_level;
  uint16_t tag;
  uint64_t timestamp;
  char message[header.msg_size-11]
};
```

- `tag`: 表示日志消息字符串来源的ID。根据系统架构，它可以代表进程、线程或类。

  - 例如，对于运行多个进程控制不同载荷、外部磁盘、串口设备等的机载计算机，可以通过将`uint16_t enum`编码到结构体的`tag`属性中来实现：

  ```c
  enum class ulog_tag : uint16_t {
    unassigned,
    mavlink_handler,
    ppk_handler,
    camera_handler,
    ptp_handler,
    serial_handler,
    watchdog,
    io_service,
    cbuf,
    ulg
  };
  ```

- `timestamp`: 微秒时间戳
- `log_level`: 与Linux内核相同：

| 名称    | 级别值 | 含义                          |
| ------- | ----------- | -------------------------------- |
| EMERG   | '0'         | 系统不可用               |
| ALERT   | '1'         | 必须立即采取行动 |
| CRIT    | '2'         | 严重条件              |
| ERR     | '3'         | 错误条件                 |
| WARNING | '4'         | 警告条件               |
| NOTICE  | '5'         | 正常但重要条件 |
| INFO    | '6'         | 信息性                    |
| DEBUG   | '7'         | 调试级别消息             |

#### 'S'：同步消息

同步消息，以便读者可以通过查找下一个同步消息来从损坏的消息中恢复。

```c
struct message_sync_s {
  struct message_header_s header; // msg_type = 'S'
  uint8_t sync_magic[8];
};
```

- `sync_magic`: [0x2F, 0x73, 0x13, 0x20, 0x25, 0x0C, 0xBB, 0x12]

#### 'O'：丢包消息

标记一个持续时间为毫秒的丢包（丢失的日志消息）。

例如，如果设备处理速度不够快，可能会发生丢包。

```c
struct message_dropout_s {
  struct message_header_s header; // msg_type = 'O'
  uint16_t duration;
};
```

#### 与定义部分共享的消息

由于定义部分和数据部分使用相同的消头格式，因此它们共享以下列出的消息：

- [信息消息](#i-information-message)。  
- [多信息消息](#m-multi-information-message)  
- [参数消息](#p-parameter-message)  
  - 对于 _数据_ 部分，当参数值发生变化时使用参数消息  
- [默认参数消息](#q-default-parameter-message)

## 解析器的要求

有效的 ULog 解析器必须满足以下要求：

- 必须忽略未知消息（但可以打印警告）
- 必须能够解析未来/未知的文件格式版本（但可以打印警告）
- 必须拒绝解析包含未知不兼容位的记录（[标志位消息](#b-flag-bits-message)中`incompat_flags`被设置），这意味着记录包含解析器无法处理的破坏性变更
- 解析器必须能够正确处理在消息中间突然结束的记录文件
  未完成的消息应直接丢弃
- 对于追加数据：解析器可以假设数据部分存在，即偏移量指向定义部分之后的位置
  - 追加的数据必须被视为常规数据部分的一部分

## 已知的解析器实现

- PX4-Autopilot: C++
  - [日志模块](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/logger)
  - [回放模块](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/replay)
  - [hardfault_log模块](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/hardfault_log): 追加硬故障崩溃数据。
- [pyulog](https://github.com/PX4/pyulog): Python, ULog读写库，附带命令行脚本。
- [ulog_cpp](https://github.com/PX4/ulog_cpp): C++, ULog读写库。
- [FlightPlot](https://github.com/PX4/FlightPlot): Java, 日志绘图工具。
- [MAVLink](https://github.com/mavlink/mavlink): 通过MAVLink传输ULog消息（注意附加数据不支持，至少对于截断的消息不支持）。
- [QGroundControl](https://github.com/mavlink/qgroundcontrol): C++, 通过MAVLink传输ULog和GeoTagging的最小解析。
- [mavlink-router](https://github.com/01org/mavlink-router): C++, 通过MAVLink传输ULog。
- [MAVGAnalysis](https://github.com/ecmnet/MAVGCL): Java, 通过MAVLink传输ULog和用于绘图分析的解析器。
- [PlotJuggler](https://github.com/facontidavide/PlotJuggler): C++/Qt日志和时间序列绘图应用。自2.1.3版本起支持ULog。
- [ulogreader](https://github.com/maxsun/ulogreader): JavaScript, ULog读取器和解析器，输出JSON对象格式日志。
- [Foxglove](https://foxglove.dev): 集成的机器人数据可视化诊断工具，支持ULog文件。
- [TypeScript ULog解析器](https://github.com/foxglove/ulog): TypeScript, ULog读取器，输出JS对象。

## 文件格式版本历史

### 版本2的变更

- 新增了[多信息消息](#m-multi-information-message)和[标志位消息](#b-flag-bits-message)功能，并实现了向日志追加数据的能力。
  - 此功能用于向现有日志中添加坠机数据。
  - 如果向消息中间被截断的日志追加数据，将无法被版本1的解析器解析。
- 除此之外，如果解析器忽略未知消息，则仍保持向前和向后兼容性。