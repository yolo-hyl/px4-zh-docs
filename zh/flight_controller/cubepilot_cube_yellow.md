# CubePilot Cube Yellow 飞行控制器

:::warning
PX4 不生产此类（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://cubepilot.org/#/home)。
:::

Cube Yellow 飞行控制器是一款灵活的自动驾驶仪，主要面向商业系统制造商。

![Cube Yellow](../../assets/flight_controller/cube/yellow/cube_yellow_hero.jpg)

该控制器设计用于与领域特定的载板配合使用，以减少接线、提高可靠性和简化装配。例如，商用检查机体的载板可能包含连接计算机伴飞器的接口，而竞速机体的载板则可能包含框架的电调。

Cube 在两个IMU上集成了振动隔离功能，第三个固定IMU作为参考/备份。

:::tip
制造商的[Cube Docs](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview)包含详细信息，包括关于[Cube颜色差异](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview#differences-between-cube-colours)的概述。
:::

## 核心特性

- 32位 STM32F777VI（32位 [ARM Cortex M7](https://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M7) 处理器，400 MHz，2MB Flash，512 KB RAM）
- 32位 STM32F103 安全处理器 <!-- 需确认 -->
- 14路 PWM/舵机输出（8路带安全机制和手动覆盖功能，6路辅助，支持高功率）
- 丰富的外设连接选项（UART、I2C、CAN）
- 集成备份系统，支持飞行中恢复和手动覆盖，配备专用处理器和独立电源（固定翼使用）
- 备份系统集成混合控制，提供一致的自动驾驶仪和手动覆盖混合模式（固定翼使用）
- 冗余电源输入和自动故障转移
- 外部安全开关
- 多色LED主视觉指示器
- 多频调谐压电音频指示器
- microSD卡用于长时间高速率日志记录

<a id="stores"></a>

## 购买渠道

- [经销商列表](https://www.cubepilot.com/#/reseller/list)

## 组装指南

[Cube 接线快速入门](../assembly/quick_start_cube.md)

## 规格参数

- **处理器:**
  - STM32F777VI（32位 [ARM Cortex M7](https://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M7)）
  - 400 MHz
  - 512 KB RAM
  - 2 MB Flash
- **安全协处理器:** <!-- 安全处理器信息不一致：32位 STM32F103 安全协处理器 http://www.proficnc.com/all-products/191-pixhawk2-suite.html -->
  - STM32F100（32位 _ARM Cortex-M3_）
  - 24 MHz
  - 8 KB SRAM
- **传感器:**（全部通过SPI连接）
  - **加速度计:**（3）ICM20948, ICM20649, ICM20602
  - **陀螺仪:**（3）ICM20948, ICM20649, ICM20602
  - **磁力计:**（1）ICM20948
  - **气压计:**（2）MS5611
- **工作条件:**
  - **工作温度:** -10C 至 55C
  - **IP等级/防水:** 非防水
  - **舵机供电电压:** 3.3V / 5V
  - **USB接口输入:**
    - 电压: 4V - 5.7V
    - 标称电流: 250 mA
  - **电源:**
    - 输入电压: 4.1V - 5.7V
    - 标称输入电流: 2.5A
    - 标称输入/输出功率: 14W
- **尺寸:**
  - **Cube:** 38.25mm x 38.25mm x 22.3mm
  - **载板:** 94.5mm x 44.3mm x 17.3mm
- **接口**
  - IO端口: 14路PWM舵机输出（8路来自IO，6路来自FMU）
  - 5x UART（串口），其中1路高功率，2路带硬件流控制
  - 2x CAN（1路内置3.3V收发器，1路在扩展接口）
  - **遥控输入:**
    - Spektrum DSM / DSM2 / DSM-X® 卫星兼容输入
    - Futaba S.BUS® 兼容输入和输出
    - PPM-SUM 信号输入
  - RSSI（PWM或电压）输入
  - I2C
  - SPI
  - 3.3v ADC 输入
  - 内部和外部 microUSB 接口

## 引脚和原理图

电路板原理图和其他文档可在以下地址找到：[The Cube Project](https://github.com/proficnc/The-Cube)。

## 端口

### 顶部（GPS、TELEM等）

![Cube 端口 - 顶部（GPS、TELEM等）和主/AUX](../../assets/flight_controller/cube/cube_ports_top_main.jpg)

## 串口映射

| UART   | 设备     | 端口                  |
| ------ | -------- | --------------------- |
| USART2 | /dev/ttyS0 | TELEM1（带流控制） |
| USART3 | /dev/ttyS1 | TELEM2（带流控制） |
| UART4  | /dev/ttyS2 | GPS1                |
| USART6 | /dev/ttyS3 | PX4IO               |
| UART7  | /dev/ttyS4 | CONSOLE/ADSB-IN     |
| UART8  | /dev/ttyS5 | GPS2                |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/boards/hex/cube-orange/default.px4board -->
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/boards/hex/cube-orange/nuttx-config/nsh/defconfig#L194-L200 -->

### 调试端口

![Cube 调试端口](../../assets/flight_controller/cube/cube_ports_debug.jpg)

### USB/SD卡端口

![Cube USB/SD卡端口](../../assets/flight_controller/cube/cube_ports_usb_sdcard.jpg)

## 固件构建

:::tip
大多数用户无需构建此固件！
:::

## 已知问题

- CAN端口的丝印标签错误（CAN1和CAN2的实际位置与标注相反）

## 进一步资料

- [CubePilot官方文档](https://docs.cubepilot.org/)
- [开发者论坛](https://discuss.cubepilot.org/)