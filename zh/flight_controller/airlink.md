

# Sky-Drones AIRLink

:::warning
PX4 不制造此（或任何）自动驾驶仪。
如有硬件支持或合规性问题，请联系[制造商](https://sky-drones.com/)。
:::

[AIRLink](https://sky-drones.com/airlink) 代表人工智能与远程连接。该设备集成了尖端无人机自动驾驶仪、AI任务计算机和LTE/5G连接模块。AIRLink帮助新无人机制造商将产品上市时间从数年数月缩短至数周。

![AIRLink](../../assets/flight_controller/airlink/airlink-main.jpg)

::: info
此飞控是[制造商支持的](../flight_controller/autopilot_manufacturer_supported.md)。
:::

AIRLink包含两台计算机和集成LTE模块：

- 飞行控制计算机（自动驾驶仪）采用三重冗余减震和温度稳定的IMU。
- 强大的AI任务计算机支持先进无人机软件功能，如计算机视觉和避障、数字高清视频流以及载荷数据流。
- LTE/5G和WiFi连接模块提供持续宽带互联网连接，为远程作业提供支持。

## 功能亮点

<lite-youtube videoid="VcBx9DLPN54" title="SmartAP AIRLink - The Most Advanced AI Drone Avionics"/>

## 规格

- **传感器**

  - 3个加速度计，3个陀螺仪，3个磁力计，3个气压传感器
  - 全球导航卫星系统(GNSS)、测距仪、激光雷达、光流、摄像头
  - 三重冗余IMU
  - 振动减震
  - 温度稳定

- **飞控**

  - STM32F7, ARM Cortex M7 with FPU, 216 MHz, 2MB 闪存, 512KB 内存
  - STM32F1, I/O协处理器
  - 以太网, 10/100 Mbps
  - 带AI任务计算机的局域网
  - 8个UART串口: 数传1, 数传2 (AI任务计算机), 数传3, GPS1, GPS2, 附加串口, 串口调试控制台, IO
  - 2个CAN总线: CAN1, CAN2
  - 支持MAVLink的USB
  - 调试串口
  - 遥控输入, SBUS输入, 信号强度输入, PPM输入
  - 16个PWM舵机输出(8个来自IO, 8个来自FMU)
  - 3个I2C端口
  - 高功率压电蜂鸣器驱动器
  - 高功率RGB LED
  - 安全开关/LED选项

- **AI任务计算机**

  - 六核CPU: 双核Cortex-A72 + 四核Cortex-A53
  - GPU Mali-T864, 支持OpenGL ES1.1/2.0/3.0/3.1
  - 支持4K VP8/9, 4K 10bit H265/H264 60fps解码的VPU
  - 远程电源控制、软件重置、关机、RTC唤醒、睡眠模式
  - 双通道4GB LPDDR4内存
  - 16GB eMMC存储
  - 最大256GB MicroSD卡
  - 10/100/1000 Native Gigabit以太网
  - WiFi 802.11a/b/g/n/ac, 蓝牙
  - USB 3.0 Type-C
  - 2路视频: 4 Lane MIPI CSI (FPV摄像头) 和 4 Lane MIPI CSI带HDMI输入 (载荷摄像头)

- **LTE/5G连接模块**

  - 最大600 Mbps带宽
  - 5G sub-6和毫米波, SA和NSA模式
  - 4G Cat 20, 最大7xCA, 256-QAM DL/UL, 2xCA UL
  - 4x4 MIMO用于4G和5G (sub-6频段)
  - 3G HSPA+
  - 经JRL/JTBL, FCC, PTCRB, RED, GCF认证
  - 天线, 4x4 MIMO
  - 频段: 全球覆盖

## 购买渠道

从原始Sky-Drones商店购买（全球配送，订单处理时间为1-2天）：

- [购买 AIRLink Enterprise 4G](https://sky-drones.com/sets/airlink-enterprise-set.html)
- [购买 AIRLink Enterprise 5G](https://sky-drones.com/sets/airlink-5g-enterprise-set.html)
- [购买 AIRLink Core 4G](https://sky-drones.com/autopilots/airlink-core.html)
- [购买 AIRLink Core 5G](https://sky-drones.com/store/airlink-5g-core.html)

## AIRLink Enterprise 套件配件

<lite-youtube videoid="lex7axW8WQg" title="SmartAP AIRLink - Unboxing"/>

AIRLink Enterprise 配备了设置自动驾驶系统所需的所有组件。

标准套装包含：

- 1x AIRLink Enterprise 主机
- 1x 带CSI线缆的FPV摄像头
- 1x 带MMCX接口的WiFi天线
- 2x/4x 带MMCX接口的LTE/5G天线
- 1x HDMI转mini HDMI线缆
- 1x 线缆套装（包含7条适配所有接口的线缆）

[AIRLink Telemetry](https://sky-drones.com/sets/airlink-telemetry-set.html) 采用Microhard LAN/IP基射频微模块，作为扩展配件提供，与AIRLink完全兼容。

## 版本

AIRLink 提供了不同集成级别的版本，以满足无人机制造商的需求：_企业版_ 和 _核心版_。
企业版适用于快速启动、评估和原型设计，而核心版则针对深度集成和中高批量生产进行了优化。

**AIRLink 企业版**

SmartAP AIRLink 企业版专为原型设计和低到中等批量无人机生产而设计。
由于专用的安装孔和集成散热器，安装快速且简便。

![AIRLink 企业版](../../assets/flight_controller/airlink/airlink-enterprise.jpg)

**AIRLink 核心版**

SmartAP AIRLink 核心版专为中高批量生产和与客户硬件的深度集成而设计。其重量仅为 89 克，可安装在金属框架上以实现最佳散热。

![AIRLink 核心版](../../assets/flight_controller/airlink/airlink-core.jpg)

| 参数           | AIRLink 企业版                                          | AIRLink 核心版                                                                        |
| ---------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 外壳           | 铝制，集成散热器和风扇安装选项。                         | 需由设计方提供外部散热器或合理的散热方案。                                           |
| 尺寸           | 103 x 61 x 37 毫米                                       | 100 x 57 x 22 毫米                                                                 |
| 重量           | 198 克                                                   | 89 克                                                                              |
| 环境温度       | -40°C-..+50°C                                            | -40°C-..+50°C                                                                      |

## 关键特性

- **易于安装**

  ![Easy mount](../../assets/flight_controller/airlink/airlink-easy-to-mount.jpg)

- **标配FPV摄像头**

  ![FPV camera comes as standard](../../assets/flight_controller/airlink/airlink-fpv-camera.jpg)

## 接口

### 左侧

![左侧](../../assets/flight_controller/airlink/airlink-interfaces-left.jpg)

- **左侧接口：**

  - 电源输入（带电压和电流监控）
  - AI Mission Computer micro SD卡
  - 飞控 micro SD卡
  - AI Mission Computer USB Type-C
  - PPM输入，SBUS输出，RSSI监控

- **POWER - JST GH SM10B-GHS-TB**

  | 引脚号 | 引脚名称       | 方向 | 电压   | 功能                   |
  | ------ | -------------- | ---- | ------ | ---------------------- |
  | 1      | 12V            | 输入 | +12V   | 主电源输入             |
  | 2      | 12V            | 输入 | +12V   | 主电源输入             |
  | 3      | 12V            | 输入 | +12V   | 主电源输入             |
  | 4      | BAT_CURRENT    | 输入 | +3.3V  | 电池电流监控           |
  | 5      | BAT_VOLTAGE    | 输入 | +3.3V  | 电池电压监控           |
  | 6      | 3V3            | 输出 | +3.3V  | 3.3V输出               |
  | 7      | PWR_KEY        | 输入 | +3.3V  | 电源按键输入           |
  | 8      | GND            | 接地 |        |                        |
  | 9      | GND            | 接地 |        |                        |
  | 10     | GND            | 接地 |        |                        |

- **CPU SD卡 - microSD**
- **CPU USB - USB Type C**
- **RC连接器 - JST GH SM06B-GHS-TB**

  | 引脚号 | 引脚名称  | 方向 | 电压   | 功能     |
  | ------ | --------- | ---- | ------ | -------- |
  | 1      | 5V        | 输出 | +5V    | 5V输出   |
  | 2      | PPM_IN    | 输入 | +3.3V  | PPM输入  |
  | 3      | RSSI_IN   | 输入 | +3.3V  | RSSI输入 |
  | 4      | FAN_OUT   | 输出 | +5V    | 风扇输出 |
  | 5      | SBUS_OUT  | 输出 | +3.3V  | SBUS输出 |
  | 6      | GND       | 接地 |        |          |

* **FMU SD卡 - microSD**

### 右侧

![Right side](../../assets/flight_controller/airlink/airlink-interfaces-right.jpg)

- **右侧接口：**

  - 带电源输出的以太网端口  
  - 遥测端口  
  - 第二个GPS端口  
  - 备用I2C/UART端口  
  - 飞控USB Type-C  
  - 微型SIM卡  
  - HDMI输入端口（载荷相机）

- **ETHERNET - JST GH SM08B-GHS-TB**

  | 引脚编号 | 引脚名称  | 方向 | 电压   | 功能                   |
  | -------- | --------- | ---- | ------ | ---------------------- |
  | 1        | 5V        | OUT  | +5V    | 无线电模块电源供应     |
  | 2        | 5V        | OUT  | +5V    | 无线电模块电源供应     |
  | 3        | ETH_TXP   | OUT  | +3.3V  | 以太网发送正极         |
  | 4        | ETH_TXN   | OUT  | +3.3V  | 以太网发送负极         |
  | 5        | ETH_RXP   | IN   | +3.3V  | 以太网接收正极         |
  | 6        | ETH_RXN   | IN   | +3.3V  | 以太网接收负极         |
  | 7        | GND       | 接地 |        |                        |
  | 8        | GND       | 接地 |        |                        |

- **TEL3 - JST GH SM06B-GHS-TB**

  | 引脚编号 | 引脚名称     | 方向 | 电压   | 功能             |
  | -------- | ------------ | ---- | ------ | ---------------- |
  | 1        | 5V           | OUT  | +5V    | 电源输出         |
  | 2        | USART2_TX    | OUT  | +3.3V  | 遥测3发送        |
  | 3        | USART2_RX    | IN   | +3.3V  | 遥测3接收        |
  | 4        | USART2_CTS   | IN   | +3.3V  | 遥测3 CTS        |
  | 5        | USART2_RTS   | OUT  | +3.3V  | 遥测3 RTS        |
  | 6        | GND          | 接地 |        |                  |

- **I2C3 / UART4 - JST GH SM06B-GHS-TB**

  | 引脚编号 | 引脚名称   | 方向 | 电压   | 功能             |
  | -------- | ---------- | ---- | ------ | ---------------- |
  | 1        | 5V         | OUT  | +5V    | 电源输出         |
  | 2        | USART4_TX  | OUT  | +3.3V  | UART 4发送       |
  | 3        | USART4_RX  | IN   | +3.3V  | UART 4接收       |
  | 4        | I2C3_SCL   | I/O  | +3.3V  | I2C3时钟         |
  | 5        | I2C3_SDA   | I/O  | +3.3V  | I2C3数据         |
  | 6        | GND        | 接地 |        |                  |

- **GPS2 - JST GH SM06B-GHS-TB**

  | 引脚编号 | 引脚名称   | 方向 | 电压   | 功能             |
  | -------- | ---------- | ---- | ------ | ---------------- |
  | 1        | 5V         | OUT  | +5V    | 电源输出         |
  | 2        | USART8_TX  | OUT  | +3.3V  | UART 8发送       |
  | 3        | USART8_RX  | IN   | +3.3V  | UART 8接收       |
  | 4        | I2C2_SCL   | I/O  | +3.3V  | I2C2时钟         |
  | 5        | I2C2_SDA   | I/O  | +3.3V  | I2C2数据         |
  | 6        | GND        | 接地 |        |                  |

- **FMU USB - USB Type C**  
- **SIM Card - micro SIM**  
- **HDMI - mini HDMI**

### 正面

![Front side](../../assets/flight_controller/airlink/airlink-interfaces-front.jpg)

- **正面接口:**

  - 主GNSS和指南针端口
  - 主遥测端口
  - CSI相机输入
  - CAN 1
  - CAN 2

- **TEL1 - JST GH SM06B-GHS-TB**

  | 引脚编号 | 引脚名称   | 方向 | 电压   | 功能            |
  | -------- | ---------- | ---- | ------ | --------------- |
  | 1        | 5V         | 输出 | +5V    | 电源输出        |
  | 2        | USART7_TX  | 输出 | +3.3V  | 遥测1发送       |
  | 3        | USART7_RX  | 输入 | +3.3V  | 遥测1接收       |
  | 4        | USART7_CTS | 输入 | +3.3V  | 遥测1清空发送   |
  | 5        | USART7_RTS | 输出 | +3.3V  | 遥测1请求发送   |
  | 6        | GND        | 接地 |        |

- **GPS1 - JST GH SM10B-GHS-TB**

  | 引脚编号 | 引脚名称   | 方向 | 电压   | 功能            |
  | -------- | ---------- | ---- | ------ | --------------- |
  | 1        | 5V         | 输出 | +5V    | 电源输出        |
  | 2        | USART1_TX  | 输出 | +3.3V  | GPS1发送        |
  | 3        | USART1_RX  | 输入 | +3.3V  | GPS1接收        |
  | 4        | I2C1_SCL   | I/O  | +3.3V  | 指南针1时钟     |
  | 5        | I2C1_SDA   | I/O  | +3.3V  | 指南针1数据     |
  | 6        | SAFETY_BTN | 输入 | +3.3V  | 安全按钮        |
  | 7        | SAFETY_LED | 输出 | +3.3V  | 安全LED         |
  | 8        | +3V3       | 输出 | +3.3V  | 3.3V输出        |
  | 9        | BUZZER     | 输出 | +5V    | 蜂鸣器输出      |
  | 10       | GND        | 接地 |        |

- **CAN1 - JST GH SM04B-GHS-TB**

  | 引脚编号 | 引脚名称 | 方向 | 电压 | 功能            |
  | -------- | -------- | ---- | ---- | --------------- |
  | 1        | 5V       | 输出 | +5V  | 电源输出        |
  | 2        | CAN1_H   | I/O  | +5V  | CAN1高电平(120Ω) |
  | 3        | CAN1_L   | I/O  | +5V  | CAN1低电平(120Ω) |
  | 4        | GND      | 接地 |      |

- **CAN2 - JST GH SM04B-GHS-TB**

  | 引脚编号 | 引脚名称 | 方向 | 电压 | 功能            |
  | -------- | -------- | ---- | ---- | --------------- |
  | 1        | 5V       | 输出 | +5V  | 电源输出        |
  | 2        | CAN2_H   | I/O  | +5V  | CAN2高电平(120Ω) |
  | 3        | CAN2_L   | I/O  | +5V  | CAN2低电平(120Ω) |
  | 4        | GND      | 接地 |      |

- **相机 - FPC 30针，0.5mm间距**

### 背面

![背面](../../assets/flight_controller/airlink/airlink-interfaces-back.jpg)

- **背面接口：**

  - SBUS 输入
  - 16 个 PWM 输出通道
  - 2 个 LTE 天线接口（MIMO）
  - WiFi 天线接口（AP & 站点模式）

# 串口映射

AIRLink 拥有大量内部和外部串口：

| 串口     | UART    | 功能                                            |
| -------- | ------- | --------------------------------------------------- |
| 串口 0   | USB     | 控制台                                             |
| 串口 1   | UART 7  | 遥测 1                                         |
| 串口 2   | UART 5  | 遥测 2 (与任务计算机内部使用) |
| 串口 3   | USART 1 | GPS 1                                               |
| 串口 4   | UART 8  | GPS 2                                               |
| 串口 5   | USART 3 | 调试控制台 (内部连接器)                  |
| 串口 6   | USART 2 | 遥测 3                                         |
| 串口 7   | UART 4  | 外部 UART                                       |

## 遥控输入

遥控输入配置在SBUS引脚上，并通过内部的反相器连接到IO MCU。对于PPM接收器，请使用位于设备左侧的RC Connector PPM引脚。

![RC Input](../../assets/flight_controller/airlink/airlink-rc-input.jpg)

## 输出

AIRLink 有 16 个 PWM 输出。主输出 1-8 连接到 IO MCU。辅输出 1-8 连接到 FMU。

| 输出   | 定时器   | 通道     |
| ------ | -------- | -------- |
| AUX 1  | 定时器 1 | 通道 4   |
| AUX 2  | 定时器 1 | 通道 3   |
| AUX 3  | 定时器 1 | 通道 2   |
| AUX 4  | 定时器 1 | 通道 1   |
| AUX 5  | 定时器 4 | 通道 2   |
| AUX 6  | 定时器 4 | 通道 3   |
| AUX 7  | 定时器 12| 通道 1   |
| AUX 8  | 定时器 12| 通道 2   |

[DShot](../peripherals/dshot.md) 可用于前四个 AUX 引脚。

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接合适硬件时，_QGroundControl_ 会预先构建并自动安装。
:::

要[构建PX4](../dev_setup/building_px4.md)以适用于此目标：

```
make sky-drones_smartap-airlink
```

## 外设

- [SmartAP GPS](../gps_compass/gps_smartap.md) - 带有指南针、压力传感器和RGB LED的GPS模块
- [SmartAP PDB](../power_module/sky-drones_smartap-pdb.md) - 电源分配板

## 参考设计

![参考设计](../../assets/flight_controller/airlink/airlink-reference-design.png)

AIRLink CAD模型可在此处获取 [https://docs.sky-drones.com/airlink/cad-model](https://docs.sky-drones.com/airlink/cad-model)

AIRLink参考设计可根据要求提供。
请联系[Sky-Drones 联系页面](https://sky-drones.com/contact-us)

## 更多信息

有关设置和使用AIRLink系统的更多信息及说明，请参阅[AIRLink Documentation](https://docs.sky-drones.com/airlink/)。

如需技术支持、帮助和定制服务，请通过[Sky-Drones contact page](https://sky-drones.com/contact-us)联系我们。

更多信息请访问[www.sky-drones.com](https://sky-drones.com)。

常见问题解答请参阅[FAQ](https://docs.sky-drones.com/airlink/faq)。

## 有用的链接

- [AIRLink 产品页面](https://sky-drones.com/airlink)
- [AIRLink 文档](https://docs.sky-drones.com/avionics/airlink)
- [AIRLink 产品规格书](https://3182378893-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MTMlWysgDtJq8Hid1v7%2Fuploads%2F8AiuNNSwLYnZSscj7uIV%2FAIRLink-Datasheet.pdf?alt=media&token=cbf0c4bf-9ab1-40c5-a0af-c6babdddb690)
- [购买 AIRLink Enterprise 4G](https://sky-drones.com/sets/airlink-enterprise-set.html)
- [购买 AIRLink Enterprise 5G](https://sky-drones.com/sets/airlink-5g-enterprise-set.html)
- [购买 AIRLink Core 4G](https://sky-drones.com/autopilots/airlink-core.html)
- [购买 AIRLink Core 5G](https://sky-drones.com/store/airlink-5g-core.html)