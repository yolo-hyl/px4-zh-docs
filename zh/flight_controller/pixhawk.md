# 3DR Pixhawk 1 飞行控制器（已停产）

:::warning
此飞行控制器已[停产](../flight_controller/autopilot_experimental.md)且不再商业销售。
您可以使用[mRo Pixhawk](../flight_controller/mro_pixhawk.md)作为即插即用替代品。
:::

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关支持或合规性问题，请联系制造商。
:::

_3DR Pixhawk<sup>&reg;</sup> 1_ 自动驾驶仪是基于 [Pixhawk-project](https://pixhawk.org/) **FMUv2** 开源硬件设计的通用飞行控制器（其功能集成了 PX4FMU + PX4IO）。  
它在 [NuttX](https://nuttx.apache.org/) 操作系统上运行 PX4。

![Pixhawk 图像](../../assets/hardware/hardware-pixhawk.png)

与 PX4 配合使用的组装/设置说明请参考：[Pixhawk 接线快速入门](../assembly/quick_start_pixhawk.md)

## 关键特性

- 主系统芯片: [STM32F427](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN1789)  
  - CPU: 180 MHz ARM® Cortex® M4 带单精度FPU  
  - RAM: 256 KB SRAM (L1)  
- 安全系统芯片: STM32F100  
  - CPU: 24 MHz ARM Cortex M3  
  - RAM: 8 KB SRAM  
- Wi-Fi: ESP8266 外部  
- GPS: u-blox® 7/8 (Hobbyking®) / u-blox 6 (3D Robotics)  
- 光流: [PX4 Flow单元](../sensor/px4flow.md)  
- 冗余电源输入及自动故障切换  
- 外部安全开关  
- 多色LED主视觉指示器  
- 高功率、多音调压电音频指示器  
- microSD卡用于长时间高频率记录  

连接性  

- 1x I2C  
- 1x CAN (2x 可选)  
- 1x ADC  
- 4x UART (2x 带流控制)  
- 1x 控制台  
- 8x PWM 带手动覆盖  
- 6x PWM / GPIO / PWM 输入  
- S.BUS / PPM / Spektrum 输入  
- S.BUS 输出# 购买渠道

该电路板最初由 3DR&reg; 原厂制造，是 PX4&reg; 的原始标准微控制器平台。虽然 3DR 已不再生产该板，但可以使用 [mRo Pixhawk](../flight_controller/mro_pixhawk.md) 作为即插即用的替代品。

购买 mRo Pixhawk 的渠道如下：

- [Bare Bones](https://store.mrobotics.io/Genuine-PixHawk-1-Barebones-p/mro-pixhawk1-bb-mr.htm) - 仅包含电路板（可作为 3DR Pixhawk 的替代品）
- [mRo Pixhawk 2.4.6 Essential Kit](https://store.mrobotics.io/Genuine-PixHawk-Flight-Controller-p/mro-pixhawk1-minkit-mr.htm) - 包含除遥测无线电外的所有组件
- [mRo Pixhawk 2.4.6 Cool Kit! (Limited edition)](https://store.mrobotics.io/product-p/mro-pixhawk1-fullkit-mr.htm) - 包含所有必要组件（包括遥测无线电）## 规格

### 处理器

- 32位 STM32F427 [Cortex-M4F](http://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M4) 核心（带FPU）
- 168 MHz
- 256 KB RAM
- 2 MB Flash
- 32位 STM32F103 安全保护协处理器

### 传感器

- ST Micro L3GD20H 16位陀螺仪
- ST Micro LSM303D 14位加速度计/磁力计
- Invensense MPU 6000 三轴加速度计/陀螺仪
- MEAS MS5611 气压计

### 接口

- 5x UART（串口），1个支持高功率，2x带硬件流控制
- 2x CAN（1个带内部3.3V收发器，1个在扩展接口）
- Spektrum DSM / DSM2 / DSM-X® 卫星兼容输入
- Futaba S.BUS® 兼容输入和输出
- PPM合成信号输入
- RSSI（PWM或电压）输入
- I2C
- SPI
- 3.3V 和 6.6V ADC 输入
- 内部microUSB端口和外部microUSB扩展端口

<lite-youtube videoid="gCCC5A-Bvv4" title="PX4 Pixhawk (3DR) Multicolor Led in action"/>

### 电源系统与保护

- 带自动切换的理想二极管控制器
- 伺服电源轨支持高功率（最大10V）和大电流（10A+）
- 所有外设输出均有过流保护，所有输入均防静电保护## 电压规格

Pixhawk 若提供三个电源输入则可实现电源三重冗余。三条电源通道分别为：电源模块输入、舵机电源输入、USB输入。

### 正常操作最大额定值

在以下条件下系统将按照此优先级顺序使用所有电源供电：

- 电源模块输入（4.8V至5.4V）  
- 舵机电源输入（4.8V至5.4V） **手动覆盖模式可支持至10V，但若无电源模块输入且电压超过5.7V时自动驾驶部分将断电**  
- USB电源输入（4.8V至5.4V）

### 绝对最大额定值

在以下条件下系统将不再消耗任何电力（无法运行），但硬件仍可保持完好：

- 电源模块输入（4.1V至5.7V，0V至20V无损）  
- 舵机电源输入（4.1V至5.7V，0V至20V）  
- USB电源输入（4.1V至5.7V，0V至6V）## 原理图

[FMUv2 + IOv2 原理图](https://raw.githubusercontent.com/PX4/Hardware/master/FMUv2/PX4FMUv2.4.5.pdf) -- 原理图和布局

::: info
作为一个CC-BY-SA 3.0许可的开源硬件设计，所有原理图和设计文件均可在[此处](https://github.com/PX4/Hardware)获取。
:::

## 连接

Pixhawk端口如下所示。  
这些端口使用Hirose DF13连接器（早于Pixhawk连接器标准中定义的JST-GH连接器）。

:::warning
许多3DR Pixhawk克隆版使用Molex picoblade连接器，而非DF13连接器。  
它们的引脚为矩形而非方形，不能假设其兼容性。  
:::

![Pixhawk Connectors](../../assets/flight_controller/pixhawk1/pixhawk_connectors.png)

:::tip
`RC IN`端口仅用于接收遥控器接收机，并为此用途提供足够的供电。  
**切勿**将任何舵机、电源或电池连接到该端口或连接到该接收机。  
:::

## 引脚分配

#### TELEM1，TELEM2端口

| 引脚    | 信号      | 电压    |
| ------- | --------- | ------- |
| 1 (红色) | VCC       | +5V     |
| 2 (黑色) | TX (OUT)  | +3.3V   |
| 3 (黑色) | RX (IN)   | +3.3V   |
| 4 (黑色) | CTS (IN)  | +3.3V   |
| 5 (黑色) | RTS (OUT) | +3.3V   |
| 6 (黑色) | GND       | GND     |

#### GPS端口

| 引脚    | 信号    | 电压    |
| ------- | ------- | ------- |
| 1 (红色) | VCC     | +5V     |
| 2 (黑色) | TX (OUT)| +3.3V   |
| 3 (黑色) | RX (IN) | +3.3V   |
| 4 (黑色) | CAN2 TX | +3.3V   |
| 5 (黑色) | CAN2 RX | +3.3V   |
| 6 (黑色) | GND     | GND     |

#### SERIAL 4/5端口

由于空间限制，两个端口位于同一个连接器上。

| 引脚    | 信号     | 电压    |
| ------- | -------- | ------- |
| 1 (红色) | VCC      | +5V     |
| 2 (黑色) | TX (#4)  | +3.3V   |
| 3 (黑色) | RX (#4)  | +3.3V   |
| 4 (黑色) | TX (#5)  | +3.3V   |
| 5 (黑色) | RX (#5)  | +3.3V   |
| 6 (黑色) | GND      | GND     |

#### ADC 6.6V

| 引脚    | 信号   | 电压        |
| ------- | ------ | ----------- |
| 1 (红色) | VCC    | +5V         |
| 2 (黑色) | ADC IN | 最高+6.6V   |
| 3 (黑色) | GND    | GND         |

#### ADC 3.3V

| 引脚    | 信号   | 电压        |
| ------- | ------ | ----------- |
| 1 (红色) | VCC    | +5V         |
| 2 (黑色) | ADC IN | 最高+3.3V   |
| 3 (黑色) | GND    | GND         |
| 4 (黑色) | ADC IN | 最高+3.3V   |
| 5 (黑色) | GND    | GND         |

#### I2C

| 引脚    | 信号       | 电压            |
| ------- | ---------- | --------------- |
| 1 (红色) | VCC        | +5V             |
| 2 (黑色) | SCL        | +3.3 (上拉电阻) |
| 3 (黑色) | SDA        | +3.3 (上拉电阻) |
| 4 (黑色) | GND        | GND             |

#### CAN

| 引脚    | 信号  | 电压  |
| ------- | ----- | ----- |
| 1 (红色) | VCC   | +5V   |
| 2 (黑色) | CAN_H | +12V  |
| 3 (黑色) | CAN_L | +12V  |
| 4 (黑色) | GND   | GND   |

#### SPI

| 引脚    | 信号           | 电压  |
| ------- | -------------- | ----- |
| 1 (红色) | VCC            | +5V   |
| 2 (黑色) | SPI_EXT_SCK    | +3.3V |
| 3 (黑色) | SPI_EXT_MISO   | +3.3V |
| 4 (黑色) | SPI_EXT_MOSI   | +3.3V |
| 5 (黑色) | !SPI_EXT_NSS   | +3.3V |
| 6 (黑色) | !GPIO_EXT      | +3.3V |
| 7 (黑色) | GND            | GND   |

#### POWER

| 引脚    | 信号    | 电压  |
| ------- | ------- | ----- |
| 1 (红色) | VCC     | +5V   |
| 2 (黑色) | VCC     | +5V   |
| 3 (黑色) | CURRENT | +3.3V |
| 4 (黑色) | VOLTAGE | +3.3V |
| 5 (黑色) | GND     | GND   |
| 6 (黑色) | GND     | GND   |

#### SWITCH

| 引脚    | 信号             | 电压  |
| ------- | ---------------- | ----- |
| 1 (红色) | VCC              | +3.3V |
| 2 (黑色) | !IO_LED_SAFETY   | GND   |
| 3 (黑色) | SAFETY           | GND   |## 串口映射

| UART   | 设备       | 端口                  |
| ------ | ---------- | --------------------- |
| UART1  | /dev/ttyS0 | IO调试              |
| USART2 | /dev/ttyS1 | TELEM1（流量控制）   |
| USART3 | /dev/ttyS2 | TELEM2（流量控制）   |
| UART4  |            |
| UART7  | CONSOLE    |
| UART8  | SERIAL4    |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->## 调试端口

### 控制台端口

[PX4系统控制台](../debug/system_console.md) 运行在标记为 [SERIAL4/5](#serial-4-5-port) 的端口上。

:::tip
连接控制台的便捷方式是使用 [Dronecode 探针](https://kb.zubax.com/display/MAINKB/Dronecode+Probe+documentation)，因为它带有可兼容多种Pixhawk设备的连接器。只需将 [Dronecode 探针](https://kb.zubax.com/display/MAINKB/Dronecode+Probe+documentation) 上的6针DF13 1:1线缆连接到Pixhawk的 `SERIAL4/5` 端口。

![Dronecode 探针](../../assets/flight_controller/pixhawk1/dronecode_probe.jpg)
:::

接线为标准串口接线，设计用于连接 [3.3V FTDI](https://www.digikey.com/en/products/detail/TTL-232R-3V3/768-1015-ND/1836393) 线缆（5V兼容）。

| 3DR Pixhawk 1 |           | FTDI |
| ------------- | --------- | ---- |
| 1             | +5V（红色） |      | 无连接 |
| 2             | S4 Tx     |      | 无连接 |
| 3             | S4 Rx     |      | 无连接 |
| 4             | S5 Tx     | 5    | FTDI RX（黄色） |
| 5             | S5 Rx     | 4    | FTDI TX（橙色） |
| 6             | GND       | 1    | FTDI GND（黑色） |

FTDI线缆与6针DF13 1:1连接器的接线方式如下图所示。

![控制台连接器](../../assets/flight_controller/pixhawk1/console_connector.jpg)

完整接线方式如下所示。

![控制台调试](../../assets/flight_controller/pixhawk1/console_debug.jpg)

::: info
有关如何_使用_控制台的更多信息，请参见：[系统控制台](../debug/system_console.md)。
:::

### SWD 端口

[SWD](../debug/swd_debug.md)（JTAG）端口隐藏在盖板下方（硬件调试时需要移除盖板）。FMU和IO有单独的端口，如下图所示。

![Pixhawk SWD](../../assets/flight_controller/pixhawk1/pixhawk_swd.jpg)

这些端口是ARM 10针JTAG连接器，可能需要焊接。端口的引脚分配如下所示（上方角落的方形标记指示1号引脚）。

![ARM 10针连接器引脚分配](../../assets/flight_controller/pixhawk1/arm_10pin_jtag_connector_pinout.jpg)

::: info
所有Pixhawk FMUv2板都具有类似的SWD端口。
:::

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接适当硬件时，它已预构建并由_QGroundControl_自动安装。
:::

要[构建 PX4](../dev_setup/building_px4.md)以针对此目标：

```
make px4_fmu-v2_default
```

## 部件 / 外壳

- **ARM MINI JTAG (J6)**: 1.27 mm 10位接头 (带屏蔽)，用于 Black Magic Probe: FCI 20021521-00010D4LF ([Distrelec](https://www.distrelec.ch/en/minitek-127-straight-male-pcb-header-surface-mount-rows-10-contacts-27mm-pitch-amphenol-fci-20021521-00010d4lf/p/14352308), [Digi-Key](https://www.digikey.com/en/products/detail/20021521-00010T1LF/609-4054-ND/2414951),) 或 Samtec FTSH-105-01-F-DV-K (未经测试) 或 Harwin M50-3600542 ([Digikey](https://www.digikey.com/en/products/detail/harwin-inc/M50-3600542/2264370) 或 [Mouser](http://ch.mouser.com/ProductDetail/Harwin/M50-3600542/?qs=%2fha2pyFadujTt%2fIEz8xdzrYzHAVUnbxh8Ki%252bwWYPNeEa09PYvTkIOQ%3d%3d))
  - JTAG 适配器选项 #1: [BlackMagic Probe](https://1bitsquared.com/products/black-magic-probe)。注意，可能不附带电缆（请向制造商确认）。  
    如果如此，您将需要 **Samtec FFSD-05-D-06.00-01-N** 电缆 ([Samtec sample service](https://www.samtec.com/products/ffsd-05-d-06.00-01-n) 或 [Digi-Key 链接: SAM8218-ND](http://www.digikey.com/product-search/en?x=0&y=0&lang=en&site=us&KeyWords=FFSD-05-D-06.00-01-N)) 或 [Tag Connect 软排线](http://www.tag-connect.com/CORTEXRIBBON10) 以及一根 Mini-USB 电缆。
  - JTAG 适配器选项 #2: [Digi-Key 链接: ST-LINK/V2](https://www.digikey.com/product-detail/en/stmicroelectronics/ST-LINK-V2/497-10484-ND) / [ST 用户手册](http://www.st.com/internet/com/TECHNICAL_RESOURCES/TECHNICAL_LITERATURE/USER_MANUAL/DM00026748.pdf)，需要一个 ARM Mini JTAG 到 20位的适配器: [Digi-Key 链接: 726-1193-ND](https://www.digikey.com/en/products/detail/texas-instruments/MDL-ADA2/1986451)
  - JTAG 适配器选项 #3: [SparkFun 链接: Olimex ARM-TINY](http://www.sparkfun.com/products/8278) 或其他 OpenOCD 兼容的 ARM Cortex JTAG 适配器，需要一个 ARM Mini JTAG 到 20位的适配器: [Digi-Key 链接: 726-1193-ND](https://www.digikey.com/en/products/detail/texas-instruments/MDL-ADA2/1986451)
- **USARTs**: Hirose DF13 6 位 ([Digi-Key 链接: DF13A-6P-1.25H(20)](https://www.digikey.com/products/en?keywords=H3371-ND))
  - 配套外壳: Hirose DF13 6 位外壳 ([Digi-Key 链接: Hirose DF13-6S-1.25C](https://www.digikey.com/products/en?keywords=H2182-ND))
- **I2C 和 CAN**: Hirose DF13 4 位 ([Digi-Key 链接: DF13A-4P-1.25H(20)](https://www.digikey.com/en/products/detail/hirose-electric-co-ltd/DF13A-4P-1-25H-20/530666) - 已停产)

## 支持的平台 / 机体结构

任何可以通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼、固定翼飞机、地面无人车或船只。