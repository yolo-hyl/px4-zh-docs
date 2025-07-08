# CubePilot Cube Orange 飞控

:::warning  
PX4 不生产此（或任何）自动驾驶仪。  
有关硬件支持或合规性问题，请联系[制造商](https://cubepilot.org/#/home)。  
:::  

[Cube Orange](https://www.cubepilot.com/#/cube/features)飞控是一款灵活的自动驾驶仪，主要面向商业系统制造商设计。  

![Cube Orange](../../assets/flight_controller/cube/orange/cube_orange_hero.jpg)  

该控制器设计为与专用载板配合使用，以减少线缆连接、提高可靠性并简化组装。例如，商业巡检机体的载板可能包含伴飞计算机的接口，而竞速机体的载板则可能集成框架专用的电调（ESC）。  

ADS-B 载板包含 uAvionix 定制的 1090MHz ADSB-In 接收器。这可提供 Cube 范围内商用有人飞机的姿态和位置信息。该功能在默认 PX4 固件中已自动配置并启用。  

Cube 在两个 IMU 上采用了减震隔离设计，第三个 IMU 则作为参考/备用固定安装。  

:::tip  
制造商提供的[Cube 文档](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview)包含详细信息，包括关于[Cube 颜色差异](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview#differences-between-cube-colours)的概述。  
:::

## 核心功能

- 32位 STM32H753VI（32位 [ARM Cortex-M7](https://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M7)，400 MHz，闪存2MB，RAM 1MB）
- 32位 STM32F103 安全保护协处理器
- 14路PWM/舵机输出（8路支持安全保护和手动接管，6路辅助输出，支持高功率设备）
- 丰富的外设连接选项（UART、I2C、CAN）
- 集成备份系统，支持飞行中恢复和手动接管功能，配备专用处理器和独立电源（固定翼使用）
- 备份系统集成混合控制功能，提供一致的自动驾驶仪和手动接管混合模式（固定翼使用）
- 冗余电源输入和自动故障转移
- 外部安全开关
- 多色LED主视觉指示器
- 高功率、多音调压电音频指示器
- microSD卡用于长时间高速数据记录

<a id="stores"></a>## 购买渠道

- [经销商列表](https://www.cubepilot.com/#/reseller/list)

## 组装

[Cube Wiring Quickstart](../assembly/quick_start_cube.md)

## 规格参数

- **处理器:**
  - STM32H753VI (32位 [ARM Cortex M7](https://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M7))
  - 400 MHz
  - 1 MB RAM
  - 2 MB Flash $fully accessible$
- **安全备用协处理器:** <!-- inconsistent info on failsafe processor: 32 bit STM32F103 failsafe co-processor http://www.proficnc.com/all-products/191-pixhawk2-suite.html -->
  - STM32F103 (32位 _ARM Cortex-M3_)
  - 24 MHz
  - 8 KB SRAM
- **传感器:** (全部通过SPI连接)
  - **加速度计:** (3) ICM20948, ICM20649, ICM20602
  - **陀螺仪:** (3) ICM20948, ICM20649, ICM20602
  - **磁力计:** (1) ICM20948
  - **气压计:** (2) MS5611
- **工作条件:**
  - **工作温度:** -10C 至 55C
  - **防护等级/防水性能:** 非防水
  - **舵机供电电压:** 3.3V / 5V
  - **USB接口输入:**
    - 电压: 4V - 5.7V
    - 额定电流: 250 mA
  - **电源:**
    - 输入电压: 4.1V - 5.7V
    - 额定输入电流: 2.5A
    - 额定输入/输出功率: 14W
- **尺寸:**
  - **立方体:** 38.25mm x 38.25mm x 22.3mm
  - **基板:** 94.5mm x 44.3mm x 17.3mm
- **接口**
  - IO端口: 14路PWM舵机输出 (8路来自IO，6路来自FMU)
  - 5x UART (串口)，1个支持高功率，2个带硬件流控制
  - 2x CAN (1个带内置3.3V收发器，1个在扩展接口)
  - **遥控输入:**
    - Spektrum DSM / DSM2 / DSM-X® 卫星接收器兼容输入
    - Futaba S.BUS® 兼容输入和输出
    - PPM-SUM 信号输入
  - RSSI (PWM或电压) 输入
  - I2C
  - SPI
  - 3.3v ADC 输入
  - 内部microUSB接口和外部microUSB扩展接口## 端口

### 顶部（GPS、TELEM等）

![Cube端口 - 顶部（GPS、TELEM等）及主/辅](../../assets/flight_controller/cube/cube_ports_top_main.jpg)

## 引脚定义

#### TELEM1, TELEM2 接口

| 引脚    | 信号       | 电压    |
| ------- | ---------- | ------- |
| 1 (红色) | VCC        | +5V     |
| 2 (黑色) | TX (输出)  | +3.3V   |
| 3 (黑色) | RX (输入)  | +3.3V   |
| 4 (黑色) | CTS (输入) | +3.3V   |
| 5 (黑色) | RTS (输出) | +3.3V   |
| 6 (黑色) | GND        | GND     |

#### GPS1 接口

| 引脚    | 信号           | 电压    |
| ------- | -------------- | ------- |
| 1 (红色) | VCC            | +5V     |
| 2 (黑色) | TX (输出)      | +3.3V   |
| 3 (黑色) | RX (输入)      | +3.3V   |
| 4 (黑色) | SCL I2C2       | +3.3V   |
| 5 (黑色) | SDA I2C2       | +3.3V   |
| 6 (黑色) | 安全按钮       | GND     |
| 7 (黑色) | 按钮LED        | GND     |
| 8 (黑色) | GND            | GND     |

<!-- check is i2c2 -->

#### GPS2 接口

| 引脚    | 信号       | 电压    |
| ------- | ---------- | ------- |
| 1 (红色) | VCC        | +5V     |
| 2 (黑色) | TX (输出)  | +3.3V   |
| 3 (黑色) | RX (输入)  | +3.3V   |
| 4 (黑色) | SCL I2C1   | +3.3V   |
| 5 (黑色) | SDA I2C1   | +3.3V   |
| 6 (黑色) | GND        | GND     |

#### ADC 接口

| 引脚    | 信号      | 电压         |
| ------- | --------- | ------------ |
| 1 (红色) | VCC       | +5V          |
| 2 (黑色) | ADC 输入  | 最高+6.6V    |
| 3 (黑色) | GND       | GND          |

#### I2C 接口

| 引脚    | 信号      | 电压            |
| ------- | --------- | --------------- |
| 1 (红色) | VCC       | +5V             |
| 2 (黑色) | SCL       | +3.3V（上拉）   |
| 3 (黑色) | SDA       | +3.3V（上拉）   |
| 4 (黑色) | GND       | GND             |

#### CAN1 & CAN2 接口

| 引脚    | 信号   | 电压  |
| ------- | ------ | ----- |
| 1 (红色) | VCC    | +5V   |
| 2 (黑色) | CAN_H  | +12V  |
| 3 (黑色) | CAN_L  | +12V  |
| 4 (黑色) | GND    | GND   |

#### POWER1 & POWER2 接口

| 引脚    | 信号             | 电压    |
| ------- | ---------------- | ------- |
| 1 (红色) | VCC              | +5V     |
| 2 (红色) | VCC              | +5V     |
| 3 (黑色) | 电流检测         | +3.3V   |
| 4 (黑色) | 电压检测         | +3.3V   |
| 5 (黑色) | GND              | GND     |
| 6 (黑色) | GND              | GND     |

#### USB 接口

| 引脚    | 信号           | 电压              |
| ------- | -------------- | ----------------- |
| 1 (红色) | VCC            | +5V               |
| 2 (黑色) | OTG_DP1        | +3.3V             |
| 3 (黑色) | OTG_DM1        | +3.3V             |
| 4 (黑色) | GND            | GND               |
| 5 (黑色) | BUZZER         | 电池电压          |
| 6 (黑色) | FMU Error LED  |                   |

#### SPKT 接口

| 引脚    | 信号   | 电压    |
| ------- | ------ | ------- |
| 1 (黑色) | 输入   |         |
| 2 (黑色) | GND    | GND     |
| 3 (红色) | 输出   | +3.3V   |

#### TELEM1, TELEM2 接口

| 引脚    | 信号       | 电压            |
| ------- | ---------- | --------------- |
| 1 (红色) | VCC        | +5V             |
| 2 (黑色) | TX (输出)  | +3.3V 至 5V     |
| 3 (黑色) | RX (输入)  | +3.3V 至 5V     |
| 4 (黑色) | CTS (输出) | +3.3V 至 5V     |
| 5 (黑色) | RTS (输入) | +3.3V 至 5V     |
| 6 (黑色) | GND        | GND             |## 串口映射

| UART   | 设备       | 端口                  |
| ------ | ---------- | --------------------- |
| USART2 | /dev/ttyS0 | TELEM1 (流控制)       |
| USART3 | /dev/ttyS1 | TELEM2 (流控制)       |
| UART4  | /dev/ttyS2 | GPS1                  |
| USART6 | /dev/ttyS3 | PX4IO                 |
| UART7  | /dev/ttyS4 | CONSOLE/ADSB-IN       |
| UART8  | /dev/ttyS5 | GPS2                  |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/boards/cubepilot/cubeorange/default.px4board -->
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/boards/cubepilot/cubeorange/nuttx-config/nsh/defconfig#L188-L197 -->

### USB/SD卡端口

![Cube USB/SDCard Ports](../../assets/flight_controller/cube/cube_ports_usb_sdcard.jpg)

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接合适的硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要[构建 PX4](../dev_setup/building_px4.md) 此目标，请打开终端并输入：

```
make cubepilot_cubeorange
```

## 原理图

板级原理图和其他文档可在此处找到：[The Cube Project](https://github.com/proficnc/The-Cube)。

## 其他信息/文档

- [Cube接线快速入门](../assembly/quick_start_cube.md)
- Cube Docs（制造商）：
  - [Cube模块概述](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview)
  - [Cube用户手册](https://docs.cubepilot.org/user-guides/autopilot/the-cube-user-manual)
  - [Mini载体板](https://docs.cubepilot.org/user-guides/carrier-boards/mini-carrier-board)