# Femtones MINI2 接收器

[MINI2 接收器](http://www.femtomes.com/#/MiniII?type=0) 是一款 RTK GPS 接收器，可提供高频率且可靠的 RTK 初始化功能，实现厘米级定位精度。  
该设备适用于需要高精度定位的应用场景（如导航和测绘等）。

该接收器通过串口（UART）连接至 PX4，并可通过标准网络浏览器使用以太网进行配置。  

![MINI II 接收器](../../assets/hardware/gps/rtk_fem_miniII_receiver.jpg)

::: info
PX4 的以太网、CAN 和 USB 驱动正在开发中。
:::

## 必需固件选项

购买设备时需要选择以下固件选项：

- 5Hz, 10Hz, 20Hz
- INS
- HEADING
- OBS
- RTK
- BASE

## 购买渠道

直接联系 [Femtones](http://www.femtomes.com/) 获取销售报价：

- **邮箱:** [sales@femtomes.com](mailto:sales@femtomes.com)
- **电话:** +86-10-53779838

## 功能端口

![MINI II 1](../../assets/hardware/gps/rtk_fem_miniII_1.jpg)

## 接线与连接

[MINI2 接收器](http://www.femtomes.com) 通过飞控器上的串口（GPS 端口）进行数据连接。  
为模块供电需要单独的 12V 电源。12 针连接器上的引脚编号如下所示。

![MINI_II_2](../../assets/hardware/gps/rtk_fem_miniII_2.jpg)

## 配置

为实现航向估计，两个天线需位于同一水平面且相距至少 30 厘米。  
天线朝向不影响使用，可通过 [GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET) 参数进行配置。

通过 [GPS_1_CONFIG](../advanced_config/parameter_reference.md#GPS_1_CONFIG) 配置 [MINI2 接收器](http://www.femtomes.com/#/MiniII?type=0) 所运行的串口，并通过 [SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD) 将波特率设置为 115200。

配置完成后，该接收器的使用方式与其它 [RTK GPS](../gps_compass/rtk_gps.md) 相同（即遵循 Survey-in 流程）。

## 附加信息

MINI2 集成了以下组件：

- [FB672](http://www.femtomes.com/#/FB672): 小型双天线双频 GNSS OEM 板（提供厘米级定位和精确航向）

  ![FB672](../../assets/hardware/gps/rtk_fem_fb_1.jpg)

- [FB6A0](http://www.femtomes.com/#/FB6A0): 小型四频 GNSS OEM 板（提供厘米级定位精度）

  ![FB6A0](../../assets/hardware/gps/rtk_fem_fb_2.jpg)

详细产品说明可通过官方网站获取或通过联系公司获得。