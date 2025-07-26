

# Holybro Durandal

:::warning
PX4 不生产此（或任何）自动驾驶仪。
如需硬件支持或合规问题，请联系[制造商](https://holybro.com/)
:::

_Durandal_<sup>&reg;</sup> 是Holybro飞行控制器系列的最新升级版本。
该产品由Holybro设计和开发。

![Durandal](../../assets/flight_controller/durandal/durandal_hero.jpg)

主要特点包括：

- 传感器集成温度控制
- 强大的STM32H7微控制器，运行频率480MHz，配备2MB闪存和1MB RAM
- 具有更高温度稳定性的新型传感器
- 内部振动隔离系统
- 双高性能、低噪声IMU，专为苛刻的稳定应用设计

以下为关键特性摘要、[组装指南](../assembly/quick_start_durandal.md)以及[购买链接](#purchase)。

::: info
此飞行控制器为[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)产品。
:::

## 快速概览

#### 技术规格

- 主FMU处理器：STM32H743  
  - 32位ARM® Cortex®-M7，480MHz，2MB内存，1MB RAM  
- IO处理器：STM32F100  
  - 32位ARM® Cortex®-M3，24MHz，8KB SRAM  
- 机载传感器  
  - 加速度计/陀螺仪：ICM-20689  
  - 加速度计/陀螺仪：BMI088或ICM20602  
  - 磁力计：IST8310  
  - 气压计：MS5611  
- GPS：u-blox Neo-M8N GPS/GLONASS 接收器；集成磁力计IST8310

#### 接口

- 8-13 个 PWM 舵机输出（8 个来自 IO，5 个来自 FMU）
- 6 个 FMU 上的专用 PWM/Capture 输入
- 专用 R/C 输入用于 Spektrum / DSM
- 专用 R/C 输入用于 CPPM 和 S.Bus
- 专用 S.Bus 舵机输出和模拟/PWM RSSI 输入
- 5 个通用串口
  - 3 个带有完整流控制
  - 1 个带有独立 1.5A 电流限制
- 3 个 I2C 接口
- 4 个 SPI 总线
  - 1 个内部高速 SPI 传感器总线，4 个芯片选择和 6 个 DRDY
  - 1 个内部低噪声 SPI 总线，专用于 XXX
  - 气压计，2 个芯片选择，无 DRDY
  - 1 个内部 SPI 总线，专用于 FRAM
  - 支持位于传感器模块上的温度控制
  - 1 个外部 SPI 总线
- 最多 2 个 CAN 总线用于双 CAN
  - 每个 CAN 总线都有独立的静默控制或 ESC RX-MUX 控制
- 2 个电池的电压/电流模拟输入
- 2 个额外的模拟输入

#### 电气数据

- 电源模块输出：4.9~5.5V
- 最大输入电压：6V
- 最大电流检测：120A
- USB电源输入：4.75~5.25V
- 舵机轨道输入：0~36V

#### 机械数据

- 尺寸：80x45x20.5mm
- 重量：68.8克

#### 其他特性

- 工作温度: -40~85C
- 存储温度: -40~85C
- CE
- FCC
- RoHS合规（无铅）

如需更多信息请查看: [Durandal技术数据表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Durandal_technical_data_sheet_90f8875d-8035-4632-a936-a0d178062077.pdf)

<a id="purchase"></a>

## 购买地点

从[Holybro](https://holybro.com/collections/autopilot-flight-controllers/products/durandal)订购。

<a id="connections"></a>

## 连接

端口/连接的位置如图所示（以及下方的[引脚分配部分](#引脚分配)）。

### 顶部

![Durandal - 顶部引脚分配（原理图）](../../assets/flight_controller/durandal/durandal_pinouts_top.jpg)

### 正面

![Durandal - 正面引脚分配（原理图）](../../assets/flight_controller/durandal/durandal_pinouts_front.jpg)

### 背面

![Durandal - 背面引脚图（示意图）](../../assets/flight_controller/durandal/durandal_pinouts_back.jpg)

### 右侧

![Durandal - 右侧引脚图（原理图）](../../assets/flight_controller/durandal/durandal_pinouts_right.jpg)

### 左侧

![Durandal - 左侧引脚分配 (示意图)](../../assets/flight_controller/durandal/durandal_pinouts_left.jpg)

## 维度

所有尺寸单位均为毫米。

![Durandal Dimensions](../../assets/flight_controller/durandal/durandal_dimensions.jpg)

<!--

## 电压规格

*Pixhawk 4* 可在提供三个电源的情况下实现电源三重冗余。三个电源轨为：**POWER1**、**POWER2** 和 **USB**。

::: info
输出电源轨 **FMU PWM OUT** 和 **I/O PWM OUT** (0V 至 36V) 不会为飞控板供电（也不由飞控板供电）。  
您必须为 **POWER1**、**POWER2** 或 **USB** 中的任意一个供电，否则板子将无法供电。  
:::

**正常操作最大额定值**

在此条件下，系统将按照以下顺序使用所有可用电源供电：  
1. **POWER1** 和 **POWER2** 输入 (4.9V 至 5.5V)  
1. **USB** 输入 (4.75V 至 5.25V)  

-->

<!--
**绝对最大额定值**

在此条件下，系统将不会消耗任何电源（无法运行），但硬件不会受损。  
1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 不会损坏）  
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 不会损坏）  
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 不会损坏）  
-->

## 组装与设置

[Durandal布线快速入门](../assembly/quick_start_durandal.md)提供了如何组装必需/重要外设的说明，包括GPS、电源管理板等。

## 构建固件

:::tip
大多数用户无需手动构建此固件！
当连接合适硬件时，QGroundControl 会自动预构建并安装。
:::

要[构建PX4](../dev_setup/building_px4.md)以适配该目标：

```
make holybro_durandal-v1_default
```

## 串口映射

| UART   | 设备       | 端口          |
| ------ | ---------- | ------------- |
| USART1 | /dev/ttyS0 | GPS1          |
| USART2 | /dev/ttyS1 | TELEM1        |
| USART3 | /dev/ttyS2 | TELEM2        |
| UART4  | /dev/ttyS3 | TELEM4/GPS2   |
| USART6 | /dev/ttyS4 | TELEM3        |
| UART7  | /dev/ttyS5 | 调试控制台      |
| UART8  | /dev/ttyS6 | PX4IO         |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->

<a id="debug_port"></a>

## 调试端口

[PX4 系统控制台](../debug/system_console.md)和[SWD 接口](../debug/swd_debug.md)运行在 _Debug Port_ 上。

其引脚定义和连接器符合 [Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini) 接口规范，该规范定义在 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 中。

有关布线和调试的信息请参阅上述链接。

::: info
I/O 板未暴露调试端口。
:::

## 外设

- [数字空速传感器](https://store-drotek.com/793-digital-differential-airspeed-sensor-kit-.html)
- [遥测无线电模块](../telemetry/index.md)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机体

任何可使用普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/地面车或船只。

完整的支持配置集可参考[机体参考](../airframes/airframe_reference.md)。

## 引脚分配

_Durandal_ 的引脚分配如下所示。
这些也可以从[这里](https://holybro.com/collections/autopilot-flight-controllers/products/Durandal-Pinouts)下载。

### 顶部引脚

![Durandal - 顶部引脚 (原理图)](../../assets/flight_controller/durandal/durandal_pinouts_top.jpg)

### 前面板引脚排列

![Durandal - 前面板引脚排列 (原理图)](../../assets/flight_controller/durandal/durandal_pinouts_front.jpg)

#### SUBS 输出端口

| 引脚       | 信号             | 电压   |
| ---------- | ---------------- | ------ |
| 1 (红色)   | -                | -      |
| 2 (黄色)   | SBUS_OUT/RSSI_IN | +3.3V  |
| 3 (黑色)   | GND              | GND    |

#### DSM RC端口

| 引脚       | 信号     | 电压   |
| ---------- | -------- | ------ |
| 1 (红色)   | VDD_3V3  | +3.3V  |
| 2 (黄色)   | DSM输入  | +3.3V  |
| 3 (黑色)   | GND      | GND    |

#### I2C A端口

| 引脚       | 信号 | 电压  |
| --------- | ------ | ----- |
| 1（红色）   | VCC    | +5V   |
| 2（黑色） | SCL4   | +3.3V |
| 3（黑色） | SDA4   | +3.3V |
| 4（黑色） | GND    | GND   |

#### CAN1端口

| 引脚       | 信号 | 电压  |
| --------- | ------ | ----- |
| 1 (红色)   | VCC    | +5V   |
| 2 (黑色) | CAN H  | +3.3V |
| 3 (黑色) | CAN L  | +3.3V |
| 4 (黑色) | GND    | GND   |

<a id="gps"></a>

#### GPS端口

| 引脚        | 信号              | 电压    |
| ---------- | ----------------- | ------- |
| 1 (红色)    | VCC               | +5V     |
| 2 (黑色)    | TX（输出）        | +3.3V   |
| 3 (黑色)    | RX（输入）        | +3.3V   |
| 4 (黑色)    | SCL1              | +3.3V   |
| 5 (黑色)    | SDA1              | +3.3V   |
| 6 (黑色)    | SAFETY_SWITCH     | +3.3V   |
| 7 (黑色)    | SAFETY_SWITCH_LED | +3.3V   |
| 8 (黑色)    | VDD_3V3           | +3.3V   |
| 9 (黑色)    | BUZZER            | +5V     |
| 10 (黑色)   | GND               | GND     |

<a id="telem4_i2cb"></a>

#### TELEM4 I2CB端口

| 引脚       | 信号        | 电压   |
| --------- | ----------- | ----- |
| 1（红色）   | VCC        | +5V   |
| 2（黑色）   | TX（输出）   | +3.3V |
| 3（黑色）   | RX（输入）   | -     |
| 4（黑色）   | SCL2       | -     |
| 5（黑色）   | SDA2       | +3.3V |
| 6（黑色）   | GND        | GND   |

<a id="telem1_2_3"></a>

#### TELEM3, TELEM2, TELEM1 端口

| 引脚       | 信号        | 电压    |
| ---------- | ----------- | ------- |
| 1（红色）  | VCC         | +5V     |
| 2（黑色）  | TX（输出）  | +3.3V   |
| 3（黑色）  | RX（输入）  | +3.3V   |
| 4（黑色）  | CTS（输入） | +3.3V   |
| 5（黑色）  | RTS（输出） | +3.3V   |
| 6（黑色）  | GND         | GND     |

<a id="power"></a>

#### 电源端口

| 引脚       | 信号    | 电压   |
| --------- | ------- | ----- |
| 1（红色）   | VCC     | +5V   |
| 2（黑色）   | VCC     | +5V   |
| 3（黑色）   | 电流    | +3.3V |
| 4（黑色）   | 电压    | +3.3V |
| 5（黑色）   | GND     | GND   |
| 6（黑色）   | GND     | GND   |

### 后部引脚分配

![Durandal - 后部引脚分配 (原理图)](../../assets/flight_controller/durandal/durandal_pinouts_back.jpg)

#### MAIN Out

| 引脚 | 信号    | 电压    | +          | -   |
|----|--------|--------|-----------|----|
| 1  | IO_CH1 | +3.3V  | VDD_SERVO | GND |
| 2  | IO_CH2 | +3.3V  | VDD_SERVO | GND |
| 3  | IO_CH3 | +3.3V  | VDD_SERVO | GND |
| 4  | IO_CH4 | +3.3V  | VDD_SERVO | GND |
| 5  | IO_CH5 | +3.3V  | VDD_SERVO | GND |
| 6  | IO_CH6 | +3.3V  | VDD_SERVO | GND |
| 7  | IO_CH7 | +3.3V  | VDD_SERVO | GND |
| 8  | IO_CH8 | +3.3V  | VDD_SERVO | GND |

#### AUX Out

| 引脚 | 信号       | 电压   | +         | -   |
| ---- | ---------- | ------ | --------- | --- |
| 1    | FMU_CH1    | +3.3V  | VDD_SERVO | GND |
| 2    | FMU_CH2    | +3.3V  | VDD_SERVO | GND |
| 3    | FMU_CH3    | +3.3V  | VDD_SERVO | GND |
| 4    | FMU_CH4    | +3.3V  | VDD_SERVO | GND |
| 5    | FMU_CH5    | +3.3V  | VDD_SERVO | GND |

#### RC IN

| 引脚 | 信号         | 电压  |
| --- | -------------- | ----- |
| S   | SBUS_IN/PPM_IN | +3.3V |
| +   | VCC            | +5V   |
| -   | GND            | GND   |

### 右侧引脚分配

![Durandal - 右侧引脚分配（原理图）](../../assets/flight_controller/durandal/durandal_pinouts_right.jpg)

#### CAN2端口

| 引脚       | 信号   | 电压   |
| --------- | ------ | ----- |
| 1 (红色)   | VCC    | +5V   |
| 2 (黑色)   | CAN H  | +3.3V |
| 3 (黑色)   | CAN L  | +3.3V |
| 4 (黑色)   | GND    | GND   |

#### CAP & ADC IN 接口

| 引脚       | 信号         | 电压                     |
| ---------- | ------------ | ------------------------ |
| 1 (红色)   | VCC          | +5V                      |
| 2 (黑色)   | FMU_CAP6     | +3.3V                    |
| 3 (黑色)   | FMU_CAP5     | +3.3V                    |
| 4 (黑色)   | FMU_CAP4     | +3.3V                    |
| 5 (黑色)   | FMU_CAP3     | +3.3V                    |
| 6 (黑色)   | FMU_CAP2     | +3.3V                    |
| 7 (黑色)   | FMU_CAP1     | +3.3V                    |
| 8 (黑色)   | ADC1_SPARE_1 | +3.3V [++](#warn_sensor) |
| 9 (黑色)   | ADC1_SPARE_2 | +6.6V [++](#warn_sensor) |
| 10 (黑色)  | GND          | 接地                     |

<a id="warn_sensor"></a>

:::warning
\++ 连接到8、9号引脚的传感器不得发送超过所示电压的信号。
:::

### 左侧引脚布局

![Durandal - 左侧引脚布局（原理图）](../../assets/flight_controller/durandal/durandal_pinouts_left.jpg)

<a id="debug_port"></a>

#### 调试端口

| 针脚       | 信号   | 电压   |
| ---------- | ------ | ------ |
| 1 (红色)   | VT     | +3.3V  |
| 2 (黑色)   | TX     | +3.3V  |
| 3 (黑色)   | RX     | +3.3V  |
| 4 (黑色)   | SWDIO  | +3.3V  |
| 5 (黑色)   | SWCLK  | +3.3V  |
| 6 (黑色)   | GND    | GND    |

#### SPI端口

| 引脚       | 信号 | 电压  |
| --------- | ------ | ----- |
| 1 (红色)   | VCC    | +5V   |
| 2 (黑色) | SCK    | +3.3V |
| 3 (黑色) | MISO   | +3.3V |
| 4 (黑色) | MOSI   | +3.3V |
| 5 (黑色) | CS1    | +3.3V |
| 6 (黑色) | CS2    | +3.3V |
| 7 (黑色) | GND    | GND   |

#### USB端口

| 针脚       | 信号 | 电压  |
| --------- | ------ | ----- |
| 1 (red)   | VBUS   | +5V   |
| 2 (black) | DM     | +3.3V |
| 3 (black) | DP     | +3.3V |
| 4 (black) | GND    | GND   |

## 更多信息

- [Durandal Wiring QuickStart](../assembly/quick_start_durandal.md)
- [Durandal Technical Data Sheet](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Durandal_technical_data_sheet_90f8875d-8035-4632-a936-a0d178062077.pdf)
- [Durandal Pinouts](https://holybro.com/collections/autopilot-flight-controllers/products/Durandal-Pinouts) (Holybro)