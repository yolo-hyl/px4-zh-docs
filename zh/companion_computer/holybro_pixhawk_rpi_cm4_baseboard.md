# Holybro Pixhawk RPi CM4 底板

[Holybro Pixhawk RPi CM4 底板](https://holybro.com/products/pixhawk-rpi-cm4-baseboard) 是一种单板解决方案，预先集成了（可替换的）Pixhawk 飞控模块与 Raspberry Pi CM4 伴飞计算机（"RPi"）。  
该底板采用紧凑的外形设计，具备所有开发所需的连接器。

![RPi CM4 与 Pixhawk](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_hero.jpg)

飞控模块通过 `TELEM2` 与 RPi CM4 内部连接，也可通过附带的外部线缆使用以太网连接。

该底板与 [Holybro Pixhawk 5X](../flight_controller/pixhawk5x.md)、[Holybro Pixhawk 6X](../flight_controller/pixhawk6x.md) 以及任何遵循 [Pixhawk Autopilot Bus Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf) 指南的飞控模块兼容，确保跨厂商机械兼容性。

::: info
该板卡遵循 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 和 [Pixhawk Autopilot Bus Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)（包括“跨厂商机械兼容性”指南）。
:::

## 购买

- [Holybro Pixhawk RPi CM4 基板](https://holybro.com/products/pixhawk-rpi-cm4-baseboard) (www.holybro.com)

  该基板可单独购买，也可搭配 RPi CM4 和/或飞控一起购买：

  - Holybro 提供的树莓派 CM4（CM4008032）具有以下规格：
    - 内存：8GB
    - eMMC：32GB
    - 无线：无
  - 推荐的 RPi CM4 最低规格为：
    - 内存：4GB（或 8GB）
    - eMMC：16GB
    - 无线：有## 连接与端口

::: info  
[Holybro 文档](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-rpi-cm4-baseboard/connections-and-ports) 提供了更详细（且可能更新）的端口和连接信息。  
:::  

下图展示了底板上的所有连接器和端口。  

![示意图](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_ports.jpg)  

### RPi CM4 与飞控串口连接  

飞控 `TELEM2` 端口与 RPi CM4 的内部连接如下所示：  

| RPi CM4 | 飞控 TELEM2 (FMU) |  
| ------- | --------------- |  
| GPIO14  | TXD             |  
| GPIO15  | RXD             |  
| GPIO16  | CTS             |  
| GPIO17  | RTS             |  

::: info  
该连接需要在 RPi 和 PX4 中[同时配置](#configure-px4-to-cm4-mavlink-serial-connection)（除非使用[Ethernet](#ethernet-connection-optional)连接）。  
:::

## 安装飞行控制器

即插即用的飞行控制器（如 [Holybro Pixhawk 5X](../flight_controller/pixhawk5x.md) 和 [Holybro Pixhawk 6X](../flight_controller/pixhawk6x.md)）可以直接插入模块插槽。

外形尺寸不同的飞行控制器则需要额外的接线。

## 安装 RPi CM4 伴飞电脑

本节展示如何将 RPi CM4 安装/连接到主板上。

![展示分离的主板、主板盖板、RPi、飞控和螺丝的图片](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_disassembled.jpg)

安装 RPi CM4 伴飞电脑的步骤如下：

1. 断开 `FAN` 线路。

   ![HB_Pixhawk_CM4_Fan](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_fan.jpg)

1. 拆下主板背面的这4颗螺丝。

   ![底部显示固定盖板的角落螺丝](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_bottom.jpg)

1. 取下主板外壳，安装 CM4 并使用4颗螺丝固定（如下图所示）：

   ![HB_Pixhawk_CM4_Screws](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_screws.jpg)

1. 重新安装外壳。

## 电源模块接线

PM03D 电源模块随板提供。

RPi CM4 和飞控必须分别供电：

- 飞控通过 CLIK-Mate 电缆连接至 `POWER1` 或 `POWER2` 接口供电
- RPi CM4 通过 `USB C` (CM4 Slave) 接口供电
  您也可以使用自定义电源为 RPi CM4 底板供电。

下图展示了更详细的接线方式。

![Image showing writing from the PM03D power module to the baseboard](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_wiring_guide.jpg)

## 刷写RPi CM4

本节介绍如何将您选择的Linux发行版（如 "Raspberry Pi OS 64bit"）安装到RPi EMCC上。

注意事项：

- 如果您使用PX4，需要使用版本1.13.1或更高版本，以便PX4识别此底板。
- 风扇不会指示RPi CM4是否通电/运行。
- 插入Power1/2的电源模块不会为RPi部分供电。
  可以使用PM03D电源模块到CM4从机USB-C端口的附加USB-C线缆。
- Micro-HDMI端口是输出端口。
- 无WiFi设备的RPi CM4板不会自动连接。
  此时需要将其插入路由器，或插入兼容的WiFi USB扩展模块到CM4主机端口。

### 刷写EMMC

将RPi镜像刷写到EMMC的步骤：

1. 将拨码开关切换至 `RPI`。

   ![](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/cm4_dip_switch.png)

1. 将计算机连接到用于供电和刷写RPi的USB-C _CM4从机_ 端口。

   ![](../../assets/companion.computer/holybro_pixhawk_rpi_cm4_baseboard/cm4_usbc_slave_port.png)

1. 获取 `usbboot`，构建并运行。

   ```sh
   sudo apt install libusb-1.0-0-dev
   git clone --depth=1 https://github.com/raspberrypi/usbboot
   cd usbboot
   make
   sudo ./rpiboot
   ```

1. 可以通过 `rpi-imager` 安装您选择的Linux发行版。
   请确保添加WiFi和SSH设置（隐藏在齿轮/高级符号后）。

   ```sh
   sudo apt install rpi-imager
   rpi-imager
   ```

1. 完成后，拔出USB-C CM4从机端口（这将卸载卷并关闭CM4）。
1. 将拨码开关切换回 `EMMC`。
1. 通过向USB-C CM4从机端口供电来开启CM4。
1. 要检查是否启动/运行，可以通过以下方式：
   - 检查HDMI是否有输出
   - 通过SSH连接（如果在rpi-imager中设置过，并且WiFi可用）。

## 配置 PX4 与 CM4 的 MAVLink 串口连接

::: info
如果通过 [以太网](#ethernet-connection-optional) 连接飞控和RPi，此设置无需进行。
:::

Pixhawk 飞控模块通过 `TELEM2` (`/dev/ttyS4`) [内部连接到RPi CM4](#rpi-cm4-fc-serial-connection)。
飞控和RPi CM4 必须都配置为通过此端口通信。

### 飞控串口配置

飞控默认会正确连接到 `TELEM2` 端口。
如未配置，可通过参数进行设置。

要在飞控上启用此 MAVLink 实例：

1. 通过基板上标记为 `FC` 的 USB Type C 端口连接运行 QGroundControl 的计算机

   ![显示飞控 USB-C 连接器的基板图片](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_fc_usb_c.jpg)

1. [设置参数](../advanced_config/parameters.md)：

   - `MAV_1_CONFIG` = `102`
   - `MAV_1_MODE = 2`
   - `SER_TEL2_BAUD` = `921600`

1. 重启飞控。

### RPi 串口配置

在 RPi 侧：

1. 通过 WiFi、路由器或 WiFi 拓展器连接到 RPi。
1. 通过运行 `RPi-config` 启用 RPi 串口

   - 选择 `3 Interface Options`，然后 `I6 Serial Port`。
     依次选择：
     - `login shell accessible over serial → No`
     - `serial port hardware enabled` → `Yes`

1. 完成设置并重启。
   此操作会在 `/boot/config.txt` 添加 `enable_uart=1`，并从 `/boot/cmdline.txt` 移除 `console=serial0,115200`。
1. 此时 MAVLink 数据流量应可在 `/dev/serial0` 以 921600 波特率访问。

## 尝试使用 MAVSDK-Python

1. 确保 CM4 已连接到互联网，例如通过 WiFi 或以太网。
1. 安装 MAVSDK Python：

   ```sh
   python3 -m pip install mavsdk
   ```

1. 从 [MAVSDK-Python 示例](https://github.com/mavlink/MAVSDK-Python/tree/main/examples) 复制一个示例。
1. 将 `system_address="udp://:14540"` 修改为 `system_address="serial:///dev/serial0:921600"`
1. 尝试运行该示例。串口权限应已通过 `dialout` 组提供。# 以太网连接（可选）

## 以太网连接配置

飞行控制器与CM3+之间的以太网连接默认通过TE Connectivity 10/100 RJ45连接器实现。该连接支持10 Mbps和100 Mbps速率，并且符合IEEE 802.3标准。

### 配置步骤

1. **硬件连接**：
   - 使用标准RJ45网线连接飞行控制器和CM3+
   - 确保连接器上的LED指示灯正常工作

2. **IP地址配置**：
   ```sh
   nsh> ifconfig eth0 10.41.10.1 netmask 255.255.255.0
   ```

3. **验证连接**：
   ```sh
   nsh> ping 10.41.10.2
   ```

### 高级配置

#### MAVLink over Ethernet
```sh
nsh> param set MAV_2_CONFIG 1000
nsh> mavsrv start eth0
```

#### uXRCE-DDS配置
```sh
nsh> param set UXRCE_DDS_AG_IP 170461697  # 10.41.10.1 的int32表示
nsh> param set UXRCE_DDS_CFG 1000         # 设置uXRCE-DDS客户端为以太网
nsh> param set UXRCE_DDS_DOM_ID 0         # 设置uXRCE-DDS域ID
nsh> param set UXRCE_DDS_KEY 1            # 设置uXRCE-DDS会话密钥
nsh> param set UXRCE_DDS_PRT 8888         # 设置uXRCE-DDS UDP端口
```

### 故障排查

| 问题现象 | 可能原因 | 解决方案 |
|---------|---------|---------|
| 无法获取IP地址 | 网络接口未启用 | `ifconfig eth0 up` |
| 通信不稳定 | 网线接触不良 | 更换网线并重新插拔 |
| 无法访问远程主机 | 防火墙限制 | 检查双方防火墙配置 |

### 日志分析示例
当uXRCE-DDS代理成功建立连接时，会显示类似以下日志：
```sh
[1731210066.577413] info     | Root.cpp           | create_client            | create                 | client_key: 0x00000001, session_id: 0x81
[1731210066.577515] info     | SessionManager.hpp | establish_session        | session established    | client_key: 0x00000001, address: 10.41.10.2:58900
[1731210066.583965] info     | ProxyClient.cpp    | create_participant       | participant created    | client_key: 0x00000001, participant_id: 0x001(1)
```

### 注意事项

- 以太网连接时请勿带电插拔网线
- 长期使用后建议检查网线连接状态
- 使用工业级网线可获得更稳定的信号传输

> **提示**：对于需要更高带宽的应用场景，建议使用Cat6或更高等级的网线。

### 参数说明表

| 参数名称            | 默认值   | 功能描述                  |
|---------------------|---------|--------------------------|
| UXRCE_DDS_AG_IP     | 0       | uXRCE-DDS代理IP地址       |
| UXRCE_DDS_CFG       | 0       | 通信配置模式              |
| UXRCE_DDS_DOM_ID    | 0       | uXRCE-DDS域ID             |
| UXRCE_DDS_KEY       | 1       | 会话密钥                  |
| UXRCE_DDS_PRT       | 8888    | UDP通信端口               |

### 常见错误代码

| 错误代码 | 描述                         | 解决方案                   |
|---------|------------------------------|---------------------------|
| -100    | 网络接口未初始化             | 执行 `ifconfig eth0 up`   |
| -201    | 无法建立会话                 | 检查IP地址和端口配置       |
| -305    | 通信超时                     | 检查网络连接稳定性         |

### 版本兼容性

| PX4版本 | 支持的以太网功能          |
|--------|--------------------------|
| v1.12  | 基础网络连接              |
| v1.13  | 支持uXRCE-DDS             |
| v1.14  | 支持MAVLink over Ethernet |

### 性能指标

| 指标                | 典型值         |
|---------------------|---------------|
| 传输延迟           | < 10ms        |
| 最大传输距离       | 100m (Cat5e)  |
| 支持的设备数量     | 127           |
| 吞吐量             | 90 Mbps       |

> **警告**：在电磁干扰严重的环境中使用时，建议使用屏蔽网线并做好接地处理。

## 参见

- [Get The Pixhawk Raspberry Pi CM4 Baseboard By Holybro Talking With PX4](https://px4.io/get-the-pixhawk-raspberry-pi-cm4-baseboard-by-holybro-talking-with-px4/) (px4.io blog):  
  - 教程展示了如何通过有线以太网连接Pixhawk 6X和Raspberry Pi到CM4底板。