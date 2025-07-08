# Pixhawk 3 Pro（已停产）

:::warning
PX4 不制造此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系 [制造商](https://store-drotek.com/)。
:::

Pixhawk<sup>&reg;</sup> 3 Pro 基于 FMUv4 硬件设计（Pixracer），并进行了一些升级和新增功能。  
该板由 [Drotek<sup>&reg;</sup>](https://drotek.com) 和 PX4 共同设计。

![Pixhawk 3 Pro 主图](../../assets/hardware/hardware-pixhawk3_pro.jpg)

::: info
主要的硬件文档在此：https://drotek.gitbook.io/pixhawk-3-pro/hardware
:::

:::tip
此自动驾驶仪由 PX4 维护和测试团队 [支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 快速摘要

- 微控制器：**STM32F469**；闪存大小为 **2MiB**，RAM 大小为 **384KiB**  
- **ICM-20608-G** 陀螺仪/加速度计  
- **MPU-9250** 陀螺仪/加速度计/磁强计  
- **LIS3MDL** 指南针  
- 通过两个 SPI 总线（一个高速率和一个低噪声总线）连接传感器  
- 两个 I2C 总线  
- 两个 CAN 总线  
- 从两个电源模块读取电压/电池数据  
- FrSky<sup>&reg;</sup> Inverter  
- 8 个主 + 6 个辅助 PWM 输出（独立 IO 芯片，PX4IO）  
- microSD（记录）  
- S.BUS / Spektrum / SUMD / PPM 输入  
- JST GH 用户友好型连接器：与 Pixracer 相同的连接器和引脚定义  

## 购买渠道

从 [Drotek 商店](https://store.drotek.com/)（欧盟）：

- [Pixhawk 3 Pro（套装）](https://store.drotek.com/autopilots/844-pixhawk-3-pro-pack.html)
- [Pixhawk 3 Pro](https://store.drotek.com/autopilots/821-pixhawk-pro-autopilot-8944595120557.html)

从 [readymaderc](https://www.readymaderc.com)（美国）：

- [Pixhawk 3 Pro](https://www.readymaderc.com/products/details/pixhawk-3-pro-flight-controller)

## 固件编译

:::tip
大多数用户无需编译此固件！  
当连接适当硬件时，_QGroundControl_ 会自动预编译并安装固件。
:::

要为该目标 [编译 PX4](../dev_setup/building_px4.md)：

```
make px4_fmu-v4pro_default
```

## 调试端口

板上有 FMU 和 IO 调试端口，如下图所示。

![调试端口](../../assets/flight_controller/pixhawk3pro/pixhawk3_pro_debug_ports.jpg)

引脚定义和连接器符合 [Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini) 接口标准，详见 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)（JST SM06B 连接器）。

| 引脚     | 信号           | 电压  |
| ------- | -------------- | ----- |
| 1 (红)  | VCC TARGET SHIFT | +3.3V |
| 2 (黑)  | CONSOLE TX (OUT) | +3.3V |
| 3 (黑)  | CONSOLE RX (IN)  | +3.3V |
| 4 (黑)  | SWDIO          | +3.3V |
| 5 (黑)  | SWCLK          | +3.3V |
| 6 (黑)  | GND            | GND   |

关于此端口接线和使用的信息，请参阅：

- [SWD 调试端口](../debug/swd_debug.md)
- [PX4 系统控制台](../debug/system_console.md#pixhawk_debug_port)（注意，FMU 控制台映射到 UART7）。

## 串口映射

| UART   | 设备       | 端口                  |
| ------ | ---------- | --------------------- |
| UART1  | /dev/ttyS0 | WiFi                  |
| USART2 | /dev/ttyS1 | TELEM1（流量控制）    |
| USART3 | /dev/ttyS2 | TELEM2（流量控制）    |
| UART4  |            |
| UART7  | CONSOLE    |
| UART8  | SERIAL4    |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->