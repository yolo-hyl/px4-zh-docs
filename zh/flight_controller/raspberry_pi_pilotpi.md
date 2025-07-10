

# RPi PilotPi Shield

<LinkedBadge type="warning" text="Experimental" url="../flight_controller/autopilot_experimental.html"/>

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](mailto:lhf2613@gmail.com)。
:::

_PilotPi_ shield 是一个完整功能方案，可在 Raspberry Pi 上直接运行 PX4 自动驾驶仪。  
它被设计为一个低成本但高度可扩展的平台，Linux 和 PX4 双方会持续更新维护。  
无需专有驱动程序，所有组件均获得 RPi 和 PX4 社区的上游支持。  
PCB 和原理图也均为开源。

![PilotPi with RPi 4B](../../assets/flight_controller/pilotpi/hardware-pilotpi4b.png)

## 快速概览

- 支持的RPi板：
  - Raspberry Pi 2B/3B/3B+/4B
- 支持的系统：
  - Raspberry Pi OS
  - Ubuntu Server (armhf/arm64)
- 加速度计/陀螺仪：
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
  - 3~6S电池（带内置电压检测）
  - 通过USB线缆为Pi供电
- 可用性: _准备发货_

## 连接性

Shield提供以下功能：

- 16路PWM输出通道  
- GPS连接器  
- 遥测连接器  
- 外部I2C总线连接器（**注意**：与CSI摄像头冲突）  
- RC输入端口（SBUS）  
- 3路ADC通道（范围0~5V）  
- 2\*8 2.54mm未使用GPIO连接器  

可直接从RPi访问：  

- 4路USB连接器  
- CSI连接器（**注意**：与外部I2C总线冲突）  
- 等等

## 推荐接线

![PilotPi PowerPart接线](../../assets/flight_controller/pilotpi/pilotpi_pwr_wiring.png)

![PilotPi SensorPart接线](../../assets/flight_controller/pilotpi/pilotpi_sens_wiring.png)

## 引脚分配

:::warning
仍使用旧版的GH1.25连接器。
接线与Pixhawk 2.4.8兼容
:::

### 连接器

#### GPS连接器

映射到 `/dev/ttySC0`

| 引脚 | 信号 | 电压 |
| --- | ------ | ---- |
| 1   | VCC    | +5V  |
| 2   | TX     | +3v3 |
| 3   | RX     | +3v3 |
| 4   | NC     | +3v3 |
| 5   | NC     | +3v3 |
| 6   | GND    | GND  |

#### 遥测连接器

映射到 `/dev/ttySC1`

| 引脚 | 信号 | 电压 |
| --- | ------ | ---- |
| 1   | VCC    | +5V  |
| 2   | TX     | +3v3 |
| 3   | RX     | +3v3 |
| 4   | CTS    | +3v3 |
| 5   | RTS    | +3v3 |
| 6   | GND    | GND  |

#### 外部I2C连接器

映射到 `/dev/i2c-0`

| 引脚 | 信号 | 电压          |
| --- | ------ | ------------- |
| 1   | VCC    | +5V           |
| 2   | SCL    | +3v3(上拉)    |
| 3   | SDA    | +3v3(上拉)    |
| 4   | GND    | GND           |

#### 遥控&ADC2/3/4

遥控信号映射到 `/dev/ttyAMA0`，并在RX线上配置信号反相开关。

| 引脚 | 信号 | 电压         |
| --- | ------ | ------------ |
| 1   | RC     | +3V3~+5V     |
| 2   | VCC    | +5V          |
| 3   | GND    | GND          |

- ADC1 内部连接到电压分压器，用于监测电池电压。
- ADC2 未使用。
- ADC3 可连接模拟空速传感器。
- ADC4 通过跳帽连接至ADC和VCC，用于监测系统电压。

| 引脚 | 信号 | 电压   |
| --- | ------ | ------ |
| 1   | ADCx   | 0V~+5V |
| 2   | VCC    | +5V    |
| 3   | GND    | GND    |

::: info
ADC3 & 4 具有替代的 VCC 电源
当 'Vref' 开关开启时，'VCC' 引脚由 REF5050 驱动。
:::

#### 板载未使用GPIO引脚

| 屏蔽引脚 | BCM编号 | WiringPi编号 | RPi引脚 |
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
| 15         | 26  | 25       | 37      |
| 16         | GND | GND      | GND     |

### 开关

#### RC反相器

该开关将决定接收机线路的信号极性：`UART_RX = SW xor RC_INPUT`

- 开启：适用于SBUS（信号反相）
- 关闭：保持原极性

#### Vref

ADC 3 & 4 的电压由以下来源驱动：

- 若开启：来自 REF5050 的 Vref 输出
- 若关闭：直接来自 RPi 的 5V 引脚

#### 启动模式

此开关连接到 Pin22(BCM25)。
系统 rc 脚本将检查其值并决定 PX4 是否应随系统启动而自动启动。

- 开启：自动启动 PX4
- 关闭：不启动 PX4

## 开发者快速入门

请参考针对运行在你的树莓派上的操作系统的具体说明：

- [树莓派操作系统精简版 (armhf)](raspberry_pi_pilotpi_rpios.md)
- [Ubuntu 服务器 (arm64 & armhf)](raspberry_pi_pilotpi_ubuntu_server.md)