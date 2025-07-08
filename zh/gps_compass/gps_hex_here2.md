# HEX/ProfiCNC Here2 GPS（已停产）

::: warning
此设备已被 [Cube Here 3](https://www.cubepilot.com/#/here/here3) 取代
:::

[Here2 GPS接收器](http://www.proficnc.com/all-products/152-gps-module.html) 是HEX推出的Here GPS模块的升级版本。

主要特性包括：

- 同时接收最多3种GNSS（GPS、伽利略、GLONASS、北斗）
- 行业领先的-167 dBm导航灵敏度
- 安全性与完整性保护
- 支持所有卫星增强系统
- 先进的干扰和欺骗检测

<img src="../../assets/hardware/gps/here2_gps_module.jpg" />

## 购买渠道

- [ProfiCNC](http://www.proficnc.com/all-products/152-gps-module.html)（澳大利亚）
- [其他经销商](http://www.proficnc.com/stores)

## 配置

在PX4上的设置和使用基本为即插即用。

::: info

- 如果GPS_未被检测到_，请[更新Here2固件](https://docs.cubepilot.org/user-guides/here-2/updating-here-2-firmware)。
- 如果GPS被检测到但无法工作，请尝试[分配节点uavcan ID](https://docs.cubepilot.org/user-guides/here-2/here-2-can-mode-instruction)中描述的流程。
  :::

## 接线与连接

Here2 GPS配有8针连接器，可直接插入[Pixhawk 2](http://www.hex.aero/wp-content/uploads/2016/07/DRS_Pixhawk-2-17th-march-2016.pdf)的GPS UART端口。

Pixhawk 3 Pro和Pixracer具有6针GPS端口连接器。对于这些控制器，可以修改GPS线缆（如下所示），移除第6和第7针。

<img src="../../assets/hardware/gps/rtk_here_plug_gps_to_6pin_connector.jpg" width="500px" />

第6和第7针用于安全按钮 - 如需使用可连接。

### 引脚分配

Here2 GPS的引脚分配如下。可用于帮助修改其他飞控板的连接器。

| 引脚 | Here2 GPS  | 引脚 | Pixhawk 3 Pro GPS |
| --- | ---------- | --- | ----------------- |
| 1   | VCC_5V     | 1   | VCC               |
| 2   | GPS_RX     | 2   | GPS_TX            |
| 3   | GPS_TX     | 3   | GPS_RX            |
| 4   | SCL        | 4   | SCL               |
| 5   | SDA        | 5   | SDA               |
| 6   | BUTTON     | -   | -                 |
| 7   | BUTTON_LED | -   | -                 |
| 8   | GND        | 6   | GND               |

## 技术规格

- **处理器:** STM32F302
- **传感器**
  - **指南针、陀螺仪、加速度计:** ICM20948
  - **气压计:** MS5611
- **接收器类型:** 72通道u-blox M8N引擎，GPS/QZSS L2 C/A，GLONASS L10F，北斗B11，伽利略E1B/C，SBAS L1 C/A: WAAS，EGNOS，MSAS，GAGAN
- **导航更新率:** 最大10 Hz
- **定位精度:** 3D Fix
- **首次定位时间:**
  - **冷启动:** 26秒
  - **辅助启动:** 2秒
  - **重新捕获:** 1秒
- **灵敏度:**
  - **跟踪与导航:** -167 dBm
  - **热启动:** -148 dBm
  - **冷启动:** -157 dBm
- **辅助GNSS**
  - AssistNow GNSS Online
  - AssistNow GNSS Offline（最多35天）
  - AssistNow Autonomous（最多6天）
  - OMA SUPL& 3GPP合规
- **振荡器:** TCXO（NEO-8MN/Q）
- **RTC晶振:** 内置
- **ROM:** Flash（NEO-8MN）
- **可用天线:** 活动天线 & 被动天线
- **信号完整性:** 带SHA 256签名功能
- **协议与接口:**
  - **UART/I2C/CAN:** JST_GH 主接口，内部切换
  - **STM32 主编程接口:** JST_SUR