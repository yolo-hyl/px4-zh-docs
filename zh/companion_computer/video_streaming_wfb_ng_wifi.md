# 使用WiFi在原始模式下进行视频流数据链传输 (WFB-ng)

本教程演示如何使用Logitech C920或RaspberryPi相机设置[伴飞计算机](../companion_computer/index.md)，将无人机的视频流传输到地面计算机并在_QGroundControl_中显示。该设置使用未连接（广播）模式的WiFi，以及来自[WFB-ng项目](https://github.com/svpcom/wfb-ng)的软件。

该通道还可作为双向[遥测](../telemetry/index.md)链路和TCP/IP隧道，用于飞行中的无人机控制。如果通过QGroundControl的Joystick手动控制无人机（使用MAVLink协议），则可以使用WFB-ng作为所有无人机通信的单一链路（视频、MAVLink遥测、通过Joystick的远程控制）。

:::warning
在使用_WFB-ng_之前，请检查当地法规是否允许此类WiFi使用。
:::

## WFB-ng 概述

_WFB-ng 项目_ 提供了一种使用低层 WiFi 数据包的数据传输方式，以避免普通 IEEE 802.11 协议栈在距离和延迟方面的限制。

_WFB-ng_ 的主要优势包括：

- 低延迟视频链路。
- 双向遥测链路（MAVLink）。
- TCP/IP 隧道。
- 自动 TX 多样性 - 在地面使用多张网卡以避免天线跟踪器。
- 完整的链路加密和认证（使用 [libsodium](https://download.libsodium.org/doc/)）。
- MAVLink 数据包聚合（在发送前将小数据包打包成批次）。
- 增强型 [OSD](https://github.com/svpcom/wfb-ng-osd) 适用于 Raspberry PI 或基于 gstreamer 的通用 Linux 桌面。

更多信息请参阅下文的 [常见问题](#常见问题)。

## 硬件

### 机体设置

机体设置包括以下组件：

- Raspberry PI 3B/3B+/ZeroW
- 摄像头。
  已测试的型号如下：

  - [Raspberry Pi 摄像头](https://www.raspberrypi.org/products/camera-module-v2/) 通过CSI接口连接。
  - [Logitech 摄像头 C920](https://www.logitech.com/en-us/product/hd-pro-webcam-c920?crid=34) 通过USB接口连接

- Wi-Fi模块 [ALPHA AWUS036ACH](https://www.alfa.com.tw/products_detail/1.htm) 或其他 **RTL8812au** 卡片。

### 地面站

- 地面站计算机。
  已测试的选项包括：

  - 任何带有USB端口的Linux计算机（在Ubuntu 18.04 x86-64上测试过）
  - 任何操作系统运行QGround control并连接Raspberry PI的计算机，通过Ethernet连接（RPi提供Wi-Fi连接）。

- Wi-Fi模块 [ALPHA AWUS036ACH](https://www.alfa.com.tw/products_detail/1.htm) 或其他 **RTL8812au** 卡片。
  有关支持模块的更多信息，请参见 [WFB-ng wiki > WiFi hardware](https://github.com/svpcom/wfb-ng/wiki/WiFi-hardware)。

## 硬件修改

Alpha AWUS036ACH 是一款中等功率的网卡，在传输时会消耗大量电流。  
如果从普通 USB2 接口供电，大多数 **ARM 板**的端口会复位。  
如果通过 **原生 USB3 数据线**将 **USB3 接口**连接到 **Linux 笔记本**，则无需修改即可使用该设备。

对于 **Raspberry PI**（无人机或地面站）必须通过以下两种方式之一直接连接 5V BEC（地面站使用大电流电源适配器）：

- 制作自定义 USB 线（[剪断 USB 插头的 `+5V` 线并连接到 BEC](https://electronics.stackexchange.com/questions/218500/usb-charge-and-data-separate-cables)）  
- 在 PCB 上靠近 USB 接口处剪断 `+5V` 线并连接到 BEC（不确定时不要操作，建议使用自定义线缆）

还必须在 **网卡 +5V 和地之间**添加一个 470uF **低 ESR 电容**（如电调电容）以过滤电压尖峰。  
建议将电容集成到自定义 USB 线缆中。  
缺少该电容可能导致数据包损坏或丢失。  
使用多条接地线时需注意 [地环问题](https://en.wikipedia.org/wiki/Ground_loop_%28electricity%29)。

::: info  
如果使用淘宝/速卖通上的"超高功率"网卡，则必须按照上述方式供电。  
:::

### UAV 配置

1. 从 [latest wfb-ng release](https://github.com/svpcom/wfb-ng/releases/) 下载 Raspberry PI 镜像  
2. 将镜像写入 **无人机**的 Raspberry PI  
3. 重启后使用标准凭据（pi/raspberry）通过 SSH 登录  
4. 按照 motd 显示的 **air** 角色执行操作  
5. 配置摄像头管道。编辑 `/etc/systemd/system/fpv-camera.service`，根据摄像头类型（树莓派摄像头或罗技摄像头）取消注释对应管道  
6. 编辑 `/etc/wifibroadcast.cfg`，根据天线设置配置 WiFi 信道（或使用默认 #165 对应 5.8GHz）  
7. 配置 PX4 输出 1500 Kbps 的遥测流（其他 UART 速率与 RPi 频率分频器不匹配）：  
   连接 Pixhawk UART 到 Raspberry Pi UART  
   在 `/etc/wifibroadcast.cfg` 的 `[drone_mavlink]` 部分取消注释 `peer = 'serial:ttyS0:1500000'`

### 使用 Linux 笔记本作为 GCS（比使用 RPi 复杂）

1. 在 **地面** Linux 开发机上执行：

   ```sh
   sudo apt install libpcap-dev libsodium-dev python3-all python3-twisted
   git clone -b stable https://github.com/svpcom/wfb-ng.git
   cd wfb-ng && make deb && sudo apt install ./deb_dist/wfb-ng*.deb
   ```

2. 按照 [Setup HOWTO](https://github.com/svpcom/wfb-ng/wiki/Setup-HOWTO) 完成安装  
3. 不要忘记从 **无人机**侧复制 `/etc/gs.key` 到 **地面**侧以绑定两套配置  
4. 同时确保使用与无人机侧相同的频率信道

### 使用 Raspberry PI 作为 GCS（更简单）

如果使用 Windows 或 macOS，或不想在 Linux 笔记本上配置 WFB-ng，可以使用相同镜像和另一块 Raspberry Pi：

1. 将镜像写入 **地面** Raspberry Pi  
2. 重启后使用标准凭据（pi/raspberry）通过 SSH 登录  
3. 按照 motd 显示的 **ground** 角色执行操作，但跳过 `fpv-video` 服务和 `osd` 服务的设置  
4. 通过网线连接笔记本和地面 RPi 并配置 IP 地址  
5. 编辑 `/etc/wifibroadcast.cfg`，在 `[gs_mavlink]` 和 `[gs_video]` 部分设置笔记本的 IP 地址（替换 `127.0.0.1`）

### QGroundControl 配置

1. 运行 _QGroundControl_ 并在 5600 端口设置 `RTP h264` 作为视频源  
2. 使用默认设置（14550 端口的 udp）作为 mavlink 源

## 调整无线电设置

在默认设置下，WFB 使用无线电频道 165（5825 MHz），带宽 20MHz，MCS #1（QPSK 1/2）配合长 GI。
这提供了约 7 mbit/s 的 **有效** 速度（即经过 FEC 和数据包编码后的可用速度），且在 **双向** 通信中总和可达此速度，因为 WiFi 是半双工。
因此，它适合用于 720p@49fps（4 mbit/s）的视频下行传输，以及两个全速遥测数据流（上行和下行）。
如果需要更高带宽，可以使用其他 MCS 索引（例如 2 或更高）

## 天线与分集

对于简单场景，可以使用全向天线（线性极化，如Wi-Fi网卡附带的天线）或圆极化叶形天线（[圆极化覆盖叶天线](http://www.antenna-theory.com/antennas/cloverleaf.php)）。
如果需要建立远距离链路，可以使用多个Wi-Fi适配器配合定向和全向天线。多适配器的TX/RX分集功能开箱即用（只需将多个网卡添加到`/etc/default/wifibroadcast`）。
如果您的WiFi适配器配备双天线（如Alfa AWU036ACH），TX分集通过[STBC](https://en.wikipedia.org/wiki/Space%E2%80%93time_block_code)实现。
目前不支持四端口卡（如Alfa AWUS1900）。

## 常见问题

**Q:** _wfb-ng 可以传输什么类型的数据？_

**A:** 任何 UDP 包且大小 <= 1445。
例如 RTP 中的 x264 或 MAVLink。

**Q:** _传输有保证吗？_

**A:** Wifibroadcast 使用 FEC（前向纠错）。
你可以根据需求调整它（TX 和 RX 同时调整！）。

**Q:** _飞行多远还能保持连接？_

**A:** 这取决于你的天线和 WiFi 卡。
使用 Alfa AWU036ACH 和地面 20dBi 板状天线时，约 20km 是可能的。

:::warning
不要使用遥控发射机所使用的频段！
或正确设置 RTL 以避免模型丢失。
:::

**Q:** _是否仅支持 Raspberry PI？_

**A:** wfb-ng 不依赖任何 GPU - 它处理 UDP 数据包。
但要获取 RTP 流，你需要一个视频编码器（将相机原始数据编码为 x264 流），或必须使用带有硬件视频编解码器的相机，如 Logitech C920 或以太网安防摄像头。

#### 推荐用于无人机的 ARM 开发板有哪些？

- RPI3b/3b+/ZeroW。
  提供预构建镜像，但仅支持 CSI 相机的 h264 视频。
- Jetson Nano。
  支持 h264 和 h265，但需根据 [Setup HOWTO](https://github.com/svpcom/wfb-ng/wiki/Setup-HOWTO) 自行配置

你可以使用任何其他 Linux ARM 开发板，但需要使用带有内置硬件视频编解码器的以太网或 USB 相机（如 Logitech C920）。

## 理论

WFB-ng 将 WiFi 卡置于监控模式。此模式允许在不建立关联和等待确认包的情况下发送和接收任意数据包。
[监控模式下 IEEE 802.11 硬件的注入能力与媒体访问分析](https://github.com/svpcom/wfb-ng/blob/master/doc/Analysis%20of%20Injection%20Capabilities%20and%20Media%20Access%20of%20IEEE%20802.11%20Hardware%20in%20Monitor%20Mode.pdf)
[802.11 时序](https://github.com/ewa/802.11-data)