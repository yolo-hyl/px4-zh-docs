# CubePilot Cube Orange+ 飞控

:::warning
PX4 不生产此（或任何）飞控。
有关硬件支持或合规性问题，请联系[制造商](https://cubepilot.org/#/home)。
:::

[Cube Orange+](https://www.cubepilot.com/#/cube/features) 飞控是一款灵活的飞控，主要用于商业系统制造商。
Cube Orange+ 与 Cube Orange 类似，但搭载了更强大的双核处理器（STM32H757），且部分传感器组件不同。

![Cube Orange](../../assets/flight_controller/cube/orangeplus/cubepilot_cube_orangeplus_standard_set.jpg)

该控制器设计为与领域专用载板配合使用，以减少布线、提高可靠性并简化组装。
例如，商业巡检机体的载板可能包含伴飞计算机连接，而竞速机体载板则可能集成框架电调。

ADS-B 载板包含来自 uAvionix 的定制 1090MHz ADSB-In 接收器。
这能提供 Cube 侦测范围内的商用有人飞机姿态和位置信息。
此功能在默认 PX4 固件中已自动配置并启用。

Cube 在两个 IMU 上实现了减震隔离，第三个 IMU 作为参考/备份固定使用。

:::tip
制造商提供的 [Cube 文档](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview) 包含详细信息，包括 [Cube 颜色差异](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview#differences-between-cube-colours) 的概述。
:::

## 核心功能

- 32位 STM32H757ZI（32位 [ARM Cortex M7](https://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M7)，400 MHz，Flash 2MB，RAM 1MB）
- 32位 STM32F103 备用安全协处理器
- 14路PWM/舵机输出（8路支持备用安全和手动覆盖，6路辅助，兼容高功率）
- 丰富的外设连接选项（UART、I2C、CAN）
- 集成备份系统，支持飞行中恢复和手动覆盖功能，配备专用处理器及独立供电（固定翼使用）
- 备份系统集成混合控制功能，提供一致的自动驾驶仪和手动覆盖混合模式（固定翼使用）
- 冗余电源输入及自动切换
- 外部安全开关
- 多色LED主视觉指示器
- 高功率多音调压电音频指示器
- microSD卡支持长时间高速率日志记录

<a id="stores"></a>

## 哪里购买

- [经销商列表](https://www.cubepilot.com/#/reseller/list)

## 组装

[Cube Wiring Quickstart](../assembly/quick_start_cube.md)

## 规格

- **处理器:**  
  - STM32H757 (32位 [ARM Cortex M7](https://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M7))  
  - 400 MHz  
  - 1 MB RAM  
  - 2 MB Flash (完全可访问)  
- **备用协处理器:** <!-- 关于备用处理器的信息不一致: 32位 STM32F103 备用协处理器 http://www.proficnc.com/all-products/191-pixhawk2-suite.html -->  
  - STM32F103 (32位 _ARM Cortex-M3_)  
  - 24 MHz  
  - 8 KB SRAM  
- **传感器:** (全部通过SPI连接)  
  - **加速度计:** (3) ICM42688p, ICM20948, ICM20649  
  - **陀螺仪:** (3) ICM42688p, ICM20948, ICM20649  
  - **磁力计:** (1) ICM20948  
  - **气压计:** (2) MS5611  
- **工作条件:**  
  - **工作温度:** -10°C 至 55°C  
  - **IP等级/防水:** 非防水  
  - **舵机供电输入电压:** 3.3V / 5V  
  - **USB接口输入:**  
    - 电压: 4V - 5.7V  
    - 额定电流: 250 mA  
  - **电源:**  
    - 输入电压: 4.1V - 5.7V  
    - 额定输入电流: 2.5A  
    - 额定输入/输出功率: 14W  
- **尺寸:**  
  - **立方体:** 38.25mm x 38.25mm x 22.3mm  
  - **载板:** 94.5mm x 44.3mm x 17.3mm  
- **接口**  
  - IO端口: 14路PWM舵机输出 (8路来自IO，6路来自FMU)  
  - 5x UART (串口)，1路高功率支持，2路带硬件流控制  
  - 2x CAN (1路内置3.3V收发器，1路在扩展接口)  
  - **R/C输入:**  
    - Spektrum DSM / DSM2 / DSM-X® 卫星兼容输入  
    - Futaba S.BUS® 兼容输入和输出  
    - PPM-SUM 信号输入  
  - RSSI (PWM或电压) 输入  
  - I2C  
  - SPI  
  - 3.3V ADC 输入  
  - 内部microUSB端口和外部microUSB扩展端口
  
  ## 端口

### 顶部接口（GPS、TELEM等）

![Cube端口 - 顶部（GPS、TELEM等）和主/AUX端口](../../assets/flight_controller/cube/cube_ports_top_main.jpg)

## 引脚定义

#### TELEM1, TELEM2 接口

| 引脚     | 信号       | 电压   |
| ------- | ---------- | ----- |
| 1 (red) | 电源       | +5V   |
| 2 (blk) | 发送(输出) | +3.3V |
| 3 (blk) | 接收(输入) | +3.3V |
| 4 (blk) | CTS(输入)  | +3.3V |
| 5 (blk) | RTS(输出)  | +3.3V |
| 6 (blk) | 地         | GND   |

#### GPS1 接口

| 引脚     | 信号           | 电压   |
| ------- | -------------- | ----- |
| 1 (red) | 电源           | +5V   |
| 2 (blk) | 发送(输出)      | +3.3V |
| 3 (blk) | 接收(输入)      | +3.3V |
| 4 (blk) | SCL I2C2       | +3.3V |
| 5 (blk) | SDA I2C2       | +3.3V |
| 6 (blk) | 安全按钮       | GND   |
| 7 (blk) | 按钮LED        | GND   |
| 8 (blk) | 地             | GND   |

<!-- check is i2c2 -->

#### GPS2 接口

| 引脚     | 信号       | 电压   |
| ------- | ---------- | ----- |
| 1 (red) | 电源       | +5V   |
| 2 (blk) | 发送(输出) | +3.3V |
| 3 (blk) | 接收(输入) | +3.3V |
| 4 (blk) | SCL I2C1   | +3.3V |
| 5 (blk) | SDA I2C1   | +3.3V |
| 6 (blk) | 地         | GND   |

#### ADC

| 引脚     | 信号   | 电压         |
| ------- | ------ | ------------ |
| 1 (red) | 电源   | +5V          |
| 2 (blk) | ADC 输入 | 最高+6.6V    |
| 3 (blk) | 地     | GND          |

#### I2C

| 引脚     | 信号   | 电压           |
| ------- | ------ | -------------- |
| 1 (red) | 电源   | +5V            |
| 2 (blk) | SCL    | +3.3 (上拉电阻) |
| 3 (blk) | SDA    | +3.3 (上拉电阻) |
| 4 (blk) | 地     | GND            |

#### CAN1 & CAN2

| 引脚     | 信号   | 电压 |
| ------- | ------ | ---- |
| 1 (red) | 电源   | +5V  |
| 2 (blk) | CAN_H  | +12V |
| 3 (blk) | CAN_L  | +12V |
| 4 (blk) | 地     | GND  |

#### POWER1 & POWER2

| 引脚     | 信号          | 电压   |
| ------- | --------------- | ----- |
| 1 (red) | 电源           | +5V   |
| 2 (red) | 电源           | +5V   |
| 3 (blk) | 电流检测       | +3.3V |
| 4 (blk) | 电压检测       | +3.3V |
| 5 (blk) | 地             | GND   |
| 6 (blk) | 地             | GND   |

#### USB

| 引脚     | 信号         | 电压             |
| ------- | ------------ | ---------------- |
| 1 (red) | 电源         | +5V              |
| 2 (blk) | OTG_DP1      | +3.3V            |
| 3 (blk) | OTG_DM1      | +3.3V            |
| 4 (blk) | 地           | GND              |
| 5 (blk) | 蜂鸣器       | 电池电压         |
| 6 (blk) | FMU错误LED   |

#### SPKT

| 引脚     | 信号   | 电压   |
| ------- | ------ | ----- |
| 1 (blk) | 输入   |
| 2 (blk) | 地     | GND   |
| 3 (red) | 输出   | +3.3V |

#### TELEM1, TELEM2

| 引脚     | 信号       | 电压       |
| ------- | ---------- | ---------- |
| 1 (red) | 电源       | +5V        |
| 2 (blk) | 发送(输出) | +3.3V至5V  |
| 3 (blk) | 接收(输入) | +3.3V至5V  |
| 4 (blk) | CTS(输出)  | +3.3V至5V  |
| 5 (blk) | RTS(输入)  | +3.3V至5V  |
| 6 (blk) | 地         | GND        |

## 串口映射

| UART   | 设备       | 端口                     |
| ------ | ---------- | ------------------------ |
| USART2 | /dev/ttyS0 | TELEM1（流量控制）       |
| USART3 | /dev/ttyS1 | TELEM2（流量控制）       |
| UART4  | /dev/ttyS2 | GPS1                     |
| USART6 | /dev/ttyS3 | PX4IO                    |
| UART7  | /dev/ttyS4 | CONSOLE/ADSB-IN          |
| UART8  | /dev/ttyS5 | GPS2                     |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/boards/cubepilot/cubeorange/default.px4board -->
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/boards/cubepilot/cubeorange/nuttx-config/nsh/defconfig#L188-L197 -->

### USB/SD卡端口

![Cube USB/SD卡端口](../../assets/flight_controller/cube/cube_ports_usb_sdcard.jpg)

## 构建固件

:::warning
Orange+ 的固件将从 PX4 v1.14 版本开始提供。
:::

要 [构建 PX4](../dev_setup/building_px4.md) 的目标固件，请打开终端并输入：

```
make cubepilot_cubeorangeplus
```

::: info
Cube Orange+ 和 Cube Orange 的固件不兼容。
由于 STM32H757 的电源特性，需要 [更新 NuttX](https://github.com/PX4/NuttX/pull/214)，因此需要新的开发板配置、引导程序、构建目标等。
:::

## 电路图

板载电路图和其他文档可以在这里找到：[The Cube Project](https://github.com/proficnc/The-Cube)。

## 更多信息/文档

- [Cube 接线快速入门](../assembly/quick_start_cube.md)
- Cube 文档（制造商）：
  - [Cube 模块概述](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview)
  - [Cube 用户手册](https://docs.cubepilot.org/user-guides/autopilot/the-cube-user-manual)
  - [迷你载板](https://docs.cubepilot.org/user-guides/carrier-boards/mini-carrier-board)