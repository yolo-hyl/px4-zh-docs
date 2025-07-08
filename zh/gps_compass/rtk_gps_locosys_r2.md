# LOCOSYS Hawk R2 RTK-指南针 GPS

<Badge type="tip" text="PX4 v1.13" />

[LOCOSYS Hawk R2](https://www.locosystech.com/en/product/hawk-r2.html) 是一款专为 Pixhawk 兼容的双频 [RTK GPS 模块](../gps_compass/rtk_gps.md) 接收器。与 [LOCOSYS Hawk R1](rtk_gps_locosys_r1.md) 相比，该模块唯一区别在于 Hawk R2 内置了磁力计。

当安装在机体上时，该模块可作为 RTK GPS 流动站使用。

接收器可同时跟踪所有全球民用导航系统，包括 GPS、GLONASS、GALILEO、BEIDOU 和 QZSS。  
它同时接收 L1 和 L5 信号，并提供厘米级 RTK 定位精度。

内置轻型螺旋天线可增强 RTK 定位稳定性。此外，它还配备指南针。  
快速首次定位时间、RTK 收敛性、卓越的灵敏度和低功耗使其成为基于 Pixhawk 平台无人机的更优选择。

## 主要特性

- 厘米级 RTK 高精度定位 + 集成三轴磁力计  
- 同时接收 L1 和 L5 频段信号  
- 支持 GPS、GLONASS、BEIDOU、GALILEO、QZSS  
- 支持 SBAS（WAAS、EGNOS、MSAS、GAGAN）  
- 支持 135 通道 GNSS  
- 低信号水平下快速 TTFF  
- 免费混合星历预测，实现更快冷启动  
- 默认 5Hz，最高 10Hz 更新频率（SBAS 仅支持 5Hz）  
- 内置超级电容，保留系统数据以实现快速卫星捕获  
- 内置三轴指南针功能  
- 三个 LED 指示灯（电源、PPS 和数据传输）

![LOCOSYS Hawk R2](../../assets/hardware/gps/locosys_hawk_a1/locosys_hawk_a1_gps.png)

## 购买渠道

- [LOCOSYS Hawk R2](https://www.locosystech.com/en/product/hawk-r2.html)

## 套件内容

RTK GPS 套件包含：

- 1 个 GPS 模块  
- 1 个螺旋天线  
- 1 条 6 针 JST 接头线缆  

## 线路连接与接线

Hawk R2 RTK GPS 配备 6 针 JST 接口，可直接插入符合 Pixhawk 标准的自动驾驶仪 GPS2 端口。

![LOCOSYS Hawk R2 连接飞控的线缆](../../assets/hardware/gps/locosys_hawk_r2/locosys_hawk_r2_jst6_cable.jpg)

它也可用于其他 UART 端口，但需要连接并配置相应端口。  
如果需要制作自定义线缆，下表提供引脚定义：

### 引脚定义

LOCOSYS GPS 引脚定义如下：

| 引脚 | Hawk R2 GPS |
| --- | ----------- |
| 1   | VCC_5V      |
| 2   | GPS_RX      |
| 3   | GPS_TX      |
| 4   | GNSS_PPS    |
| 5   | Null        |
| 6   | Null        |
| 7   | I2C_CLK     |
| 8   | I2C_DAT     |
| 9   | GND         |

## PX4 配置

将 Hawk R2 连接到兼容 Pixhawk 主板的 `GPS2` 端口后，通过 _QGroundControl_ 在 PX4 上进行 RTK 设置和使用基本为即插即用。  
更多信息请参见：[RTK GPS](../gps_compass/rtk_gps.md#positioning-setup-configuration)。

还需要将使用的串口配置为正确的波特率。  
如果使用 GPS2，将参数 [SER_GPS2_BAUD](../advanced_config/parameter_reference.md#SER_GPS2_BAUD) 设置为 230400 8N1。

指南针仅需进行常规的 [指南针校准](../config/compass.md)。

## 状态 LED

| 颜色 | 名称           | 描述                     |
| ---- | -------------- | ------------------------ |
| 绿色 | TX 指示灯      | GNSS 数据传输            |
| 红色 | 电源指示灯     | 电源                     |
| 蓝色 | PPS            | 精确定位服务激活         |

![Hawk A1 LED](../../assets/hardware/gps/locosys_hawk_a1/locosys_hawk_a1_leds.png)

## 规格参数

- 频率  
  - GPS/QZSS: L1 C/A, L5C  
  - GLONASS: L1OF  
  - BEIDOU: B1I, B2a  
  - GALILEO: E1, E5a  
- 135 通道支持  
- 最高 10 Hz 更新频率（默认 5Hz）  
- 启动时间  
  - 热启动（开阔天空）2 秒内  
  - 冷启动（开阔天空）无 AGPS 时 28 秒内  
- PPS 100ms 脉宽，1.8Vdc  
- 外部有源螺旋天线  
  - SMA 接口  
- Ublox 协议支持  
  - U5Hz: UBX-NAV-PVT, UBX-NAV-DOP  
  - 1Hz: UBX-NAV-TIMEGPS  
- 连接性：  
  - 6 针 JST-GH UART/I2C（Pixhawk 兼容）  
- 电源：  
  - 直流输入电压 3.3V ~ 5.0V  
  - 功耗 <1W  

## 更多信息

更多信息请访问 [LOCOSYS Hawk R2](https://www.locosystech.com/en/product/hawk-r2.html)