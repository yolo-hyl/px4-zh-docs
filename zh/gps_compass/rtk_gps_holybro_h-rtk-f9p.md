# Holybro H-RTK F9P GNSS

::: tip
[Holybro H-RTK ZED-F9P Rover](../dronecan/holybro_h_rtk_zed_f9p_gps.md) 是该模块的升级版本。
:::

[Holybro H-RTK F9P GNSS](https://holybro.com/products/h-rtk-f9p-gnss-series) 是 Holybro 推出的多频段高精度 [RTK GNSS 系统](../gps_compass/rtk_gps.md) 系列产品。  
该系列产品与 [H-RTK M8P](../gps_compass/rtk_gps_holybro_h-rtk-m8p.md) 系列类似，但采用多频段 RTK 技术，具有更快的收敛时间与可靠性性能，可同时接收 GPS、GLONASS、伽利略和北斗信号，并具备更高的更新速率，适用于需要厘米级精度的高动态和高容量应用。  
该产品采用 u-blox F9P 模块、IST8310 磁力计和三色 LED 指示灯，并集成了安全开关以实现简单便捷的操作。

Holybro H-RTK F9P 提供三种型号可选，每种型号采用不同天线设计以满足不同需求。  
更多详情请参考 [规格与型号对比部分](#规格与型号对比)。

通过 RTK 技术，PX4 可获得厘米级定位精度，其精度远高于普通 GPS 提供的水平。

![h-rtk](../../assets/hardware/gps/rtk_holybro_h-rtk-f9p_all_label.jpg)

## 购买渠道

- [H-RTK F9P (Holybro 官网)](https://holybro.com/products/h-rtk-f9p-gnss-series)
- [H-RTK 附件 (Holybro 官网)](https://holybro.com/collections/h-rtk-gps)

## 配置

通过 _QGroundControl_ 在 PX4 上配置和使用 RTK 基本上是即插即用的（更多信息请参见 [RTK GPS](../gps_compass/rtk_gps.md)）。

## 线缆连接

H-RTK 螺旋型号配备 GH 10 针和 6 针线缆，兼容使用 Pixhawk 连接器标准的飞行控制器上的 GPS1 和 GPS2 接口，例如 [Pixhawk 4](../flight_controller/pixhawk4.md) 和 [Pixhawk 5x](../flight_controller/pixhawk5x.md)。

H-RTK Rover Lite 提供两个版本：  
标准版本配备 10 针连接器，用于 `GPS1` 接口。  
“第二 GPS”版本配备 6 针连接器，用于 `GPS2` 接口。  
该版本作为 [双 GPS 系统](../gps_compass/index.md#dual_gps) 的备用 GPS 使用。

::: info
可能需要修改线缆/连接器以连接其他飞控板（详见下文 [引脚映射](#引脚映射)）。
:::

## 引脚映射

![h-rtk-f9p_rover_pinmap](../../assets/hardware/gps/rtk_holybro_h-rtk_helical_pinmap.jpg)

![h-rtk-f9p_helical_pinmap](../../assets/hardware/gps/rtk_holybro_h-rtk_rover_lite_pinmap.jpg)

## 规格与型号对比

![h-rtk-f9p_spec](../../assets/hardware/gps/rtk_holybro_h-rtk-f9p_spec.png)

## GPS 附件

[H-RTK 安装支架 (Holybro 官网)](https://holybro.com/products/vertical-mount-for-h-rtk-helical)

![h-rtk](../../assets/hardware/gps/rtk_holybro_h-rtk_mount_3.png)