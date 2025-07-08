# Trimble MB-Two

[Trimble MB-Two RTK GPS接收器](https://www.trimble.com/Precision-GNSS/MB-Two-Board.aspx) 是一款高端双频[RTK GPS模块](../gps_compass/rtk_gps.md)，可配置为基准站或流动站。

除了提供精确的位置信息，MB-Two还能估算航向角（支持双天线配置）。这在指南针无法提供可靠航向信息时非常有用，例如靠近金属结构物飞行时。

![MB-Two 主图](../../assets/hardware/gps/rtk_trimble_two_gnss_hero.jpg)

## 必需固件选项

购买设备时需要选择以下固件选项：

- \[X\] \[2\] \[N\] \[G\] \[W\] \[Y\] \[J\]：20Hz位置更新和RTK支持，水平1cm/垂直2cm定位精度
- \[L\] LBAND
- \[D\] DUO - 双天线航向
- \[B\] BEIDOU + \[O\] GALILEO（如需）

## 天线与线缆

Trimble MB-Two需要两个双频（L1/L2）天线。  
一个优质示例是[Maxtenna M1227HCT-A2-SMA](http://www.maxtena.com/products/helicore/m1227hct-a2-sma/)（可从[Farnell](https://uk.farnell.com/maxtena/m1227hct-a2-sma/antenna-1-217-1-25-1-565-1-61ghz/dp/2484959)购买）。

设备天线接口类型为MMCX。  
适用于上述SMA接口天线的线缆：

- [30厘米版本](https://www.digikey.com/products/en?mpart=415-0073-012&v=24)
- [45厘米版本](https://www.digikey.com/products/en?mpart=415-0073-018&v=24)

## 线路连接

Trimble MB-Two通过飞控器的UART（GPS端口）进行数据连接。  
模块供电需要独立的3.3V电源（最大功耗360mA）。

::: info
该模块不能由Pixhawk供电。
:::

28针接口的引脚编号如下所示：

![MB-Two 引脚定义](../../assets/hardware/gps/rtk_trimble_two_gnss_pinouts.jpg)

| 引脚 | 名称       | 描述                                           |
| ---- | ---------- | -------------------------------------------- |
| 6    | Vcc 3.3V   | 电源                                           |
| 14   | GND        | 连接飞控的电源和接地                           |
| 15   | TXD1       | 连接飞控的RX                                   |
| 16   | RXD1       | 连接飞控的TX                                   |

## 配置

首先将GPS协议设置为Trimble（[GPS_x_PROTOCOL=3](../advanced_config/parameter_reference.md#GPS_1_PROTOCOL)）。

对于航向估算，两个天线需处于同一水平面且间距至少30cm。  
天线朝向可通过[GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET)参数配置。

::: info
`GPS_YAW_OFFSET` 是基线（两个GPS天线之间的连线）与机体x轴（前后轴，详情见[此处](../config/flight_controller_orientation.md#calculating-orientation)）形成的夹角。
:::

使用[GPS_1_CONFIG](../advanced_config/parameter_reference.md#GPS_1_CONFIG)配置Trimple运行的串口，并通过[SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD)将波特率设置为115200。

要激活姿态估算的航向融合，将[EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)参数设置为启用"双天线航向"。

::: info
另请参见：[GPS > 配置 > GPS作为偏航/航向源](../gps_compass/index.md#configuring-gps-as-yaw-heading-source)
:::