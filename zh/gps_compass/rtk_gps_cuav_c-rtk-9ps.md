# CUAV C-RTK 9Ps

CUAV [C-RTK 9Ps](https://www.cuav.net/en/c_rtk_9ps/) 是一款多卫星、多频段、厘米级精度的 RTK GNSS 系统。

该模块可同时接收 GPS、GLONASS、Galileo 和 BeiDou 卫星信号，从而实现更快的定位和更高的精度。  
它还支持通过双模块实现 [RTK GPS Heading](../gps_compass/u-blox_f9p_heading.md)。

使用 C-RTK 9Ps 可使 PX4 实现厘米级的定位精度。  
这非常适用于测绘无人机、农业无人机等应用场景。

![C-RTK 9Ps](../../assets/hardware/gps/cuav_9ps/c-rtk9s.jpg)

## 购买渠道

[cuav Store](https://store.cuav.net/shop/c-rtk-9ps/)

## 规格参数

![Specification](../../assets/hardware/gps/cuav_9ps/c-rtk9s-specification.jpg)

## 接线与连接

**C-RTK 9Ps (基站)**

![Base module setup](../../assets/hardware/gps/cuav_9ps/c-rtk9ps_base.png)

- 使用三脚架将基站天线安装在顶部，并将天线连接到基站
- 通过 USB 线缆将基站和遥测设备连接到计算机

**C-RTK 9Ps (移动站)**

![Rover mode setup](../../assets/hardware/gps/cuav_9ps/c-rtk9ps-rover.png)

- 垂直安装 C-RTK 9Ps (移动站) 天线
- 将天线连接到 C-RTK 9Ps (移动站)
- 将 C-RTK 9Ps (移动站) 连接到飞控
- 将遥测设备连接到飞控的 `TELEM1`/`TELEM2` 接口

::: info
C-RTK 9Ps 提供 6 针和 10 针连接器，兼容 Pixhawk 标准飞控  
连接至 `GPS1` 或 `GPS2`  
根据飞控选择合适的线缆
:::

## 配置

通过 _QGroundControl_ 在 PX4 上设置和使用 RTK 基本上是即插即用（更多信息请参见 [RTK GPS](../gps_compass/rtk_gps.md)）。

## 引脚分配

![Pinouts](../../assets/hardware/gps/cuav_9ps/pinouts-en.jpg)

## 物理尺寸

![Cuav-9ps dimensions](../../assets/hardware/gps/cuav_9ps/c-rtk9ps_dimensions.jpg)

![Base unit antenna: physical dimensions](../../assets/hardware/gps/cuav_9ps/c-rtk9ps_base_unit_antenna_dimensions.jpg)

![Sky unit antenna: physical dimensions](../../assets/hardware/gps/cuav_9ps/c-rtk9ps_sky_unit_antenna_dimensions.jpg)

## 导航更新率

![Navigation update rate information](../../assets/hardware/gps/cuav_9ps/nav-rate-en.png)

## 更多信息

[CUAV docs](https://doc.cuav.net/gps/c-rtk-series/en/c-rtk-9ps/)