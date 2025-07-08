# LightWare Lidar (SF1X/SF02/LW20/SF45)

LightWare 开发了一系列轻量级通用激光高度计("Lidar")，适用于安装在无人机上。
这些设备适用于地形跟随、精确悬停（例如摄影）、监管高度限制警告、防撞感应等应用。

<img src="../../assets/hardware/sensors/lidar_lightware/sf11c_120_m.jpg" width="350px" alt="LightWare SF11/C Lidar"/>![LightWare SF45 旋转 Lidar](../../assets/hardware/sensors/lidar_lightware/sf45.png)

## 支持的型号

以下型号受 PX4 支持，可通过 I2C 或串口总线连接（下表显示各型号可用的总线类型）。

| 型号                                                      | 量程 (米) | 总线               | 描述                                                                                |
| ---------------------------------------------------------- | --------- | ----------------- | ------------------------------------------------------------------------------------------ |
| [SF11/C](https://lightwarelidar.com/products/sf11-c-100-m) | 100       | 串口或 I2C 总线   |
| [LW20/C](https://lightware.co.za/products/lw20-c-100-m)    | 100       | I2C 总线          | 防水（IP67）带舵机用于感知与避让应用                                                    |
| [SF45/B](../sensor/sf45_rotating_lidar.md)                 | 50        | 串口              | 旋转 Lidar（用于 [防撞](../computer_vision/collision_prevention.md)） |

::: details 已停产

以下型号受 PX4 支持但已停产。

| 型号                                                                                              | 量程 | 总线           |                                                                 |
| -------------------------------------------------------------------------------------------------- | ----- | ------------- | --------------------------------------------------------------- |
| [SF02](http://documents.lightware.co.za/SF02%20-%20Laser%20Rangefinder%20Manual%20-%20Rev%208.pdf) | 50    | 串口          |                                                                 |
| [SF10/A](http://documents.lightware.co.za/SF10%20-%20Laser%20Altimeter%20Manual%20-%20Rev%206.pdf) | 25    | 串口或 I2C    |                                                                 |
| [SF10/B](http://documents.lightware.co.za/SF10%20-%20Laser%20Altimeter%20Manual%20-%20Rev%206.pdf) | 50    | 串口或 I2C    |                                                                 |
| SF10/C                                                                                             | 100m  | 串口或 I2C    |                                                                 |
| LW20/B                                                                                             | 50    | I2C 总线      | 防水（IP67）带舵机用于感知与避让应用                                                  |

:::

## I2C 配置

请参考上表确认哪些型号可连接至 I2C 接口。

### Lidar 配置 (SF11/C)

SF11/C 硬件默认不启用 Pixhawk I2C 兼容模式。
要启用支持，需下载 [LightWare Studio](https://lightwarelidar.com/pages/lightware-studio) 并前往 **Parameters > Communication** 勾选 **I2C 兼容模式（Pixhawk）**。

![LightWare SF11/C Lidar-I2C 配置](../../assets/hardware/sensors/lidar_lightware/lightware_studio_i2c_config.jpg)

其他受支持的 Lightware 测距仪不需要此步骤。

### 硬件

将 Lidar 连接到飞控的 I2C 接口（此处以 [Pixhawk 1](../flight_controller/mro_pixhawk.md) 为例）。

![SF1XX LIDAR 到 I2C 连接](../../assets/hardware/sensors/lidar_lightware/sf1xx_i2c.jpg)

::: info
部分旧版型号无法与 PX4 兼容。
具体来说，它们的 I2C 地址可能被错误配置为 `0x55`，这与 `rgbled` 模块冲突。
在 Linux 系统中可通过 [i2cdetect](https://linux.die.net/man/8/i2cdetect) 检测地址。
如果 I2C 地址为 `0x66`，则传感器可与 PX4 兼容。
:::

### 参数配置 {#i2c_parameter_setup}

设置 [SENS_EN_SF1XX](../advanced_config/parameter_reference.md#SENS_EN_SF1XX) 参数以匹配测距仪型号，然后重启。

垂直起降（VTOL）机体可选择设置 [SF1XX_MODE](../advanced_config/parameter_reference.md#SF1XX_MODE) 为 `2: 在 VTOL 快速前飞时禁用`。

## 串口配置 {#serial_hardware_setup}

::: tip
[SF45/B](../sensor/sf45_rotating_lidar.md) 的配置请参见链接文档。
:::

### 硬件

Lidar 可连接至任意未使用的 _串口_ (UART)，例如：TELEM2、TELEM3、GPS2 等。

<!-- 很好能展示串口配置！ -->

### 参数配置 {#serial_parameter_setup}

使用 [SENS_SF0X_CFG](../advanced_config/parameter_reference.md#SENS_SF0X_CFG) 配置 Lidar 所在的串口。
无需设置波特率，因为驱动程序会自动配置。

::: info
如果在 _QGroundControl_ 中找不到配置参数，可能需要 [将驱动添加到固件](../peripherals/serial_configuration.md#parameter_not_in_firmware)。
:::

然后设置 [SENS_EN_SF0X](../advanced_config/parameter_reference.md#SENS_EN_SF0X) 参数以匹配测距仪型号并重启。

垂直起降（VTOL）机体可选择设置 [SF1XX_MODE](../advanced_config/parameter_reference.md#SF1XX_MODE) 为 `2: 在 VTOL 快速前飞时禁用`。

## 更多信息

- [模块参考: 距离传感器（驱动）: lightware_laser_i2c](../modules/modules_driver_distance_sensor.md#lightware-laser-i2c)
- [模块参考: 距离传感器（驱动）: lightware_laser_serial](../modules/modules_driver_distance_sensor.md#lightware-laser-serial)