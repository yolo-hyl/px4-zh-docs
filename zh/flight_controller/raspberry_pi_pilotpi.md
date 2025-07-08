# RPi PilotPi Shield

<LinkedBadge type="warning" text="Experimental" url="../flight_controller/autopilot_experimental.html"/>

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](mailto:lhf2613@gmail.com)。
:::

_PilotPi_ 盾板是直接在 Raspberry Pi 上运行 PX4 自动驾驶仪的完整解决方案。  
它被设计为低成本但高度可扩展的平台，Linux 和 PX4 方面持续更新。  
所有组件均无需专有驱动程序，因 RPi 和 PX4 社区均提供上游支持。  
PCB 和原理图也是开源的。

![PilotPi 与 RPi 4B](../../assets/flight_controller/pilotpi/hardware-pilotpi4b.png)

## 快速概览

- 支持的 RPi 板：
  - Raspberry Pi 2B/3B/3B+/4B
- 支持的操作系统：
  - Raspberry Pi OS
  - Ubuntu Server (armhf/arm64)
- 加速度计 / 陀螺仪：
  - ICM42688P
- 磁力计：
  - IST8310
- 气压计：
  - MS5611
- PWM：
  - PCA9685
- ADC：
  - ADS1115
- 电源：
  - 3~6S 电池，内置电压检测。
  - 通过 USB 线为 Pi 供电
- 可用性：_准备发货_

## 连接性

盾板提供：

- 16 通道 PWM 输出
- GPS 接口
- 通信接口
- 外部 I2C 总线接口（**注意：** 与 CSI 摄像头冲突）
- RC 输入端口（SBUS）
- 3 个 0~5V 范围的 ADC 通道
- 2\*8 2.54mm 未使用 GPIO 接口

可直接从 RPi 访问：

- 4 个 USB 接口
- CSI 接口（**注意：** 与外部 I2C 总线冲突）
- 等等

## 推荐接线

![PilotPi 电源部分接线](../../assets/flight_controller/pilotpi/pilotpi_pwr_wiring.png)

![PilotPi 传感器部分接线](../../assets/flight_controller/pilotpi/pilotpi_sens_wiring.png)

## 引脚分配

:::warning
仍使用旧版 GH1.25 接口。
接线与 Pixhawk 2.4.8 兼容。
:::

### 接口

#### GPS 接口

映射到 `/dev/ttySC0`

| 引脚 | 信号 | 电压 |
| --- | ------ | ---- |
| 1   | VCC    | +5V  |
| 2   | TX     | +3v3 |
| 3   | RX     | +3v3 |
| 4   | NC     | +3v3 |
| 5   | NC     | +3v3 |
| 6   | GND    | GND  |

#### 通信接口

映射到 `/dev/ttySC1`

| 引脚 | 信号 | 电压 |
| --- | ------ | ---- |
| 1   | VCC    | +5V  |
| 2   | TX     | +3v3 |
| 3   | RX     | +3v3 |
| 4   | CTS    | +3v3 |
| 5   | RTS    | +3v3 |
| 6   | GND    | GND  |

#### 外部 I2C 接口

映射到 `/dev/i2c-0`

| 引脚 | 信号 | 电压          |
| --- | ------ | ------------- |
| 1   | VCC    | +5V           |
| 2   | SCL    | +3v3(pullups) |
| 3   | SDA    | +3v3(pullups) |
| 4   | GND    | GND           |

#### RC & ADC2/3/4

RC 映射到 `/dev/ttyAMA0`，通过 RX 线的信号反相器开关。

| 引脚 | 信号 | 电压     |
| --- | ------ | -------- |
| 1   | RC     | +3V3~+5V |
| 2   | VCC    | +5V      |
| 3   | GND    | GND      |

- ADC1 内部连接到分压器，用于电池电压监测。
- ADC2 未使用。
- ADC3 可连接模拟空速传感器。
- ADC4 在 ADC 和 VCC 之间有跳线帽，用于监测系统电压水平。

| 引脚 | 信号 | 电压   |
| --- | ------ | ------ |
| 1   | ADCx   | 0V~+5V |
| 2   | VCC    | +5V    |
| 3   | GND    | GND    |

::: info
当 'Vref' 开关开启时，ADC3 & 4 的 VCC 由 REF5050 提供。
:::

#### 板载未使用 GPIO

| 盾板引脚 | BCM | WiringPi | RPi 引脚 |
| ---------- | --- | -------- | ------- |
| 1          | 3V3 | 3v3      | 3V3     |
| 2          | 5V  | 5V       | 5V      |
| 3          | 4   | 7        | 7       |
| 4          | 14  | 15       | 8       |
| 5          | 17  | 0        | 11      |
| 6          | 27  | 2        | 13      |
| 7          | 22  | 3        | 15      |
| 8          | 23  | 4        | 16      |
| 9          | 7   | 11       | 26      |
| 10         | 5   | 21       | 29      |
| 11         | 6   | 22       | 31      |
| 12         | 12  | 26       | 32      |
| 13         | 13  | 23       | 33      |
| 14         | 16  | 27       | 36      |
| 15         | 26  | 10       | 37      |
| 16         | 19  | 9        | 35      |

### 开关

#### RC 反相器

控制 `/dev/ttyAMA0` 的 RX 信号反相。

#### Vref

控制 ADC3 & 4 的参考电压源。

#### 电源选择

选择使用 USB 或电池供电。

## 开发者

[RPi PilotPi Shield](https://github.com/ArduPilot/ArduPilot/tree/master/Tools/Frame-Files/PilotPi) 是由 [ArduPilot](https://ardupilot.org/) 社区开发的开源项目。

## 附录

### 接线图

![PilotPi 接线图](../../assets/flight_controller/pilotpi/pilotpi_schematics.png)

### 3D 模型

[PilotPi 3D 模型](https://github.com/ArduPilot/ArduPilot/tree/master/Tools/Frame-Files/PilotPi) 可在 GitHub 上找到。

## 开发者

[RPi PilotPi Shield](https://github.com/ArduPilot/ArduPilot/tree/master/Tools/Frame-Files/PilotPi) 是由 [ArduPilot](https://ardupilot.org/) 社区开发的开源项目。