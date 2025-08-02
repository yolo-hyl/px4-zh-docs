# RTK GNSS/GPS（PX4集成）

[Real Time Kinematic](https://en.wikipedia.org/wiki/Real_Time_Kinematic) (RTK) 可提供厘米级GPS精度。  
本页说明RTK如何集成到PX4中。

:::tip
RTK GNSS的使用说明请参见 [硬件 > RTK GPS](../gps_compass/rtk_gps.md)。
:::

## 概述

RTK通过测量信号载波波的相位而非信号的信息内容来实现定位。它依赖于一个参考站提供实时校正数据，该数据可同时服务于多个移动站。

在PX4中设置RTK需要两个RTK GNSS模块和一个数据链路。固定式地面GPS单元称为**基站**，空中单元称为**流动站**。基站通过USB连接到_QGroundControl_，并通过数据链路使用MAVLink [GPS_RTCM_DATA](https://mavlink.io/en/messages/common.html#GPS_RTCM_DATA) 消息将RTCM校正数据流发送到机体。在飞控器上，MAVLink数据包被解包并发送到流动站单元进行处理以获得RTK解算结果。

数据链路通常需要能够处理每秒300字节的上行速率（更多信息请参见下方的[上行数据速率](#上行数据速率)部分）。

## 支持的RTK GNSS模块

我们测试的设备列表请参见 [用户指南](../gps_compass/rtk_gps.md#supported-devices)。

::: info
大多数设备有两个版本，分别为基站和流动站。  
请确保选择正确的版本。
:::

## 自动配置

PX4 GPS堆栈会根据模块连接位置（连接到_QGroundControl_还是飞控器）自动配置GPS模块通过UART或USB收发正确的消息。

一旦飞控器接收到`GPS_RTCM_DATA` MAVLink消息，就会自动通过现有数据通道将RTCM数据转发到连接的GPS模块（无需专用校正数据通道）。

::: info
u-blox U-Center RTK模块配置工具**不需要/不使用**！
:::

::: info
_QGroundControl_和飞控器固件共享相同的[PX4 GPS驱动堆栈](https://github.com/PX4/GpsDrivers)。  
这意味着新协议和/或消息的支持只需添加一次即可。
:::

### RTCM消息

QGroundControl配置RTK基站以1Hz频率输出以下RTCM3.2帧（除非另有说明）：

- **1005** - 基站天线参考点的XYZ坐标（基站位置），0.2Hz。
- **1077** - 完整的GPS伪距、载波相位、多普勒和信号强度（高分辨率）。
- **1087** - 完整的GLONASS伪距、载波相位、多普勒和信号强度（高分辨率）。
- **1230** - GLONASS码相位偏差。
- **1097** - 完整的Galileo伪距、载波相位、多普勒和信号强度（高分辨率）。
- **1127** - 完整的BeiDou伪距、载波相位、多普勒和信号强度（高分辨率）。

## 上行数据速率

基站的原始RTCM消息被封装到MAVLink `GPS_RTCM_DATA` 消息中并通过数据链路发送。  
每个MAVLink消息的最大长度为182字节。根据RTCM消息类型，MAVLink消息通常不会完全填满。

RTCM基站位置消息（1005）长度为22字节，其余消息长度根据可见卫星数量和卫星信号数量（如L1单元M8P仅1个）而变化。在任意时刻，任何单一星座的可见卫星最大数量为12颗。在实际条件下，理论上300 B/s的上行速率已足够。

如果使用_MAVLink 1_，则每个RTCM消息都会发送一个182字节的`GPS_RTCM_DATA`消息，无论消息长度如何。因此，上行需求约为每秒700+字节。这可能导致低带宽半双工遥测模块（如3DR Telemetry Radios）的链路饱和。

如果使用_MAVLink 2_，则`GPS_RTCM_DATA`消息中的空闲空间会被移除。最终的上行需求约为理论值（~300字节/秒）。

:::tip
如果GCS和遥测模块支持，PX4会自动切换到MAVLink 2。
:::

低带宽链路上必须使用MAVLink 2才能获得良好的RTK性能。需确保遥测链路全程使用MAVLink 2。  
可通过系统控制台的`mavlink status`命令验证协议版本：

```sh
nsh> mavlink status
instance #0:
        GCS heartbeat:  593486 us ago
        mavlink chan: #0
        type:           3DR RADIO
        rssi:           219
        remote rssi:    219
        txbuf:          94
        noise:          61
        remote noise:   58
        rx errors:      0
        fixed:          0
        flow control:   ON
        rates:
        tx: 1.285 kB/s
        txerr: 0.000 kB/s
        rx: 0.021 kB/s
        rate mult: 0.366
        accepting commands: YES
        MAVLink version: 2
        transport protocol: serial (/dev/ttyS1 @57600)
```