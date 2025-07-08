# ULog 文件格式

ULog 是用于记录消息的文件格式。该格式具有自描述特性，即它包含记录的格式和 [uORB](../middleware/uorb.md) 消息类型。
本文档旨在作为 ULog 文件格式规范文档。
它特别适用于任何希望编写 ULog 解析器/序列化器并需要解码/编码文件的人员。

PX4 使用 ULog 记录与以下来源相关的（但不仅限于）uORB 主题消息：

- **设备输入:** 传感器、遥控输入等
- **内部状态:** CPU 负载、姿态、EKF 状态等
- **字符串消息:** `printf` 语句，包括 `PX4_INFO()` 和 `PX4_ERR()` 

该格式对所有二进制类型使用 [little endian](https://en.wikipedia.org/wiki/Endianness) 内存布局（数据类型的最低有效字节 (LSB) 位于最低内存地址）。

## 数据类型

日志记录中使用了以下二进制类型。它们对应于 C 语言中的类型。

| 类型              | 字节大小 |
| ----------------- | ------------- |
| int8_t, uint8_t   | 1             |
| int16_t, uint16_t | 2             |
| int32_t, uint32_t | 4             |
| int64_t, uint64_t | 8             |
| float             | 4             |
| double            | 8             |
| bool, char        | 1             |

此外，这些类型可以作为固定大小的数组使用，例如：`float[5]`。

字符串(`char[length]`)的末尾不包含终止空字符`'\0'`。

::: info
字符串比较区分大小写，这在[添加订阅](#a-subscription-message)时比较消息名称时需要注意。
:::The ULOG (Unified Logging) file format is a structured, binary logging system designed for embedded systems, drones, or similar applications. It organizes data into **Definitions** and **Data** sections, with each section containing specific message types. Here's a breakdown of the key components and their interactions:

---

### **Definitions Section**
This section defines the structure of the data and metadata. It must appear **before** the Data section.

#### **1. Format Message (`B`)**  
- **Purpose**: Defines the structure of a message type.  
- **Structure**:  
  ```c
  struct message_add_logged_s {
      uint8_t multi_id;       // Instance identifier (e.g., for multiple sensors).
      uint16_t msg_id;        // Unique ID for the message (starts at 0, increments).
      char message_name[];    // Name of the message (must match a Format Message).
  }
  ```
- **Behavior**:  
  - `multi_id` allows multiple instances of the same message type (e.g., two GPS sensors).  
  - `msg_id` is used in **Logged Data Messages** (`D`) to reference the message.  
  - Must appear **before** the first `D` message.

#### **2. Parameter Messages (`P` and `Q`)**  
- **`P` (Parameter Message)**:  
  - Stores initial parameter values when logging starts.  
  - Example: `P: key=PID_RATE, value=1.5`.  
- **`Q` (Default Parameter Message)**:  
  - Defines default values for a parameter (e.g., system-wide or configuration-specific).  
  - `default_types` is a bitfield:  
    - Bit 0: System-wide default.  
    - Bit 1: Configuration-specific default.  

#### **3. Information Messages (`I` and `M`)**  
- **`I` (Information Message)**:  
  - Key-value metadata (e.g., `I: key=HW_MODEL, value=DroneX`).  
- **`M` (Multi-Information Message)**:  
  - Extends `I` for long or repeated data.  
  - `is_continued` indicates if the message continues the previous one with the same key.  

#### **4. Flag Message (`F`)**  
- **Optional**: Holds system-wide flags (e.g., `F: 0x01` for a safety mode).  
- Rarely used in modern implementations.

---

### **Data Section**
Contains the actual logged data and runtime updates.

#### **1. Subscription Message (`A`)**  
- **Purpose**: Links a message name to a `msg_id` for logging.  
- **Structure**:  
  ```c
  struct message_add_logged_s {
      uint8_t multi_id;       // Instance identifier.
      uint16_t msg_id;        // Must be unique and increment from 0.
      char message_name[];    // Must match a Format Message.
  }
  ```
- **Behavior**:  
  - Must precede the first `D` message for the same `msg_id`.  
  - `multi_id` allows multiple instances of the same message type.

#### **2. Logged Data Message (`D`)**  
- **Purpose**: Logs binary data for a subscribed message.  
- **Structure**:  
  ```c
  struct message_data_s {
      uint16_t msg_id;        // References a Subscription Message.
      uint8_t data[];         // Binary data as defined by the Format Message.
  }
  ```
- **Behavior**:  
  - `data` must conform to the structure defined in the corresponding `B` message.  
  - Padding fields (e.g., `__pad__`) are ignored during parsing.

#### **3. String Logging Messages (`L` and `C`)**  
- **`L` (Logged String)**:  
  - Logs debug/info strings (e.g., `L: level=6, message="Motor initialized"`).  
  - `log_level` matches Linux kernel severity levels (0-7).  
- **`C` (Tagged Logged String)**:  
  - Extends `L` with a `tag` to identify the source (e.g., process/thread).  
  - Example: `C: tag=0x01, message="GPS signal lost"`.

#### **4. Synchronization Message (`S`)**  
- **Purpose**: Helps parsers recover from corrupted data.  
- **Structure**:  
  ```c
  struct message_sync_s {
      uint8_t sync_magic[8];  // Fixed value: [0x2F, 0x73, 0x13, 0x20, 0x25, 0x0C, 0xBB, 0x12]
  }
  ```

#### **5. Dropout Message (`O`)**  
- **Purpose**: Marks data loss (e.g., due to buffer overflow).  
- **Structure**:  
  ```c
  struct message_dropout_s {
      uint16_t duration;      // Duration in milliseconds.
  }
  ```

#### **6. Shared Messages**  
- **`I`/`M` (Information/Multi-Information)**:  
  - Can appear in Data section for runtime metadata updates.  
- **`P`/`Q` (Parameter/Default Parameter)**:  
  - `P` updates parameter values during runtime.  
  - `Q` provides default values for analysis.

---

### **Key Interactions**
1. **Message Flow**:  
   - **Definitions Section**: Define message formats (`B`), initial parameters (`P`/`Q`), and metadata (`I`/`M`).  
   - **Data Section**: Subscribe messages (`A`), log data (`D`), and update parameters (`P`).  

2. **Subscription and Logging**:  
   - A `B` message defines the structure of a message.  
   - An `A` message assigns a `msg_id` to the message.  
   - `D` messages use the `msg_id` to log data.  

3. **Parameter Updates**:  
   - `P` updates parameter values during runtime (e.g., after user input).  
   - `Q` provides default values for context (e.g., system defaults vs user settings).  

4. **Error Handling**:  
   - `S` messages act as sync points for parsers to recover from corruption.  
   - `O` messages indicate data loss, helping analyze log integrity.

---

### **Example Workflow**
1. **Definitions**:  
   - `B: multi_id=0, msg_id=0, message_name="GPS"`  
   - `P: key=GPS_BAUD, value=57600`  
   - `I: key=HW_VERSION, value=1.2`  

2. **Data**:  
   - `A: multi_id=0, msg_id=0, message_name="GPS"`  
   - `D: msg_id=0, data=[binary GPS data...]`  
   - `P: key=GPS_BAUD, value=115200` (runtime update)  

3. **Logging**:  
   - `L: level=6, message="GPS lock acquired"`  

---

### **Common Use Cases**
- **Debugging**: Use `L`/`C` messages to trace system events.  
- **Performance Analysis**: Parse `D` messages to analyze sensor data over time.  
- **System Health**: Monitor `O` messages to detect data loss and `I`/`M` for metadata.  

This structure ensures efficient logging and analysis while maintaining flexibility for runtime updates and recovery.## 解析器的要求

有效的 ULog 解析器必须满足以下要求：

- 必须忽略未知消息（但可以打印警告）
- 必须能够解析未来/未知文件格式版本（但可以打印警告）
- 必须拒绝解析包含未知不兼容位设置的日志（即日志的[标志位消息](#b-flag-bits-message)中设置了`incompat_flags`），这意味着日志包含解析器无法处理的破坏性变更
- 解析器必须能够正确处理在消息中间突然结束的日志
  未完成的消息应直接丢弃
- 对于附加数据：解析器可以假设数据部分存在，即偏移量指向定义部分之后的位置
  - 附加数据必须被视为常规数据部分的一部分## 已知的解析器实现

- PX4-Autopilot：C++
  - [logger module](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/logger)
  - [replay module](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/replay)
  - [hardfault_log module](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/hardfault_log)：追加硬故障崩溃数据。
- [pyulog](https://github.com/PX4/pyulog)：Python，ULog 读写库及命令行脚本。
- [ulog_cpp](https://github.com/PX4/ulog_cpp)：C++，ULog 读写库。
- [FlightPlot](https://github.com/PX4/FlightPlot)：Java，日志绘图工具。
- [MAVLink](https://github.com/mavlink/mavlink)：通过 MAVLink 传输 ULog 消息（注意不支持数据追加，至少对于截断的消息不支持）。
- [QGroundControl](https://github.com/mavlink/qgroundcontrol)：C++，通过 MAVLink 传输 ULog 并进行最小解析用于地理标记。
- [mavlink-router](https://github.com/01org/mavlink-router)：C++，通过 MAVLink 传输 ULog。
- [MAVGAnalysis](https://github.com/ecmnet/MAVGCL)：Java，通过 MAVLink 传输 ULog 并解析用于绘图分析。
- [PlotJuggler](https://github.com/facontidavide/PlotJuggler)：C++/Qt 应用程序，用于绘制日志和时间序列。自 2.1.3 版本起支持 ULog。
- [ulogreader](https://github.com/maxsun/ulogreader)：JavaScript，ULog 读取器及解析器，输出 JSON 对象格式日志。
- [Foxglove](https://foxglove.dev)：机器人数据的集成可视化与诊断工具，支持 ULog 文件。
- [TypeScript ULog parser](https://github.com/foxglove/ulog)：TypeScript，ULog 读取器，输出 JS 对象。

## 文件格式版本历史

### 版本2的变更

- 新增了 [多信息消息](#m-multi-information-message) 和 [标志位消息](#b-flag-bits-message) 以及向日志追加数据的功能。
  - 该功能用于将坠毁数据追加到现有日志中。
  - 如果向中途被截断的消息日志追加数据，将无法通过版本1解析器解析。
- 只要解析器忽略未知消息，就保持向前和向后兼容性。