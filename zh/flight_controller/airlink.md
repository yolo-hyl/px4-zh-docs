# Sky-Drones AIRLink

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://sky-drones.com/)。
:::

[AIRLink](https://sky-drones.com/airlink) 代表人工智能与远程连接。该单元集成了先进的无人机自动驾驶仪、AI 任务计算机和 LTE/5G 连接模块。AIRLink 可将新无人机制造商的上市时间从数年和数月缩短至数周。

![AIRLink](../../assets/flight_controller/airlink/airlink-main.jpg)

::: info
此飞控是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

AIRLink 包含两台计算机和集成 LTE 模块：

- 飞行控制计算机（自动驾驶仪）采用三重冗余减震且温度稳定化的 IMU。
- 强大的 AI 任务计算机实现高级无人机软件功能，如计算机视觉和避障、数字高清视频流以及有效载荷数据流。
- LTE/5G 和 WiFi 连接模块提供永久宽带互联网连接，支持远程工作流程。

## 功能亮点

<lite-youtube videoid="VcBx9DLPN54" title="SmartAP AIRLink - The Most Advanced AI Drone Avionics"/>## 规格

- **传感器**

  - 3x 加速度计，3x 陀螺仪，3x 磁力计，3x 压力传感器  
  - 全球导航卫星系统(GNSS)，测距仪，激光雷达，光流传感器，摄像头  
  - 三冗余惯性测量单元(IMU)  
  - 振动阻尼设计  
  - 温度稳定系统  

- **飞控器**

  - STM32F7，ARM Cortex M7 带FPU，216 MHz，2MB Flash，512 kB RAM  
  - STM32F1，I/O协处理器  
  - 以太网，10/100 Mbps  
  - 带AI任务计算机的局域网  
  - 8x 通用异步收发传输器(UART)：遥测1，遥测2（AI任务计算机），遥测3，GPS1，GPS2，扩展UART，串口调试控制台，IO  
  - 2x CAN总线：CAN1，CAN2  
  - 支持MAVLink的USB  
  - 调试串口  
  - 遥控器输入，SBUS输入，RSSI输入，PPM输入  
  - 16x PWM舵机输出（8路来自IO，8路来自FMU）  
  - 3x I2C端口  
  - 高功率压电蜂鸣器驱动器  
  - 高功率RGB LED  
  - 安全开关/LED选项  

- **AI任务计算机**

  - 六核CPU：双核Cortex-A72 + 四核Cortex-A53  
  - GPU Mali-T864，OpenGL ES1.1/2.0/3.0/3.1  
  - 带4K VP8/9解码的视频处理单元(VPU)，支持4K 10bit H265/H264 60fps解码  
  - 远程电源控制，软件复位，关机，RTC唤醒，睡眠模式  
  - 双通道4GB LPDDR4内存  
  - 16GB eMMC存储  
  - microSD卡（最大256GB）  
  - 10/100/1000 Mbps原生千兆以太网  
  - WiFi 802.11a/b/g/n/ac，蓝牙  
  - USB 3.0 Type C  
  - 2x 视频接口：4通道MIPI CSI（FPV摄像头）和带HDMI输入的4通道MIPI CSI（载荷摄像头）  

- **LTE/5G通信模块**

  - 最大600 Mbps带宽  
  - 5G sub-6和毫米波，支持SA和NSA模式  
  - 4G Cat 20，最大7xCA，256-QAM DL/UL，2xCA UL  
  - 4x4 MIMO支持4G和5G（sub-6频段）  
  - 3G HSPA+  
  - 通过JRL/JTBL、FCC、PTCRB、RED、GCF认证  
  - 天线，4x4 MIMO  
  - 频段：全球覆盖## 购买渠道

从原厂 Sky-Drones Store 购买（全球发货，订单处理时间为1-2天）:

- [购买 AIRLink Enterprise 4G](https://sky-drones.com/sets/airlink-enterprise-set.html)
- [购买 AIRLink Enterprise 5G](https://sky-drones.com/sets/airlink-5g-enterprise-set.html)
- [购买 AIRLink Core 4G](https://sky-drones.com/autopilots/airlink-core.html)
- [购买 AIRLink Core 5G](https://sky-drones.com/store/airlink-5g-core.html)

## AIRLink 企业套件配件

<lite-youtube videoid="lex7axW8WQg" title="SmartAP AIRLink - Unboxing"/>

AIRLink 企业版包含设置自动驾驶系统所需的所有组件。

标准套件包含：

- 1x AIRLink 企业版单元
- 1x 带CSI数据线的FPV摄像头
- 1x 带MMCX接口的WiFi天线
- 2x/4x 带MMCX接口的LTE/5G天线
- 1x HDMI转mini HDMI数据线
- 1x 线缆套装（7根线缆适配所有接口）

基于Microhard LAN/IP无线射频微模块的[AIRLink Telemetry](https://sky-drones.com/sets/airlink-telemetry-set.html)可作为扩展配件使用，完全兼容AIRLink。

## 版本

AIRLink 提供了不同集成级别的版本，以满足无人机制造商的需求：_Enterprise_ 和 _Core_。
AIRLink Enterprise 适合快速启动、评估和原型设计，而 Core 版本则针对深度集成和中高批量生产进行了优化。

**AIRLink Enterprise**

SmartAP AIRLink 的 Enterprise 版本适用于原型设计和中低批量无人机生产。
得益于专用的安装孔和用于散热的集成散热器，安装快速且简便。

![AIRLink Enterprise](../../assets/flight_controller/airlink/airlink-enterprise.jpg)

**AIRLink Core**

SmartAP AIRLink 的 Core 版本适用于中高批量生产和与客户硬件的深度集成。重量仅为 89 克，可安装在金属框架上以实现最佳散热。

![AIRLink Core](../../assets/flight_controller/airlink/airlink-core.jpg)

| 参数                | AIRLink Enterprise                                            | AIRLink Core                                                                           |
| ------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| 外壳                | 铝制，集成散热器和风扇安装选项。                            | 需由设计提供外部散热器或合理的散热方案。                                           |
| 尺寸                | L103 x W61 x H37 mm                                           | L100 x W57 x H22 mm                                                                  |
| 重量                | 198 克                                                        | 89 克                                                                                |
| 工作环境温度        | -40°C-..+50°C                                                 | -40°C-..+50°C                                                                        |## 核心特性

- **易于安装**

  ![易于安装](../../assets/flight_controller/airlink/airlink-easy-to-mount.jpg)

- **标配FPV摄像头**

  ![标配FPV摄像头](../../assets/flight_controller/airlink/airlink-fpv-camera.jpg)

## 接口

### 左侧

![左侧](../../assets/flight_controller/airlink/airlink-interfaces-left.jpg)

- **左侧接口：**

  - 带电压和电流监控的电源输入
  - AI任务计算机micro SD卡
  - 飞控micro SD卡
  - AI任务计算机USB Type-C
  - PPM输入、SBUS输出、RSSI监控

- **POWER - JST GH SM10B-GHS-TB**

  | 针脚编号 | 针脚名称     | 方向 | 电压   | 功能                   |
  | -------- | ------------ | ---- | ------ | ------------------------ |
  | 1        | 12V          | IN   | +12V   | 主电源输入             |
  | 2        | 12V          | IN   | +12V   | 主电源输入             |
  | 3        | 12V          | IN   | +12V   | 主电源输入             |
  | 4        | BAT_CURRENT  | IN   | +3.3V  | 电池电流监控           |
  | 5        | BAT_VOLTAGE  | IN   | +3.3V  | 电池电压监控           |
  | 6        | 3V3          | OUT  | +3.3V  | 3.3V输出               |
  | 7        | PWR_KEY      | IN   | +3.3V  | 电源键输入             |
  | 8        | GND          | 接地 |
  | 9        | GND          | 接地 |
  | 10       | GND          | 接地 |

- **CPU SD卡 - microSD**
- **CPU USB - USB Type C**
- **RC连接器 - JST GH SM06B-GHS-TB**

  | 针脚编号 | 针脚名称   | 方向 | 电压   | 功能       |
  | -------- | ---------- | ---- | ------ | ---------- |
  | 1        | 5V         | OUT  | +5V    | 5V输出     |
  | 2        | PPM_IN     | IN   | +3.3V  | PPM输入    |
  | 3        | RSSI_IN    | IN   | +3.3V  | RSSI输入   |
  | 4        | FAN_OUT    | OUT  | +5V    | 风扇输出   |
  | 5        | SBUS_OUT   | OUT  | +3.3V  | SBUS输出   |
  | 6        | GND        | 接地 |

* **FMU SD卡 - microSD**

### 右侧

![右侧](../../assets/flight_controller/airlink/airlink-interfaces-right.jpg)

- **右侧接口：**

  - 带电源输出的以太网端口
  - 通信端口
  - 第二个GPS端口
  - 备用I2C/UART端口
  - 飞控USB Type-C
  - micro SIM卡
  - HDMI输入端口（载荷相机）

- **ETHERNET - JST GH SM08B-GHS-TB**

  | 针脚编号 | 针脚名称   | 方向 | 电压   | 功能                   |
  | -------- | ---------- | ---- | ------ | ------------------------ |
  | 1        | 5V         | OUT  | +5V    | 5V输出                 |
  | 2        | TXD        | OUT  | +3.3V  | 以太网TXD              |
  | 3        | RXD        | IN   | +3.3V  | 以太网RXD              |
  | 4        | GND        | 接地 |
  | 5        | 5V         | OUT  | +5V    | 5V输出                 |
  | 6        | TXD2       | OUT  | +3.3V  | 第二路以太网TXD        |
  | 7        | RXD2       | IN   | +3.3V  | 第二路以太网RXD        |
  | 8        | GND        | 接地 |

- **通信端口 - JST GH SM06B-GHS-TB**

  | 针脚编号 | 针脚名称 | 方向 | 电压   | 功能       |
  | -------- | -------- | ---- | ------ | ---------- |
  | 1        | 5V       | OUT  | +5V    | 5V输出     |
  | 2        | TX       | OUT  | +3.3V  | 串口TX     |
  | 3        | RX       | IN   | +3.3V  | 串口RX     |
  | 4        | CTS      | IN   | +3.3V  | 清除发送   |
  | 5        | RTS      | OUT  | +3.3V  | 请求发送   |
  | 6        | GND      | 接地 |

- **FMU备用端口 - JST GH SM06B-GHS-TB**

  | 针脚编号 | 针脚名称 | 方向 | 电压   | 功能       |
  | -------- | -------- | ---- | ------ | ---------- |
  | 1        | 5V       | OUT  | +5V    | 5V输出     |
  | 2        | SCL      | I/O  | +3.3V  | I2C时钟   |
  | 3        | SDA      | I/O  | +3.3V  | I2C数据   |
  | 4        | UART_TX  | OUT  | +3.3V  | 串口TX     |
  | 5        | UART_RX  | IN   | +3.3V  | 串口RX     |
  | 6        | GND      | 接地 |

### 前侧

![前侧](../../assets/flight_controller/airlink/airlink-interfaces-front.jpg)

- **前侧接口：**

  - 16路PWM输出
  - 安全按钮和LED
  - 蜂鸣器输出
  - 3.3V电源

- **PWM输出 - JST GH SM16B-GHS-TB**

  | 针脚编号 | 针脚名称 | 功能             |
  | -------- | -------- | ---------------- |
  | 1-16     | PWM      | PWM信号输出      |

- **安全接口 - JST GH SM04B-GHS-TB**

  | 针脚编号 | 针脚名称    | 功能         |
  | -------- | ----------- | ------------ |
  | 1        | 3.3V        | 3.3V电源     |
  | 2        | Safety_BTN  | 安全按钮输入 |
  | 3        | Safety_LED  | 安全LED输出  |
  | 4        | GND         | 接地         |

- **蜂鸣器输出 - JST GH SM02B-GHS-TB**

  | 针脚编号 | 针脚名称 | 功能       |
  | -------- | -------- | ---------- |
  | 1        | 5V       | 5V电源     |
  | 2        | Buzzer   | 蜂鸣器输出 |

- **3.3V电源 - JST GH SM02B-GHS-TB**

  | 针脚编号 | 针脚名称 | 功能       |
  | -------- | -------- | ---------- |
  | 1        | 3.3V     | 3.3V电源   |
  | 2        | GND      | 接地       |

### 顶部

![顶部](../../assets/flight_controller/airlink/airlink-interfaces-top.jpg)

- **顶部接口：**

  - I2C总线
  - UART接口
  - 电源管理接口

- **I2C总线 - JST GH SM04B-GHS-TB**

  | 针脚编号 | 针脚名称 | 功能       |
  | -------- | -------- | ---------- |
  | 1        | 3.3V     | 3.3V电源   |
  | 2        | SCL      | I2C时钟   |
  | 3        | SDA      | I2C数据   |
  | 4        | GND      | 接地       |

- **UART接口 - JST GH SM04B-GHS-TB**

  | 针脚编号 | 针脚名称 | 功能       |
  | -------- | -------- | ---------- |
  | 1        | 3.3V     | 3.3V电源   |
  | 2        | TX       | 串口TX     |
  | 3        | RX       | 串口RX     |
  | 4        | GND      | 接地       |

- **电源管理接口 - JST GH SM04B-GHS-TB**

  | 针脚编号 | 针脚名称 | 功能       |
  | -------- | -------- | ---------- |
  | 1        | 5V       | 5V电源     |
  | 2        | VBUS     | USB供电    |
  | 3        | GND      | 接地       |
  | 4        | PGOOD    | 电源正常   |# 串口映射

AIRLink 具有大量内部和外部串口：

| 串口     | UART    | 功能                                                |
| -------- | ------- | --------------------------------------------------- |
| 串口 0   | USB     | 控制台                                              |
| 串口 1   | UART 7  | 遥测 1                                              |
| 串口 2   | UART 5  | 遥测 2（与任务计算机内部连接）                      |
| 串口 3   | USART 1 | GPS 1                                               |
| 串口 4   | UART 8  | GPS 2                                               |
| 串口 5   | USART 3 | 调试控制台（内部连接器）                            |
| 串口 6   | USART 2 | 遥测 3                                              |
| 串口 7   | UART 4  | 外部 UART                                           |## RC输入

RC输入配置在SBUS引脚上，并通过内部反相器连接到IO MCU。  
对于PPM接收器，请使用位于设备左侧的RC连接器PPM引脚。

![RC输入](../../assets/flight_controller/airlink/airlink-rc-input.jpg)

## 输出

AIRLink 有 16 个 PWM 输出。主输出 1-8 连接到 IO 微控制器。辅助输出 1-8 连接到 FMU。

| 输出    | 定时器    | 通道      |
| ------- | -------- | --------- |
| 辅助 1  | 定时器 1  | 通道 4    |
| 辅助 2  | 定时器 1  | 通道 3    |
| 辅助 3  | 定时器 1  | 通道 2    |
| 辅助 4  | 定时器 1  | 通道 1    |
| 辅助 5  | 定时器 4  | 通道 2    |
| 辅助 6  | 定时器 4  | 通道 3    |
| 辅助 7  | 定时器 12 | 通道 1    |
| 辅助 8  | 定时器 12 | 通道 2    |

[DShot](../peripherals/dshot.md) 可用于前四个辅助引脚。

## 构建固件

:::tip
大多数用户无需构建此固件！
它已预先构建，并在连接适当硬件时由_QGroundControl_自动安装。
:::

要[构建PX4](../dev_setup/building_px4.md)针对此目标：

```
make sky-drones_smartap-airlink
```

## 外设

- [SmartAP GPS](../gps_compass/gps_smartap.md) - 带指南针、气压传感器和RGB LED的GPS模块  
- [SmartAP PDB](../power_module/sky-drones_smartap-pdb.md) - 电源分配板## 参考设计

![参考设计](../../assets/flight_controller/airlink/airlink-reference-design.png)

AIRLink CAD模型可在此处获取 [here](https://docs.sky-drones.com/airlink/cad-model)

AIRLink 参考设计可应要求提供  
通过 [Sky-Drones 联系页面](https://sky-drones.com/contact-us) 联系我们## 更多信息

有关设置和使用 AIRLink 系统的更多信息和操作指南，请参阅 [AIRLink 文档](https://docs.sky-drones.com/airlink/)。

如需技术支持、帮助和定制服务，请通过 [Sky-Drones 联系页面](https://sky-drones.com/contact-us) 联系我们。

更多信息请访问 [www.sky-drones.com](https5://sky-drones.com)。

常见问题解答请参阅 [FAQ](https://docs.sky-drones.com/airlink/faq)。

## 有用链接

- [AIRLink 产品页面](https://sky-drones.com/airlink)
- [AIRLink 文档](https://docs.sky-drones.com/avionics/airlink)
- [AIRLink 数据手册](https://3182378893-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F-MTMlWysgDtJq8Hid1v7%2Fuploads%2F8AiuNNSwLYnZSscj7uIV%2FAIRLink-Datasheet.pdf?alt=media&token=cbf0c4bf-9ab1-40c5-a0af-c6babdddb690)
- [购买 AIRLink Enterprise 4G](https://sky-drones.com/sets/airlink-enterprise-set.html)
- [购买 AIRLink Enterprise 5G](https://sky-drones.com/sets/airlink-5g-enterprise-set.html)
- [购买 AIRLink Core 4G](https://sky-drones.com/autopilots/airlink-core.html)
- [购买 AIRLink Core 5G](https://sky-drones.com/store/airlink-5g-core.html)