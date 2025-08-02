

# 六边形黑飞行控制器

:::warning
PX4 不制造此（或任何）自动驾驶仪。
联系[制造商](https://cubepilot.org/#/home)获取硬件支持或合规性问题。
:::

:::tip
[Cube Orange](cubepilot_cube_orange.md) 是该产品的后续产品。
我们建议考虑基于行业标准的产品，例如[Pixhawk 标准](autopilot_pixhawk_standard.md)。
此飞行控制器未遵循标准且使用了专利连接器。
:::

[六边形黑](http://www.proficnc.com/61-system-kits2)飞行控制器（先前称为 Pixhawk 2.1）是一款面向商用系统制造商的灵活自动驾驶仪。
它基于[Pixhawk-project](https://pixhawk.org/) **FMUv3** 开源硬件设计，并在[NuttX](https://nuttx.apache.org/) 操作系统上运行 PX4。

![Cube Black](../../assets/flight_controller/cube/cube_black_hero.png)

该控制器设计为与领域特定的载板配合使用，以减少布线、提高可靠性和简化组装。
例如，商用检测机体的载板可能包含配套计算机的连接，而竞速机体的载板可能包含框架集成的电调。

Cube 在两个 IMU 上提供了减震隔离，第三个固定 IMU 作为参考/备用。

::: info
制造商的[Cube Docs](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview) 包含详细信息，包括[不同 Cube 颜色的差异](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview#differences-between-cube-colours)概览。
:::

:::tip
此自动驾驶仪受到 PX4 维护和测试团队的[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 主要特性

- 32位STM32F427 [Cortex-M4F](http://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M4)<sup>&reg;</sup> 核心带浮点运算单元
- 168 MHz / 252 MIPS
- 256 KB RAM
- 2 MB 闪存（完全可访问）
- 32位STM32F103 故障保护协处理器
- 14路PWM/舵机输出（8路带故障保护和手动覆盖功能，6路辅助输出，兼容高压）
- 丰富的外设连接选项（UART、I2C、CAN）
- 集成备份系统，支持飞行中恢复和手动覆盖功能，配备专用处理器和独立电源（固定翼应用）
- 备份系统集成混合控制功能，提供一致的自动驾驶仪和手动覆盖混合模式（固定翼应用）
- 冗余电源输入和自动故障切换
- 外部安全开关
- 多色LED主视觉指示器
- 高功率多音调压电音频指示器
- microSD卡用于长时间高速率数据记录

<a id="stores"></a>

## 购买渠道

[Cube Black](http://www.proficnc.com/61-system-kits) (ProfiCNC)

## 组装

[Cube接线快速入门](../assembly/quick_start_cube.md)

## 规格

### 处理器

- 32位STM32F427 [Cortex-M4](http://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M4) 内核带FPU
- 168 MHz / 252 MIPS
- 256 KB RAM
- 2 MB Flash（完全可访问）
- 32位STM32F103故障安全协处理器

### 传感器

- TBA

### 接口

- 5个UART（串口），其中1个为高功率型，2个带硬件流控制  
- 2个CAN（1个内部3.3V收发器，1个在扩展接口上）  
- Spektrum DSM / DSM2 / DSM-X® Satellite兼容输入  
- Futaba S.BUS®兼容输入和输出  
- PPM总和信号输入  
- RSSI（PWM或电压）输入  
- I2C  
- SPI  
- 3.3v ADC输入  
- 内部microUSB端口和外部microUSB端口扩展

### 电源系统与保护

- 理想二极管控制器支持自动故障切换
- 伺服电源轨支持高功率（最高10V）和大电流（10A+）
- 所有外设输出均具有过流保护，所有输入均具备静电放电（ESD）保护

### 电压规格

Pixhawk 可在提供三个电源时实现电源的三重冗余。三个电源轨分别为：电源模块输入、舵机电源轨输入、USB 输入。

#### 正常运行最大额定值

在这些条件下，系统将按以下顺序使用所有电源供电：

- 电源模块输入（4.8V 至 5.4V）
- 舵机电源输入（4.8V 至 5.4V）**最高可达10V用于手动接管，但若未连接电源模块输入，则高于5.7V时自动驾驶部分将断电**
- USB电源输入（4.8V 至 5.4V）

#### 绝对最大额定值

在此条件下系统将不会消耗任何电力（不会运行），但硬件将保持完好。

- 电源模块输入（4.1V 至 5.7V，0V 至 20V 不损坏）
- 舵机电源输入（4.1V 至 5.7V，0V 至 20V）
- USB 电源输入（4.1V 至 5.7V，0V 至 6V）

## 引脚分配和电路图

电路板电路图和其他文档可在此处找到: [The Cube Project](https://github.com/proficnc/The-Cube)

## 端口

### 顶部（GPS、TELEM等）

![Cube端口 - 顶部（GPS、TELEM等）和Main/AUX](../../assets/flight_controller/cube/cube_ports_top_main.jpg)

<a id="serial_ports"></a>

### 串口映射

| UART   | 设备     | 端口                  |
| ------ | -------- | --------------------- |
| USART1 | /dev/ttyS0 | <!-- IO debug? -->    |
| USART2 | /dev/ttyS1 | TELEM1 (流控制)       |
| USART3 | /dev/ttyS2 | TELEM2 (流控制)       |
| UART4  | /dev/ttyS3 | GPS1                 |
| USART6 | /dev/ttyS4 | PX4IO                |
| UART7  | /dev/ttyS5 | CONSOLE              |
| UART8  | /dev/ttyS6 | <!-- unknown -->      |

<!-- 注意：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->
<!-- 原文曾写过 "**TEL4:** /dev/ttyS6 (ttyS4 UART): **注意** `TEL4` 在 Cube 上标记为 `GPS2`。-->

### 调试端口

![Cube 调试端口](../../assets/flight_controller/cube/cube_ports_debug.jpg)

### USB/SD卡端口

![Cube USB/SD卡端口](../../assets/flight_controller/cube/cube_ports_usb_sdcard.jpg)

## 构建固件

:::tip
大多数用户无需手动构建此固件！
当连接合适的硬件时，_QGroundControl_ 会自动完成预构建固件的安装。
:::

要[构建PX4](../dev_setup/building_px4.md)以适配本目标平台：

```
make px4_fmu-v3_default
```

## 问题

Cube Black 上的 CAN1 和 CAN2 丝印被颠倒了（CAN1 实际标记为 CAN2，反之亦然）。

## 更多信息/文档

- [Cube 接线快速入门](../assembly/quick_start_cube.md)
- Cube 文档 (制造商)：
  - [Cube 模块概述](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview)
  - [Cube 用户手册](https://docs.cubepilot.org/user-guides/autopilot/the-cube-user-manual)
  - [Mini 载板](https2://docs.cubepilot.org/user-guides/carrier-boards/mini-carrier-board)