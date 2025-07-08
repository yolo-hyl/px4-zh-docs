# LOCOSYS Hawk R1 RTK GPS

<Badge type="tip" text="PX4 v1.13" />

[LOCOSYS Hawk R1](https://www.locosystech.com/en/product/hawk-r1.html) 是一款双频 [RTK GPS模块](../gps_compass/rtk_gps.md)，专为与Pixhawk兼容而设计。

当安装在机体上时，该模块可作为RTK GPS漫游器使用。

接收器能够同时跟踪所有全球民用导航系统，包括GPS、GLONASS、GALILEO、BEIDOU和QZSS。
它同时获取L1和L5信号，提供厘米级RTK定位精度。

内置的轻量螺旋天线增强了RTK定位的稳定性。
快速首次定位时间、RTK收敛、卓越的灵敏度和低功耗使其成为基于Pixhawk平台无人机的更好选择。

::: info
此模块没有指南针。
如需带指南针的等效GPS模块，请尝试：[LOCOSYS Hawk R2](../gps_compass/rtk_gps_locosys_r2.md)。
:::

## 主要特性

- 同时接收L1和L5频段信号
- 支持GPS、GLONASS、BEIDOU、GALILEO、QZSS
- 支持SBAS（WAAS、EGNOS、MSAS、GAGAN）
- 支持135通道GNSS
- 在低信号水平下快速首次定位时间（TTFF）
- 提供免费混合星历预测以实现更快的冷启动
- 默认5Hz，最高10Hz更新率（SBAS仅支持5Hz）
- 内置超级电容以保留系统数据，实现快速卫星捕获

![LOCOSYS Hawk R1](../../assets/hardware/gps/locosys_hawk_a1/locosys_hawk_a1_gps.png)

## 购买渠道

- [LOCOSYS Hawk R1](https://www.locosystech.com/en/product/hawk-r1.html)

## 套件内容

RTK GPS套件包括：

- 1个GPS模块
- 1个螺旋天线
- 1个6针JST-GH连接器

## 配置

通过_QGroundControl_ 在PX4上设置和使用RTK基本为即插即用（更多信息请参见[RTK GPS](../gps_compass/rtk_gps.md)）。
将Hawk R1连接到兼容Pixhawk板上的`GPS2`端口（推荐，也可使用其他未使用的UART端口）。

对于机体，应将参数[SER_GPS2_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD)设置为230400 8N1，以确保PX4配置正确的波特率。

## 接线与连接

Hawk R1 RTK GPS配有6针JST-GH连接器，可插入Pixhawk自动驾驶仪。

### 引脚分配

LOCOSYS GPS引脚分配如下：

| 引脚 | Hawk R1 GPS |
| --- | ----------- |
| 1   | VCC_5V      |
| 2   | GPS_RX      |
| 3   | GPS_TX      |
| 4   | Null        |
| 5   | Null        |
| 6   | Null        |
| 7   | Null        |
| 8   | Null        |
| 9   | GND         |

## 状态LED

| 颜色 | 名称            | 描述                        |
| ----- | --------------- | ---------------------------------- |
| 绿色 | TX指示灯        | GNSS数据传输             |
| 红色 | 电源指示灯      | 电源                              |
| 蓝色 | PPS             | 精确定位服务激活 |

![Hawk A1 LED](../../assets/hardware/gps/locosys_hawk_a1/locosys_hawk_a1_leds.png)

## 规格参数

- 频率
  - GPS/QZSS: L1 C/A, L5C
  - GLONASS: L1OF
  - BEIDOU: B1I, B2a
  - GALILEO: E1, E5a
- 135通道支持
- 最高10Hz更新率（默认5Hz）
- 获取时间
  - 热启动（开阔天空）2秒内
  - 冷启动（开阔天空）无AGPS时28秒
- PPS脉宽100ms，1.8Vdc
- 外部有源螺旋天线
  - SMA连接器
- UBlox协议支持
  - U5Hz:UBX-NAV-PVT,UBX-NAV-DOP
  - 1Hz: UBX-NAV-TIMEGPS
- 连接性：
  - 6针JST-GH UART/I2C（Pixhawk兼容）
- 电源：
  - 直流供电电压3.3V ~ 5.0V输入
  - 功耗<1W

## 更多信息

更多信息请访问[LOCOSYS Hawk R1](https://www.locosystech.com/en/product/hawk-r1.html)