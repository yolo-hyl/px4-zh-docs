# Holybro Durandal

:::warning
PX4不生产此（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

_Durandal_<sup>&reg;</sup> 是Holybro飞控家族的最新升级产品。
该产品由Holybro设计和开发。

![Durandal](../../assets/flight_controller/durandal/durandal_hero.jpg)

主要特性包括：

- 传感器集成温度控制系统。
- 搭载480MHz主频的STM32H7微控制器，2 MB Flash存储器和1 MB RAM。
- 采用具有更高温度稳定性的新型传感器。
- 内置振动隔离系统。
- 双高性能低噪声IMU，专为苛刻的稳定化应用设计。

下文提供了关键特性摘要、[组装](../assembly/quick_start_durandal.md)和[购买](#purchase)链接。

::: info
此飞控模块是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 快速概览

#### 技术规格

- 主FMU处理器: STM32H743
  - 32位Arm® Cortex®-M7, 480MHz, 2MB存储, 1MB RAM
- IO处理器: STM32F100
  - 32位Arm® Cortex®-M3, 24MHz, 8KB SRAM
- 板载传感器
  - 加速度计/陀螺仪: ICM-20689
  - 加速度计/陀螺仪: BMI088 or ICM20602
  - 磁力计: IST8310
  - 气压计: MS5611
- GPS: u-blox Neo-M8N GPS/GLONASS接收器；集成磁力计IST8310

#### 接口

- 8-13个PWM舵机输出（8个来自IO，5个来自FMU）
- FMU上6个专用PWM/捕获输入
- 专用R/C输入用于Spektrum/DSM
- 专用R/C输入用于CPPM和S.Bus
- 专用S.Bus舵机输出和模拟/PWM RSSI输入
- 5个通用串行端口
  - 3个带有完整流控制
  - 1个带有单独1.5A电流限制
- 3个I2C端口
- 4个SPI总线
  - 1个内部高速SPI传感器总线，4个芯片选择，6个DRDY
  - 1个内部低噪声SPI总线专用于XXX
  - 气压计带有2个芯片选择，无DRDY
  - 1个内部SPI总线专用于FRAM
  - 支持位于传感器模块上的温度控制
  - 1个外部SPI总线
- 最多2个CAN总线用于双CAN
  - 每个CAN总线有独立的静默控制或ESC RX-MUX控制
- 电池电压/电流的模拟输入
- 2个额外的模拟输入

#### 电气数据

- 电源模块输出: 4.9~5.5V
- 最大输入电压: 6V
- 最大电流检测: 120A
- USB电源输入: 4.75~5.25V
- 舵机供电输入: 0~36V

#### 机械数据

- 尺寸: 80x45x20.5mm
- 重量: 68.8g

#### 其他特性

- 工作温度: ~40~85°C
- 存储温度: -40~85°C
- CE
- FCC
- RoHS合规（无铅）

更多信息请参见: [Durandal技术规格书](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Durandal_technical_data_sheet_90f8875d-8035-4632-a936-a0d178062077.pdf).

<a id="purchase"></a>## 购买渠道

从 [Holybro](https://holybro.com/collections/autopilot-flight-controllers/products/durandal) 订购。

<a id="connections"></a>## 连接

端口/连接位置如图所示（及下方的[引脚图部分](#pinouts)）。

### 顶部

![Durandal - 顶部引脚图 (Schematic)](../../assets/flight_controller/durandal/durandal_pinouts_top.jpg)

### 前面

![Durandal - 前部引脚图 (Schematic)](../../assets/flight_controller/durandal/durandal_pinouts_front.jpg)

### 背面

![Durandal - 背部引脚图 (Schematic)](../../assets/flight_controller/durandal/durandal_pinouts_back.jpg)

### 右侧

![Durandal - 右侧引脚图 (Schematic)](../../assets/flight_controller/durandal/durandal_pinouts_right.jpg)

### 左侧

![Durandal - 左侧引脚图 (Schematic)](../../assets/flight_controller/durandal/durandal_pinouts_left.jpg)

## 尺寸

所有尺寸均以毫米为单位。

![Durandal尺寸图](../../assets/flight_controller/durandal/durandal_dimensions.jpg)

<!--## 电压规格

*Pixhawk 4* 可通过三个电源输入实现电源三重冗余。三个电源轨分别为：**POWER1**、**POWER2** 和 **USB**。

::: info
输出电源轨 **FMU PWM OUT** 和 **I/O PWM OUT**（0V 到 36V）不会为飞控板供电（也不会被其供电）。
必须为 **POWER1**、**POWER2** 或 **USB** 中的至少一个供电，否则电路板将处于断电状态。
:::

**正常操作最大额定值**

在此条件下系统将按以下优先级使用所有电源供电：
1. **POWER1** 和 **POWER2** 输入（4.9V 到 5.5V）
1. **USB** 输入（4.75V 到 5.25V）
-->

<!--
**绝对最大额定值**

在此条件下系统将不会消耗任何电力（无法运行），但仍可保持完好：
1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 到 5.7V，0V 到 10V 不会受损）
1. **USB** 输入（工作范围 4.1V 到 5.7V，0V 到 6V 不会受损）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 到 42V 不会受损）

-->## 组装/设置

[Durandal Wiring Quick Start](../assembly/quick_start_durandal.md) 提供了如何组装必需/重要的外设（如 GPS、电源管理板等）的说明。

## 构建固件

:::提示
大多数用户不需要构建此固件！
当连接适当的硬件时，_QGroundControl_ 会预构建并自动安装。
:::

要为此目标[构建 PX4](../dev_setup/building_px4.md)：

```
make holybro_durandal-v1_default
```

## 串口映射

| UART    | 设备       | 端口            |
| ------- | ---------- | --------------- |
| USART1  | /dev/ttyS0 | GPS1            |
| USART2  | /dev/ttyS1 | TELEM1          |
| USART3  | /dev/ttyS2 | TELEM2          |
| UART4   | /dev/ttyS3 | TELEM4/GPS2     |
| USART6  | /dev/ttyS4 | TELEM3          |
| UART7   | /dev/ttyS5 | 调试控制台       |
| UART8   | /dev/ttyS6 | PX4IO           |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->

<a id="debug_port"></a>## 调试端口

[PX4 System Console](../debug/system_console.md) 和 [SWD interface](../debug/swd_debug.md) 运行在 _Debug Port_ 上。

引脚和连接器符合 [Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini) 接口规范，该规范定义于 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 中。

对于接线和调试信息，请参见上述链接。

::: info
I/O板未暴露调试端口。
:::

## 外设

- [数字空速传感器](https://store-drotek.com/793-digital-differential-airspeed-sensor-kit-.html)
- [遥测无线电模块](../telemetry/index.md)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台/机型

任何可以通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼、固定翼飞机、地面车或船只。

所有支持的配置均可在[Airframes Reference](../airframes/airframe_reference.md)中查看。

## 引脚分配

_Durandal_ 的引脚分配如下所示。  
这些内容也可以从 [这里](https://holybro.com/collections/autopilot-flight-controllers/products/Durandal-Pinouts) 下载。

### 顶面引脚分配

![Durandal - 顶面引脚分配 (原理图)](../../assets/flight_controller/durandal/durandal_pinouts_top.jpg)

### 正面引脚分配

![Durandal - 正面引脚分配 (原理图)](../../assets/flight_controller/durandal/durandal_pinouts_front.jpg)

#### SUBS Out 接口

| 引脚       | 信号           | 电压   |
| ---------- | -------------- | ------ |
| 1 (红色)   | -              | -      |
| 2 (黄色)   | SBUS_OUT/RSSI_IN | +3.3V |
| 3 (黑色)   | GND            | GND    |

#### DSM RC 接口

| 引脚       | 信号   | 电压   |
| ---------- | ------ | ------ |
| 1 (红色)   | VDD_3V3 | +3.3V |
| 2 (黄色)   | DSM_IN  | +3.3V |
| 3 (黑色)   | GND     | GND    |

#### I2C A 接口

| 引脚      | 信号 | 电压   |
| -------- | ---- | ------ |
| 1 (红色)  | VCC  | +5V   |
| 2 (黑色)  | SCL4 | +3.3V |
| 3 (黑色)  | SDA4 | +3.3V |
| 4 (黑色)  | GND  | GND    |

#### CAN1 接口

| 引脚      | 信号 | 电压   |
| -------- | ---- | ------ |
| 1 (红色)  | VCC  | +5V   |
| 2 (黑色)  | CAN H | +3.3V |
| 3 (黑色)  | CAN L | +3.3V |
| 4 (黑色)  | GND  | GND    |

<a id="gps"></a>

#### GPS 接口

| 引脚       | 信号            | 电压   |
| ---------- | --------------- | ------ |
| 1 (红色)   | VCC             | +5V   |
| 2 (黑色)   | TX (输出)       | +3.3V |
| 3 (黑色)   | RX (输入)       | +3.3V |
| 4 (黑色)   | SCL1            | +3.3V |
| 5 (黑色)   | SDA1            | +3.3V |
| 6 (黑色)   | SAFETY_SWITCH   | +3.3V |
| 7 (黑色)   | SAFETY_SWITCH_LED | +3.3V |
| 8 (黑色)   | VDD_3V3         | +3.3V |
| 9 (黑色)   | BUZZER          | +5V   |
| 10 (黑色)  | GND             | GND    |

<a id="telem4_i2cb"></a>

#### TELEM4 I2CB 接口

| 引脚       | 信号     | 电压   |
| ---------- | -------- | ------ |
| 1 (红色)   | VCC      | +5V   |
| 2 (黑色)   | TX (输出) | +3.3V |
| 3 (黑色)   | RX (输入) | -     |
| 4 (黑色)   | SCL2     | -     |
| 5 (黑色)   | SDA2     | -     |
| 6 (黑色)   | GND      | GND    |

<a id="telem4_i2cb"></a>

#### TELEM4 I2CB 接口

| 引脚       | 信号     | 电压   |
| ---------- | -------- | ------ |
| 1 (红色)   | VCC      | +5V   |
| 2 (黑色)   | TX (输出) | +3.3V |
| 3 (黑色)   | RX (输入) | -     |
| 4 (黑色)   | SCL2     | -     |
| 5 (黑色)   | SDA2     | -     |
| 6 (黑色)   | GND      | GND    |

（注：原文中 TELEM4 I2CB 接口部分存在重复锚点 ID，此处保留原结构并修正为唯一锚点）

<a id="telem4_i2cb_new"></a>

#### TELEM4 I2CB 接口

| 引脚       | 信号     | 电压   |
| ---------- | -------- | ------ |
| 1 (红色)   | VCC      | +5V   |
| 2 (黑色)   | TX (输出) | +3.3V |
| 3 (黑色)   | RX (输入) | -     |
| 4 (黑色)   | SCL2     | -     |
| 5 (黑色)   | SDA2     | -     |
| 6 (黑色)   | GND      | GND    |

<a id="warn_sensor"></a>

:::warning
\++ 连接到引脚8和9的传感器不得发送超过指示电压的信号。
:::

### 左侧引脚分配

![Durandal - 左侧引脚分配 (原理图)](../../assets/flight_controller/durandal/durandal_pinouts_left.jpg)

<a id="debug_port"></a>

#### DEBUG 接口

| 引脚       | 信号 | 电压   |
| ---------- | ---- | ------ |
| 1 (红色)   | VT   | +3.3V |
| 2 (黑色)   | TX   | +3.3V |
| 3 (黑色)   | RX   | +3.3V |
| 4 (黑色)   | SWDIO | +3.3V |
| 5 (黑色)   | SWCLK | +3.3V |
| 6 (黑色)   | GND  | GND    |

#### SPI 接口

| 引脚       | 信号 | 电压   |
| ---------- | ---- | ------ |
| 1 (红色)   | VCC  | +5V   |
| 2 (黑色)   | SCK  | +3.3V |
| 3 (黑色)   | MISO | +3.3V |
| 4 (黑色)   | MOSI | +3.3V |
| 5 (黑色)   | CS1  | +3.3V |
| 6 (黑色)   | CS2  | +3.3V |
| 7 (黑色)   | GND  | GND    |

#### USB 接口

| 引脚       | 信号 | 电压   |
| ---------- | ---- | ------ |
| 1 (红色)   | VBUS | +5V   |
| 2 (黑色)   | DM   | +3.3V |
| 3 (黑色)   | DP   | +3.3V |
| 4 (黑色)   | GND  | GND    |## 进一步信息

- [Durandal 接线快速入门](../assembly/quick_start_durandal.md)
- [Durandal 技术数据表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Durandal_technical_data_sheet_90f8875d-8035-4632-a936-a0d178062077.pdf)
- [Durandal 引脚分配](https://holybro.com/collections/autopilot-flight-controllers/products/Durandal-Pinouts) (Holybro)