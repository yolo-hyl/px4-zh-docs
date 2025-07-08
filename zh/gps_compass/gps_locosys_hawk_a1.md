# LOCOSYS Hawk A1 GPS/GNSS

[LOCOSYS HAWK A1 GPS/GNSS接收机](https://www.locosystech.com/en/product/hawk-a1-LU23031-V2.html) 是一款兼容PX4的双频多星座GNSS/GPS接收机。

主要功能包括：

- 同时接收L1和L5频段信号
- 支持GPS、GLONASS、BEIDOU、GALILEO、QZSS
- 兼容SBAS（WAAS、EGNOS、MSAS、GAGAN）
- 支持135通道GNSS
- 低信号强度下快速获取首次定位（TTFF）
- 提供免费混合星历预测以实现更快冷启动
- 默认5Hz更新率，最高可达10Hz（SBAS仅支持5Hz）
- 内置超级电容，用于存储系统数据以实现快速卫星捕获
- 三色LED指示灯（电源/PPS/数据传输）

![Hawk A1](../../assets/hardware/gps/locosys_hawk_a1/locosys_hawk_a1_gps.png)

## 购买渠道

- [LOCOSYS](https://www.locosystech.com/en/product/hawk-a1-LU23031-V2.html)（台湾）

## 配置

Hawk A1可作为主（主要）或次级GPS系统使用。
根据使用场景需按以下参数设置PX4：

### 主GNSS

将Hawk A1用作主GPS设备时：

| 参数                                                                  | 值                                          | 描述                                                                             |
| -------------------------------------------------------------------------- | ---------------------------------------------- | --------------------------------------------------------------------------------------- |
| [GPS_1_CONFIG](../advanced_config/parameter_reference.md#GPS_1_CONFIG)     | 102（Telem 2或可用串口） | 配置主GPS端口                                                                 |
| [GPS_1_PROTOCOL](../advanced_config/parameter_reference.md#GPS_1_PROTOCOL) | 1（u-blox）                                     | 配置GPS协议                                                                  |
| [SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD)   | 230400                                         | 配置串口波特率（例如GPS连接到TELEM2） |

### 次级GNSS

将Hawk A1用作辅助GPS设备（与主GPS配合使用）时：

| 参数                                                                  | 值                                          | 描述                                                                           |
| -------------------------------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------------------------------- |
| [GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG)     | 102（Telem 2或可用串口） | 配置主GPS端口                                                               |
| [GPS_2_PROTOCOL](../advanced_config/parameter_reference.md#GPS_2_PROTOCOL) | 1（u-blox）                                     | 配置GPS协议                                                                |
| [SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD)   | 230400                                         | 配置串口波特率（例如GPS连接到TELEM2） |

## 接线与连接

Locosys GPS配备6针JST-GH Pixhawk标准连接器，可直接插入Pixhawk FMUv5的GPS1 UART端口或GPS2 UART端口。

![GPS线缆](../../assets/hardware/gps/locosys_hawk_a1/locosys_gps_cable.png)

### 引脚分配

以下是LOCOSYS GPS的引脚分配，可用于修改其他飞控板的连接器：

| 引脚 | Locosys GPS | 引脚 | Pixhawk GPS 2 |
| --- | ----------- | --- | ------------- |
| 1   | VCC_5V      | 1   | VCC           |
| 2   | GPS_RX      | 2   | GPS_TX        |
| 3   | GPS_TX      | 3   | GPS_RX        |
| 4   | NC          | 4   | SDA           |
| 5   | NC          | 5   | SCL           |
| 6   | GND         | 6   | GND           |

## 状态LED

| 颜色 | 名称            | 描述                        |
| ----- | --------------- | ---------------------------------- |
| 绿色 | 数据传输指示灯    | GNSS数据传输             |
| 红色 | 电源指示灯 | 电源                              |
| 蓝色  | PPS             | 精密定位服务激活 |

![Hawk A1 LED](../../assets/hardware/gps/locosys_hawk_a1/locosys_hawk_a1_leds.png)

## 规格参数

- **接收机类型：** 135通道LOCOSYS MC-1612-V2b引擎，支持GPS/QZSS L1 C/A、L5C、GLONASS L1OF、BeiDou B1I、B2a、Galileo E1、E5a、SBAS L1 C/A（WAAS、EGNOS、MSAS、GAGAN）
- **导航更新率：** 默认5Hz，最高10Hz
- **定位精度：** 3D定位
- **首次定位时间（TTFF）：**
  - **冷启动：** 28秒
  - **辅助启动：** EASY
- **灵敏度：**
  - **跟踪与导航：** -165 dBm
- **辅助GNSS：** EASY DGPS
- **振荡器：** 26MHz TCXO
- **RTC晶振：** 32.768KHz
- **可用天线：** L1+L5多频段天线
- **信号完整性：** L1+L5 GPS GLONASS GALILEO BEIDOU QZSS SBAS
- **协议与接口：**
  - **UART/I2C：** JST_GH主接口，内部切换。

## 详细信息

- [LOCOSYS GPS用户手册](https://www.locosystech.com/Templates/att/LU23031-V2%20datasheet_v0.2.pdf?lng=en)