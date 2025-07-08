# ESP8266 WiFi模块

ESP8266及其克隆产品是低成本且易于获取的Wi-Fi模块，具有完整的TCP/IP协议栈和微控制器功能。它们可以与任何Pixhawk系列控制器配合使用。

:::tip
ESP8266是[Pixracer](../flight_controller/pixracer.md)的_defacto_默认WiFi模块（通常与之捆绑销售）。
:::

## 购买渠道

ESP8266模块可从多家供应商处获得。  
以下列出部分供应商。  

大多数模块仅支持3.3V输入，而部分飞控（如Pixhawk 4）输出为5V（您需要检查兼容性，并在必要时进行电压分压处理）。

支持3.3V供电的模块：

- [WRL-17146](https://www.sparkfun.com/products/13678) (Sparkfun)
- [AI Cloud](https://us.gearbest.com/boards-shields/pp_009604906563.html) - 已停产 (GearBeast)

支持5.0V供电的模块：

- [AI Thinker](https://www.banggood.com/Wireless-Wifi-to-Uart-Telemetry-Module-With-Antenna-for-Mini-APM-Flight-Controller-p-1065339.html) (Banggood)
- [AlphaUAVLink](https://www.banggood.com/MAVLink-Wifi-Bridge-2_4G-Wireless-Wifi-Telemetry-Module-with-Antenna-for-Pixhawk-APM-Flight-Controller-p-1428590.html) (Banggood)
- [Kahuna](https://www.beyondrobotix.com/products/kahuna?utm_source=px4-esp8266-docs) (Beyond Robotix)

  一款即插即用的ESP8266模块。

  Kahuna配有可直接连接Pixhawk标准`TELEM1`或`TELEM2`端口的线缆。  
  该模块已预烧录最新固件，并配备u.fl连接器以连接外部天线。  
  最多可能需要设置波特率参数，对于`TELEM1`端口为`SER_TEL1_BAUD = 57600 (57600 8N1)`。  
  [用户指南](https://docs.google.com/document/d/1VyOsp9_q6dIAdYdWuDFcWoqqrNy_vbFMANubZA3Uz5g/edit?pli=1&tab=t.0)包含WiFi设置及其他相关信息。

  ![Kahuna ESP8266 WiFi Module](../../assets/peripherals/telemetry/esp8266/beyond_robotics_kahuna_esp8266.png)

## Pixhawk/PX4 设置与配置 {#px4_config}

:::tip
你_可能_需要首先使用PX4兼容的ESP8266固件更新遥控器([见下文](#esp8266-flashing-firmware-advanced))。
制造说明应会解释是否需要执行此操作。
:::

将ESP8266连接到Pixhawk系列飞行控制器（例如Pixracer）上的任意空闲UART。

通过USB将飞行控制器连接到地面站（因为WiFi尚未完全配置）。

使用_QGroundControl_：

- [加载最近的PX4固件到飞行控制器](../config/firmware.md)。
- [配置用于连接ESP8266的串口](../peripherals/serial_configuration.md)。
  请记住将波特率设置为921600以匹配ESP8266的设置值。
- [配置MAVLink](../peripherals/mavlink_peripherals.md)在对应串口上，以便通过ESP866接收遥测数据和发送指令。

在配置完用于连接遥控器的飞行控制器串口后，可以移除地面站与机体之间的物理USB连接。

## 通过 ESP8266 连接到 QGC

该模块会暴露一个WiFi热点，您的地面站计算机可通过该热点连接到机体。

::: info
ESP8266 热点的设置通常会随模块一起提供（例如通常打印在电路板背面或包装盒上）。

常见的出厂网络设置如下：

- **SSID:** PixRacer
- **Password:** pixracer
- **WiFi Channel:** 11
- **UART speed:** 921600

其他模块可能使用类似以下的设置：

- **SSID:** IFFRC_xxxxxxxx
- **Password:** 12345678
- **IP:** 192.168.4.1
- **Port:** 6789 (TCP)

以下是来自 AlphaUILink 和 DOITING 的电路板示例：

<img src="../../assets/peripherals/telemetry/esp8266/alpha_uavlink_back.jpg" width="250px" alt="AlphaUAVLink - Back"/> <img src="../../assets/peripherals/telemetry/esp8266/alpha_uavlink_front.jpg" width="250px" alt="AlphaUAVLink - Front"/> <img src="../../assets/peripherals/telemetry/esp8266/doiting_eps_12f_back.jpg" width="250px" alt="DOITING EPS 12F - Back"/> <img src="../../assets/peripherals/telemetry/esp8266/doiting_eps_12f_front.jpg" width="250px" alt="DOITING EPS 12F - Front"/>
:::

在支持WiFi的_QGroundControl_地面站计算机/平板上，查找并连接到ESP8266的开放无线网络。在Windows计算机上，名为 **Pixracer** 且默认密码为 **pixracer** 的网络设置将如下所示：

![Windows 网络设置: 连接](../../assets/peripherals/pixracer_network_setup_connection_windows.png)
![Windows 网络设置: 安全](../../assets/peripherals/pixracer_network_setup_security_windows.png)

当地面站计算机连接到名为 "Pixracer" 的WiFi接入点时，_QGroundControl_ 会自动连接到机体。

如果您使用的是其他WiFi名称的模块，则需要手动设置 QGroundControl 的WiFi连接，如下一节所示。

## 配置 QGC 使用非标准 WiFi 连接

_QGroundControl_ 会在地面站计算机连接到 "Pixracer" WiFi 接入点时自动连接到机体。  
对于其他任何接入点名称，您需要手动创建自定义通信链接：

1. 前往 [应用设置 > 通信链接](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/settings_view.html)  
2. 添加新连接并设置相应参数。  
3. 选择新连接，然后点击 **连接**。  
4. 此时机体应已连接## 验证

此时您应该能通过无线链路在QGC电脑上看到HUD移动，并且可以查看ESP8266 WiFi Bridge的摘要面板（如下所示）。

![QGC Summary showing Wifi Bridge](../../assets/qgc/summary/wifi_bridge.png)

:::tip
如果连接过程中遇到任何问题，请参见[QGC 使用问题](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/troubleshooting/qgc_usage.html)。
:::

## ESP8266 烧录/固件 (高级)

来自不同制造商的ESP8266模块可能没有预装合适的ESP8266固件。  
以下说明解释了如何使用正确版本更新数传模块。

### 预构建二进制文件

[MavLink ESP8266 Firmware V 1.2.2](http://www.grubba.com/mavesp8266/firmware-1.2.2.bin)

### 从源码构建

[firmware repository](https://github.com/dogmaphobic/mavesp8266) 包含构建和烧录ESP8266固件所需的所有工具和说明。

### 使用OTA更新固件

如果已安装1.0.4或更高版本固件，可通过ESP的 _Over The Air Update_ 功能进行更新。  
只需连接到其AP WiFi网络并访问：`http://192.168.4.1/update`，然后选择上方下载的固件文件并上传到WiFi模块。

:::tip  
这是更新固件最简便的方法！  
:::

### 烧录ESP8266固件

烧录前请确保按以下说明将ESP8266置于 _Flash Mode_。  
如果已克隆[MavESP8266](https://github.com/dogmaphobic/mavesp8266) 仓库，可通过提供的[PlatformIO](http://platformio.org) 工具和环境构建并烧录固件。  
如果下载了上述预构建固件，请下载[esptool](https://github.com/espressif/esptool) 工具并使用以下命令行：

```sh
esptool.py --baud 921600 --port /dev/your_serial_port write_flash 0x00000 firmware_xxxxx.bin
```

其中：

- **firmware_xxxxx.bin** 是上方下载的固件文件  
- **your_serial_port** 是ESP8266连接的串口名称（例如 `/dev/cu.usbmodem`）

### 固件烧录接线方式

:::warning  
大多数ESP8266模块仅支持3.3伏电压，而某些飞控（例如Pixhawk 4）输出为5伏。请检查兼容性并在需要时降低电压。  
:::

将ESP8266置于 _Flash Mode_ 有多种方法，但并非所有USB/UART适配器都提供自动模式切换所需的所有引脚。  
要使ESP8266进入 _Flash Mode_，需将GPIO-0引脚接地（GND），CH_PD引脚接高电平（VCC）。  
我的接线方式如下：

![esp8266 烧录接线](../../assets/hardware/telemetry/esp8266_flashing_rig.jpg)

我制作了一根电缆，将FTDI适配器与ESP8266的RX、TX、VCC和GND引脚正确连接。  
从ESP8266引出的GPIO-0和CH_PD引脚保持自由，可通过分别连接到GND和VCC来启动正常模式或烧录模式。

#### ESP8266 (ESP-01) 引脚分布

![esp8266 wifi模块引脚](../../assets/hardware/telemetry/esp8266_pinout.jpg)

#### 使用FTDI USB/UART适配器的烧录接线图

![esp8266 烧录](../../assets/hardware/telemetry/esp8266_flashing_ftdi.jpg)