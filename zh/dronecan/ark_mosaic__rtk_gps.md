# ARK MOSAIC-X5 RTK GPS

[ARK MOSAIC-X5 RTK GPS](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-mosaic-x5-rtk-gps) 是一款美国制造的 [DroneCAN](index.md) 模块，集成了 Septentrio Mosaic-X5 RTK GPS、磁力计、气压计、IMU、蜂鸣器和安全开关。

![ARK MOSAIC-X5 RTK GPS](../../assets/hardware/gps/ark/ark_mosaic_rtk_gps.jpg)

## 购买渠道

可通过以下链接购买该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-mosaic-x5-gps/) (美国)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_MosaicX5_GPS)
- 传感器
  - [Septentrio Mosaic-X5 GPS](https://www.septentrio.com/en/products/gnss-receivers/gnss-receiver-modules/mosaic-x5)
    - 三频段 L1/L2/L5
    - [AIM+ 抗干扰保护](https://www.septentrio.com/en/learn-more/advanced-positioning-technology/aim-anti-jamming-protection)
    - 100 Hz 更新频率
  - ST IIS2MDC 磁力计
  - [Bosch BMP390 气压计](https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/pressure-sensors-bmp390.html)
  - [Invensense ICM-42688-P 6轴IMU](https://invensense.tdk.com/products/motion-tracking/6-axis/icm-42688-p/)
- STM32F412VGH6 MCU
- 接口
  - 两个 Pixhawk 标准 CAN 接口（4针 JST-GH，5V 输入）
  - Pixhawk 标准 "Basic GPS Port"（6针 JST-GH，支持 `USART3` 和 I2C2 连接外部传感器如空速计或距离传感器）
  - Pixhawk 标准调试接口（6针 JST-SH）
  - USB-C 接口（5V 输入，USB 2.0 连接 Mosaic-X5）
  - Micro-SD 插槽用于 Mosaic-X5 日志记录
  - Mosaic "UART 2" 接口（5针 JST-GH，含 TX、RX、TIMEPULSE、GP1、GND）
- 供电需求
  - 5V
  - 平均 260mA
  - 峰值 340mA
- LED 指示灯
  - 安全 LED
  - GPS 定位状态
  - RTK 状态
  - RGB 系统状态 LED
- 其他信息
  - 包含 4针 Pixhawk 标准 CAN 线缆
  - 三频段（L1/L2/L5）螺旋 GPS 天线
  - 美国制造
  - 支持 DroneCAN 固件更新

## 参见

- [ARK MOSAIC-X5 RTK GPS 文档](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-mosaic-x5-rtk-gps) (ARK 官方文档)