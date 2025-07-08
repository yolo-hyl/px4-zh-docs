# Holybro H-RTK M8P GNSS（已停产）

:::warning
该GNSS模块已停产，目前无法在市场上购买。
:::

[Holybro H-RTK M8P GNSS](https://holybro.com/collections/standard-h-rtk-series/products/h-rtk-m8p-gnss-series) 是一个面向大众市场的[RTK GNSS模块](../gps_compass/rtk_gps.md)系列。该产品线与[H-RTK M9P](../gps_compass/rtk_gps_holybro_h-rtk-f9p.md)系列类似，但使用了更小、更轻且成本更低的M8P u-blox RTK GNSS模块（仍能提供比前代产品优越得多的定位精度）。

Holybro H-RTK M8P系列提供三种不同型号，每种型号采用不同天线设计以满足不同需求。详情请参考[规格和型号比较部分](#specification-and-model-comparison)。

通过RTK技术，PX4可获得厘米级定位精度，这比普通GPS的精度高出许多。

![h-rtk_rover](../../assets/hardware/gps/rtk_holybro_h-rtk-m8p_all_label.jpg)

## 购买渠道

- [H-RTK M8P（GPS RTK安装支架）](https://holybro.com/products/vertical-mount-for-h-rtk-helical)

## 配置

通过_QGroundControl_在PX4上配置和使用RTK功能基本属于即插即用（更多信息请参见[RTK GPS](../gps_compass/rtk_gps.md)）。

## 接线与连接

所有H-RTK GNSS型号均配备GH 10针连接器/电缆，与[Pixhawk 4](../flight_controller/pixhawk4.md)兼容。

::: info
为连接其他飞控板，可能需要修改线缆/连接器（请参见下方[引脚映射](#pin_map)）。
:::

<a id="pin_map"></a>

## 引脚映射

![h-rtk_rover_pinmap](../../assets/hardware/gps/rtk_holybro_h-rtk-m8p_pinmap.jpg)

## 规格和型号比较

![h-rtk_spec](../../assets/hardware/gps/rtk_holybro_h-rtk-m8p_spec.png)

## GPS配件

[GPS配件（Holybro官网）](https://holybro.com/collections/gps-accessories)

![h-rtk](../../assets/hardware/gps/rtk_holybro_h-rtk_mount_3.png)