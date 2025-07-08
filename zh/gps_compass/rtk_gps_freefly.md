# Freefly Systems RTK GPS

[Freefly Systems RTK GPS模块](https://store.freeflysystems.com/products/rtk-gps-ground-station) 是来自 Freefly Systems 的多频段 [RTK GPS模块](../gps_compass/rtk_gps.md)，提供高度可靠的导航功能。
模块可作为飞行器上的移动站（rover）或连接到计算机的基准站（base station）使用。

主要功能包括：

- 多频段（L1/L2）接收器（u-blox ZED-F9P）
- 同时接收所有4个GNSS（GPS、Galileo、GLONASS、BeiDou）
- 内置磁力计（IST8310）、气压计（BMP388）、RGB LED、安全开关和安全指示灯

::: info
该模块可与 PX4 v1.9 或更高版本配合使用（对 u-blox ZED-F9P 的支持从 PX4 v1.9 开始添加）。
:::

![FreeFly GPS模块](../../assets/hardware/gps/freefly_gps_module.jpg)

## 购买渠道

- [Freefly官方商店](https://store.freeflysystems.com/products/rtk-gps-ground-station)

## 套件内容

RTK GPS套件包含：

- 2个带天线的GPS模块
- 3米USB-C转USB-A数据线
- 基准站模块磁性快装接口（1/4-20螺纹适配三脚架）
- 用于安装到 Freefly AltaX 的螺丝

## 配置

通过 QGroundControl 在 PX4 上设置和使用 RTK 主要是即插即用（更多信息请参考 [RTK GPS](../gps_compass/rtk_gps.md)）。

对于飞行器，应将参数 [SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD) 设置为 115200 8N1，以确保 PX4 配置正确的波特率。

## 接线与连接

Freefly RTK GPS 配备 8 针 JST-GH 接口，可插入 PixHawk 自主飞行器。
作为基准站使用时，模块配备 USB-C 接口

### 引脚分配

Freefly GPS 的引脚分配如下。
对于 Hex Cube 和 PixRacer 等部分自主飞行器，仅需一根 8 针直插式 JST-GH 数据线即可。

| 引脚 | Freefly GPS |
| --- | ----------- |
| 1   | VCC_5V      |
| 2   | GPS_RX      |
| 3   | GPS_TX      |
| 4   | I2C_SCL     |
| 5   | I2C_SDA     |
| 6   | BUTTON      |
| 7   | BUTTON_LED  |
| 8   | GND         |

## 规格参数

- u-blox ZED-F9P GPS接收器
  - 超级电容备用电源，实现快速（热启动）重启
  - 接收器上的电磁干扰（EMI）屏蔽，提高抗干扰能力
- IST8310磁力计
- 安全开关和安全指示灯
- RGB LED状态指示灯
  - NCP5623CMUTBG I2C驱动器
- I2C总线上的BMP388气压计
- 外部有源天线（Maxtena M7HCT）
  - SMA接口
- STM32 MCU，用于未来基于CAN的通信
  - 通过USB接口进行固件更新
- 连接性：
  - USB-C
  - 双路USB切换至MCU和F9P
  - SMA有源天线接口（最大20mA）
  - 4针JST-GH CAN总线（符合dronecode标准）
  - 8针JST-GH UART/I2C
    - **电源：**
  - 输入源（二极管或）：
  - USB（5V）
  - CAN（4.7至25.2V）
  - （4.7至25.2V）
  - 功耗 <1W

## 更多信息

更多资料请访问 [Freefly的Wiki](https://freefly.gitbook.io/freefly-public/products/rtk-gps)