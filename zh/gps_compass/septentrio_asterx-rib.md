# Septentrio AsteRx-m3 Pro 与 RIB 板

Septentrio 是全球领先的 OEM GPS/GNSS 接收器供应商。Septentrio OEM 接收器以小巧轻便的外形，为严苛的工业应用提供精确可靠的位置信息。有多款双天线接收器选项可将 GPS 信息融合到航向角中（也可确定其他姿态信息，但 PX4 仅融合航向角）。

AsteRx-m3 Pro 系列的惯性传感器集成方案提供完整的姿态解决方案（航向角、俯仰角和滚转角），并能与精确的定位同步。

![Septentrio 机器人接口板](../../assets/hardware/gps/septentrio_sbf/asterx_m3_and_rib_board.png)

机器人接口板配合 Septentrio GNSS 接收器板提供 USB、以太网等通用接口，以及用于快速原型设计、产品评估或高效集成的机载日志功能。其特性包括：

- 超低功耗信用卡大小的电路板
- 易于集成到任何系统中
- 采用真多星座多频段 GNSS 技术，RTK 性能行业领先
- 先进抗干扰（AIM+）抗干扰和抗欺骗技术
- 抗振动和抗冲击能力强
- 44针 I/O 接口，适用于 Pixhawk 等飞控系统
- 机载日志功能
- USB Micro-B 接口
- 尺寸：71.53 x 47.5 x 18.15 mm
- 重量：50g

## 购买

所有 AsteRx 接收器和机器人接口板均可通过 Septentrio 官网购买：

- [AsteRx-m3 Pro](https://web.septentrio.com/l/858493/2022-04-19/xgrrz)
- [AsteRx-m3 Pro+](https://web.septentrio.com/l/858493/2022-04-19/xgrs3)

其他 PX4 支持的 Septentrio 设备：

- [mosaic-go 评估套件](../gps_compass/septentrio_mosaic-go.md)

## 接口

![Septentrio 机器人接口板 Fritzing 示意图](../../assets/hardware/gps/septentrio_sbf/rib.png)

### USB

_连接器类型：micro-USB B型。_

micro USB B型连接器可连接到电脑，通过 USB 接口为接收器供电并与其通信。

### 44针接头

_连接器类型：SAMTEC TMM-122-03-S-D，2mm 间距。_

44针接头可用于连接多个 GPIO 设备。具体引脚定义请参考 [硬件手册](https://web.septentrio.com/l/858493/2022-04-19/xgrsw)。

### LED

LED 引脚可用于监控接收器状态。可驱动外部 LED（最大驱动电流 10mA）。假设当对应引脚电平为高时 LED 亮起。通用 LED（GPLED 引脚）通过 setLEDMode 命令配置。

### 日志按钮接头

在 LOG Button 接头上跳线（.100" 垂直接头）等效于按下"日志按钮"。接口板会自动处理消抖。

### PPS/事件接头

_连接器类型：SAMTEC TMM-103-03-G-D，2mm 间距。_

micro USB 连接器旁边的 6针 2mm 接头暴露第一个 PPS 信号。

### 电源选项

当 USB 线缆连接到 USB Micro-B 接口时，接口板通过 USB 连接器由电脑供电。也可通过 44针接头的 `PWR_IN` 引脚供电。`PWR_IN` 引脚的电压范围为 4.5V 至 30V。两种电源可同时使用，板载二极管防止短路。接口板为 AsteRx-m3 OEM 接收器提供 3V3 电源，并为 AsteRx-m3 OEM 的 `VANT` 引脚提供 5V 直流电压。

## PX4 配置

PX4 配置请参考 [Septentrio GNSS 接收器](../gps_compass/septentrio.md)。

## 硬件设置

![Septentrio 机器人接口板接线图](../../assets/hardware/gps/septentrio_sbf/rib_wiring.png)

1. 确保接收器至少获得 3.3V 供电。可通过 micro USB 连接器或 44针线缆上的 "PWR & GND" 开放电源实现。
2. 将一个或两个 GNSS 天线连接到 AsteRx-m3 Pro 板的外部天线端口。
3. 将 44针线缆连接到 RIB 上的 AsteRx-m3 Pro 板，并将 10针 JST 连接器连接到 Pixhawk 4 的 _GPS MODULE_ 接口，如上图所示。

::: info
PX4 会确保 GNSS 模块自动配置。但是，如果使用双天线配置，需要在 web 应用中尽可能精确地设置布局。
:::

### 双天线

姿态（航向角/俯仰角）可通过主天线和 aux1 GNSS 天线之间基线的方向计算得出。

![多天线姿态确定设置](../../assets/hardware/gps/septentrio_sbf/multi-antenna_attitude_setup.png)

要启用多天线姿态确定，请按以下步骤操作：

1. 使用长度大致相同的线缆将两个天线安装在机体上。默认天线配置如图所示，将天线沿机体纵向轴线排列，主天线位于 `AUX1` 后方。为获得最佳精度，应尽可能增大天线间距，并避免天线 ARP 之间出现显著高度差。
2. 实际应用中，两个天线 ARP 可能在机体中不在同一高度，或者主-aux1 基线可能不完全平行或垂直于机体纵向轴线。这会导致计算姿态角出现偏差。这些偏差可通过 Septentrio 驱动在 PX4 中提供的航向参数进行补偿。

::: info
为获得最佳航向结果，两个天线之间的距离至少应为 30cm / 11.8 英寸（建议更大）。
:::

### 提示

::: tip
建议在安装前仔细阅读 [硬件手册](https://web.septentrio.com/l/858493/2022-04-19/xgrsz) 以确保正确配置。
:::