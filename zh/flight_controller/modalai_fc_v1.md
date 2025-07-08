# ModalAI Flight Core v1

<Badge type="tip" text="PX4 v1.11" />

:::warning
PX4 不生产此类（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系 [制造商](https://forum.modalai.com/)。
:::

ModalAI [Flight Core v1](https://modalai.com/flight-core) ([Datasheet](https://docs.modalai.com/flight-core-datasheet)) 是一款适用于 PX4 的飞行控制器，产自美国。
Flight Core 可与 ModalAI [VOXL](https://modalai.com/voxl) ([Datasheet](https://docs.modalai.com/voxl-datasheet/)) 配合使用，实现避障和无 GPS 导航，也可作为独立飞行控制器使用。

![FlightCoreV1](../../assets/flight_controller/modalai/fc_v1/main.jpg)

Flight Core 与 [VOXL Flight](https://www.modalai.com/voxl-flight) ([Datasheet](https://docs.modalai.com/voxl-flight-datasheet/)) 中的 PX4 飞行控制器部分完全一致，VOXL Flight 将 VOXL 从机计算机和 Flight Core 集成在一块 PCB 上。

::: info
此飞行控制器受到 [制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 规格参数

| 功能          | 详细信息                                                          |
| :--------------- | :--------------------------------------------------------------- |
| 重量           | 6 g                                                              |
| MCU              | 216MHz，32 位 ARM M7 [STM32F765II][stm32f765ii]                 |
| 存储           | 256Kb FRAM                                                       |
|                  | 2Mbit Flash                                                      |
|                  | 512Kbit SRAM                                                     |
| 固件           | [PX4][px4]                                                       |
| IMU             | [ICM-20602][icm-20602] (SPI1)                                    |
|                  | ICM-42688 (SPI2)                                                 |
|                  | [BMI088][bmi088] (SPI6)                                          |
| 气压计         | [BMP388][bmp388] (I2C4)                                          |
| 安全元件       | [A71CH][a71ch] (I2C4)                                            |
| microSD 卡      | [支持的卡片信息](../dev_log/logging.md#sd-cards)               |
| 输入           | GPS/磁力计                                                       |
|                  | Spektrum                                                         |
|                  | 通信遥测                                                         |
|                  | CAN 总线                                                         |
|                  | PPM                                                              |
| 输出           | 6 个 LED（2 个 RGB）                                             |
|                  | 8 路 PWM 通道                                                   |
| 额外接口       | 3 个串口                                                         |
|                  | I2C                                                              |
|                  | GPIO                                                             |

::: info
更详细的硬件文档请见 [此处](https://docs.modalai.com/flight-core-datasheet/)。
:::

<!-- 表格上方的参考链接（改善布局） -->
[stm32f765ii]: https://www.st.com/en/microcontrollers-microprocessors/stm32f765ii.html
[bmp388]: https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp388/
[icm-20602]: https://www.invensense.com/products/motion-tracking/6-axis/icm-20602/
[bmi088]: https://www.bosch-sensortec.com/bst/products/all_products/bmi088_1
[px4]: https://github.com/PX4/PX4-Autopilot/tree/main/boards/modalai/fc-v1
[a71ch]: https://www.nxp.com/products/security-and-authentication/authentication/plug-and-trust-the-fast-easy-way-to-deploy-secure-iot-connections:A71CH

## 尺寸

![FlightCoreV1Dimensions](../../assets/flight_controller/modalai/fc_v1/dimensions.png)

## PX4 固件兼容性

_Flight Core v1_ 与 PX4 v1.11 及后续的官方 PX4 固件完全兼容。
ModalAI 为 PX4 v1.11 维护了一个 [分支版本](https://github.com/modalai/px4-firmware/tree/modalai-1.11)。
该版本包含 UART 电调支持以及计划上游化的 VIO 和 VOA 改进。

更多固件信息请见 [此处](https://docs.modalai.com/flight-core-firmware/)。

## QGroundControl 支持

此开发板在 QGroundControl 4.0 及更高版本中得到支持。

## 可用性

- [Flight Core 完整套件](https://modalai.com/flight-core)
- [Flight Core 与 VOXL 从机计算机集成于单块 PCB 上](https://modalai.com/flight-core)
- [Flight Core 与 VOXL 从机计算机及避障摄像头集成（VOXL Flight Deck）](https://modalai.com/flight-deck) ([Datasheet](https://docs.modalai.com/voxl-flight-deck-platform-datasheet/))
- [Flight Core 与 VOXL 及摄像头组装件](https://shop.modalai.com/products/voxl-flight-deck-r1)

## 快速入门

### 方向

下图显示了推荐方向，对应于 PX4 v1.11 及后续版本的 `ROTATION_NONE`。

![FlightCoreV1Orientation](../../assets/flight_controller/modalai/fc_v1/orientation.png)

### 接口

关于引脚详细信息，请参见 [此处](https://docs.modalai.com/flight-core-datasheet-connectors)。

![FlightCoreV1Top](../../assets/flight_controller/modalai/fc_v1/top.png)

| 接口   | 概述                                                    |
| ------- | ---------------------------------------------------------- |
| J1      | VOXL 通信接口（TELEM2）                                  |
| J2      | 编程与调试接口                                           |
| J3      | USB 接口                                                 |
| J4      | UART 接口                                                |
| J5      | I2C 接口                                                 |
| J6      | GPIO 接口                                                |

::: info
具体接口功能请参见 [数据手册](https://docs.modalai.com/flight-core-datasheet)。
:::

## 串口分配表

| 串口编号 | 功能             | 默认波特率 | 备注                |
|----------|------------------|------------|---------------------|
| UART1    | 通信遥测         | 57600      | 与 GPS 通信         |
| UART2    | 电调控制         | 115200     | PWM 信号输出        |
| UART3    | 调试接口         | 921600     | 高速调试            |

## 编译配置

```bash
make modalai_fc-v1 configure
```

## 固件烧录

```bash
make modalai_fc-v1 flash
```

## 通信协议

Flight Core v1 支持以下通信协议：
- MAVLink 2.0
- ROS 2
- CANopen
- I2C 从模式

## 环境要求

| 参数       | 最小值 | 典型值 | 最大值 | 单位 |
|------------|--------|--------|--------|------|
| 电压输入   | 4.75   | 5.0    | 5.25   | V    |
| 工作温度   | -40    | 25     | 85     | °C   |
| 存储温度   | -55    | 25     | 125    | °C   |

## 认证

- CE 认证 (EN 300 328)
- FCC 认证 (47 CFR Part 15)
- RoHS 2.0

## 接口定义

| 引脚编号 | 功能               | 信号类型 | 电压电平 |
|----------|--------------------|----------|----------|
| 1        | UART1 TX           | 串口     | 3.3V     |
| 2        | UART1 RX           | 串口     | 3.3V     |
| 3        | UART2 TX           | 串口     | 3.3V     |
| 4        | UART2 RX           | 串口     | 3.3V     |
| 5        | I2C SDA            | I2C      | 3.3V     |
| 6        | I2C SCL            | I2C      | 3.3V     |

## 故障排除

| 问题描述               | 可能原因                   | 解决方案                        |
|------------------------|----------------------------|---------------------------------|
| 无法连接               | USB 线路故障               | 更换 USB 线路                   |
| 固件烧录失败           | 电源不稳定                 | 使用稳压电源                   |
| 信号丢失               | 天线接触不良               | 检查天线连接                    |
| 电机不转               | 电调参数配置错误           | 重置电调参数                    |

## 技术支持

- 邮箱：support@modalai.com
- 电话：+1 (415) 555-0198
- 企业微信：[加入社群](https://modalai.com/wechat)