# 伴飞计算机外设

本节介绍伴飞计算机外设相关的信息。
这些外设既包括可能连接到伴飞计算机的组件（可能由PX4触发/访问），也包括连接计算机到飞控器所需的设备。

## 伴飞计算机/Pixhawk通信

本节列出可用于伴飞计算机和飞控器之间物理串口/数据连接的设备。

::: info
通过TELEM2使用MAVLink与伴飞计算机通信的PX4配置，请参考[MAVLink (OSD / Telemetry)](../peripherals/mavlink_peripherals.md#telem2)。
其他相关主题/章节包括：[伴飞计算机](../companion_computer/index.md)、[机器人](../robotics/index.md)和[uXRCE-DDS (PX4-ROS 2/DDS Bridge)](../middleware/uxrce_dds.md)。
:::

### FTDI设备

FTDI USB适配器是伴飞计算机与Pixhawk之间通信的最常见方式。
只要适配器的IO设置为3.3V，通常都是即插即用的。
为了充分利用Pixhawk硬件提供的串口链路的全部功能/可靠性，建议使用流量控制。

一些"开箱即用"的选项如下：

| 设备名称                                                                 | 3.3V IO（默认） | 流量控制 | Tx/Rx LED | JST-GH |
| ---------------------------------------------------------------------- | ----------------- | ------------ | ---------- | ------ |
| [mRo USB FTDI串口转JST-GH（基础版）][mro_usb_ftdi_serial_to_jst_gh] | 支持              | 支持         | 否         | 是     |
| [SparkFun FTDI基础版转接器][sparkfun_ftdi__breakout]                | 是                | 否           | 是         | 否     |

<!-- 上表的参考链接 -->

[mro_usb_ftdi_serial_to_jst_gh]: https://store.mrobotics.io/USB-FTDI-Serial-to-JST-GH-p/mro-ftdi-jstgh01-mr.htm
[sparkfun_ftdi basic_breakout]: https://www.sparkfun.com/products/9873

您也可以使用现成的FTDI电缆[例如此款](https://www.sparkfun.com/products/9717)，并通过适当的接头适配器将其连接到飞控器上
（JST-GH连接器在Pixhawk标准中指定，但您应确认飞控器的连接器类型）。

### 逻辑电平转换器

有时伴飞计算机可能会暴露运行在1.8V或5V的硬件级IO，而Pixhawk硬件运行在3.3V IO。
为了解决这个问题，可以使用电平转换器安全地转换发送/接收信号电压。

选项包括：

- [SparkFun 双向逻辑电平转换器](https://www.sparkfun.com/products/12009)
- [4通道I2C安全双向逻辑电平转换器 - BSS138](https://www.adafruit.com/product/757)

## 摄像头

摄像头用于图像和视频捕获，更广泛地说是为[计算机视觉](../computer_vision/index.md)应用提供数据（在这种情况下，"摄像头"可能仅提供处理后的数据，而非原始图像）。

### 立体摄像头

立体摄像头通常用于深度感知、路径规划和SLAM。
它们与伴飞计算机之间完全即插即用没有任何保证。

流行的立体摄像头包括：

- [Intel® RealSense™ 深度摄像头 D435](https://www.intelrealsense.com/depth-camera-d435/)
- [Intel® RealSense™ 深度摄像头 D415](https://www.intelrealsense.com/depth-camera-d415/)
- [DUO MLX](https://duo3d.com/product/duo-minilx-lv1)

### VIO摄像头/传感器

以下传感器可用于[视觉惯性里程计（VIO）](../computer_vision/visual_inertial_odometry.md)：

- [T265 Realsense 跟踪摄像头](../peripherals/camera_t265_vio.md)

## 数据通信（LTE）

可以将LTE USB模块连接到伴飞计算机，并用于在飞控器和互联网之间路由MAVLink流量。

地面站和伴飞计算机之间没有"标准方法"通过互联网连接。
通常情况下无法直接连接，因为它们中的任何一个都不会在互联网上拥有公共/静态IP地址。

::: info
通常您的路由器（或移动网络）拥有公共IP地址，而您的GCS计算机/机体位于一个_本地_网络中。
路由器使用网络地址转换（NAT）将来自本地网络的_出站_请求映射到互联网，并可以使用该映射将_响应_路由回请求系统。
然而NAT无法知道如何将来自任意外部系统的流量定向到哪里，因此无法_主动_连接到运行在本地网络中的GCS或机体。
:::

一种常见方法是在伴飞计算机和GCS计算机之间建立虚拟专用网络（即在两台计算机上安装类似[zerotier](https://www.zerotier.com/)的VPN系统）。
伴飞计算机随后使用[mavlink-router](https://github.com/intel/mavlink-router)在串口接口（飞控器）和GCS计算机的VPN网络之间路由流量。

这种方法的优势在于GCS计算机地址在VPN内可以是静态的，因此_mavlink路由器_的配置不需要随时间改变。
此外，通信链路是安全的，因为所有VPN流量都是加密的（MAVLink 2本身不支持加密）。

::: info
您也可以选择将流量路由到VPN的广播地址（即`x.x.x.255:14550`，其中'x'取决于VPN系统）。
这种方法意味着您不需要知道GCS计算机的IP地址，但可能会导致比预期更多的流量（因为数据包会广播到VPN网络中的每台计算机）。
:::

一些已知可工作的USB模块包括：

- [华为 E8372](https://consumer.huawei.com/en/mobile-broadband/e8372/) 和 [华为 E3372](https://consumer.huawei.com/en/mobile-broadband/e3372/)
  - _E8372_ 包含WiFi，您可以在将其插入伴飞计算机时配置SIM卡（使开发工作流程更容易）。_E3372_ 没有WiFi，因此您必须通过将其插入笔记本电脑来配置。