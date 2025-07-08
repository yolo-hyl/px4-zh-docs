# 驱动开发

PX4 设备驱动基于 [Device](https://github.com/PX4/PX4-Autopilot/tree/main/src/lib/drivers/device) 框架。

## 创建驱动

PX4 几乎完全通过 [uORB](../middleware/uorb.md) 消费数据。通用外设类型的驱动必须发布正确的 uORB 消息（例如：陀螺仪、加速度计、气压计等）。

创建新驱动的最佳方法是使用类似驱动作为模板（参见 [src/drivers](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers)）。

::: info
有关特定 I/O 总线和传感器的详细信息可能可在 [Sensor and Actuator Buses](../sensor_bus/index.md) 章节中找到。
:::

::: info
发布正确的 uORB 主题是驱动 *必须* 遵循的唯一模式。
:::

## 核心架构

PX4 是一个 [响应式系统](../concept/architecture.md)，使用 [uORB](../middleware/uorb.md) 发布/订阅机制传输消息。文件句柄不是必需的，也不用于系统核心操作。主要使用两个 API：

* 根据 PX4 运行的系统，其后端可能是文件、网络或共享内存的发布/订阅系统。
* 全局设备注册表，可用于枚举设备和获取/设置其配置。这可以是简单的链表或映射到文件系统。

## 设备 ID

PX4 使用设备 ID 在系统内一致标识各个传感器。这些 ID 存储在配置参数中，用于匹配传感器校准值，并确定哪个传感器记录到哪个日志文件条目。

传感器顺序（例如 `/dev/mag0` 和备用 `/dev/mag1`）不决定优先级 - 优先级存储在发布的 uORB 主题中。

### 解码示例

对于系统中三个磁力计的情况，使用飞行日志 (.px4log) 转储参数。三个参数编码了传感器 ID，`MAG_PRIME` 用于标识哪个磁力计被选为主传感器。每个 MAGx_ID 是一个 24 位数字，手动解码时应左补零。


```
CAL_MAG0_ID = 73225.0
CAL_MAG1_ID = 66826.0
CAL_MAG2_ID = 263178.0
CAL_MAG_PRIME = 73225.0
```

这是通过 I2C 连接的外部 HMC5983，总线 1 地址 `0x1E`：它将在日志文件中显示为 `IMU.MagX`。

```
# 24 位二进制的设备 ID 73225:
00000001  00011110  00001 001

# 解码为:
HMC5883   0x1E    总线 1 I2C
```

这是通过 SPI 连接的内部 HMC5983，总线 1，从设备选择槽 5。它将在日志文件中显示为 `IMU1.MagX`。

```
# 24 位二进制的设备 ID 66826:
00000001  00000101  00001 010

# 解码为:
HMC5883   设备 5   总线 1 SPI
```

这是通过 SPI 连接的内部 MPU9250 磁力计，总线 1，从设备选择槽 4。它将在日志文件中显示为 `IMU2.MagX`。

```
# 24 位二进制的设备 ID 263178:
00000100  00000100  00001 010

# 解码为:
MPU9250   设备 4   总线 1 SPI
```

### 设备 ID 编码

设备 ID 是一个 24 位数字，格式如下。注意在解码示例中，前几个字段是最低有效位。

```C
struct DeviceStructure {
  enum DeviceBusType bus_type : 3;
  uint8_t bus: 5;    // 总线类型的实例
  uint8_t address;   // 总线上的地址（例如 I2C 地址）
  uint8_t devtype;   // 设备类特定的设备类型
};
```
`bus_type` 的解码方式：

```C
enum DeviceBusType {
  DeviceBusType_UNKNOWN = 0,
  DeviceBusType_I2C     = 1,
  DeviceBusType_SPI     = 2,
  DeviceBusType_UAVCAN  = 3,
};
```

`devtype` 的解码方式：

```C
#define DRV_MAG_DEVTYPE_HMC5883  0x01
#define DRV_MAG_DEVTYPE_LSM303D  0x02
#define DRV_MAG_DEVTYPE_ACCELSIM 0x03
#define DRV_MAG_DEVTYPE_MPU9250  0x04
#define DRV_ACC_DEVTYPE_LSM303D  0x11
#define DRV_ACC_DEVTYPE_BMA180   0x12
#define DRV_ACC_DEVTYPE_MPU6000  0x13
#define DRV_ACC_DEVTYPE_ACCELSIM 0x14
#define DRV_ACC_DEVTYPE_GYROSIM  0x15
#define DRV_ACC_DEVTYPE_MPU9250  0x16
#define DRV_GYR_DEVTYPE_MPU6000  0x21
#define DRV_GYR_DEVTYPE_L3GD20   0x22
#define DRV_GYR_DEVTYPE_GYROSIM  0x23
#define DRV_GYR_DEVTYPE_MPU9250  0x24
#define DRV_RNG_DEVTYPE_MB12XX   0x31
#define DRV_RNG_DEVTYPE_LL40LS   0x32
```

## 调试

有关通用调试主题，请参见：[Debugging/Logging](../debug/index.md)。

### 详细日志

驱动（和其他模块）默认输出最小详细的日志字符串（例如，对于 `PX4_DEBUG`、`PX4_WARN`、`PX4_ERR` 等）。

日志详细程度通过构建时使用的 `RELEASE_BUILD`（默认）、`DEBUG_BUILD`（详细）或 `TRACE_BUILD`（极度详细）宏定义。

通过驱动 `px4_add_module` 函数中的 `COMPILE_FLAGS` 在 **CMakeLists.txt** 中更改日志级别。以下代码片段展示了为单个模块或驱动启用 DEBUG_BUILD 级别调试所需的更改。

```
px4_add_module(
	MODULE templates__module
	MAIN module
```
```
	COMPILE_FLAGS
		-DDEBUG_BUILD
```
```
	SRCS
		module.cpp
	DEPENDS
		modules__uORB
	)
```

:::tip
还可以通过在 .cpp 文件顶部添加 `#define DEBUG_BUILD`（在任何包含之前）来按文件启用详细日志。
:::