

# Holybro Pixhawk RPi CM4 基板

[Holybro Pixhawk RPi CM4 Baseboard](https://holybro.com/products/pixhawk-rpi-cm4-baseboard) 是一款单板解决方案，预先集成了可替换的Pixhawk飞控与Raspberry Pi CM4协处理器（"RPi"）。该基板采用紧凑结构，集成了所有开发所需接口。

![RPi CM4 with Pixhawk](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_hero.jpg)

飞控模块通过`TELEM2`与RPi CM4内部连接，也可通过附带的外部网线使用Ethernet连接。

该基板兼容 [Holybro Pixhawk 5X](../flight_controller/pixhawk5x.md)、[Holybro Pixhawk 6X](../flight_controller/pixhawk6x.md) 以及任何符合[Pixhawk Autopilot Bus Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)厂商间机械兼容规范的Pixhawk控制器。

::: info
该板卡遵循[Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)和[Pixhawk Autopilot Bus Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)（包括"厂商间机械兼容"指导原则）
:::

## 购买

- [Holybro Pixhawk RPi CM4 Baseboard](https://holybro.com/products/pixhawk-rpi-cm4-baseboard) (www.holybro.com)

  该底板可单独购买，也可搭配RPi CM4和/或飞控模块购买：

  - Holybro提供的Raspberry Pi CM4 (CM4008032) 具备以下规格：
    - 内存: 8GB
    - eMMC: 32GB
    - 无线: 无
  - 推荐的RPi CM4最低规格为：
    - 内存: 4GB (或 8GB)
    - eMMC: 16GB
    - 无线: 有

## 连接与端口

::: info
[Holybro 文档](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-rpi-cm4-baseboard/connections-and-ports) 提供了更详细（且可能更"最新"）的端口和连接信息。
:::

下图显示了底板上的所有连接器和端口。

![示意图](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_ports.jpg)

### RPi CM4 与 FC 串口连接

飞行控制器 `TELEM2` 端口在内部连接到 RPi CM4，如所示：

| RPi CM4 | FC TELEM2 (FMU) |
| ------- | --------------- |
| GPIO14  | TXD             |
| GPIO15  | RXD             |
| GPIO16  | CTS             |
| GPIO17  | RTS             |

::: info
连接必须[在 RPi 和 PX4 中进行配置](#配置 PX4 与 CM4 的 MAVLink 串口连接)（除非使用 [以太网](#ethernet-connection-optional) 代替）。
:::

## 安装飞控模块

即插即用的飞控模块（如 [Holybro Pixhawk 5X](../flight_controller/pixhawk5x.md) 和 [Holybro Pixhawk 6X](../flight_controller/pixhawk6x.md)）可直接插入模块插槽。

对于外形尺寸不同的飞控模块，需要额外的接线。

## 安装RPi CM4 Companion

本节展示如何将RPi CM4安装/连接到底板上。

![图片显示分离的底板、底板盖、RPi、飞控和螺丝](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_disassembled.jpg)

要安装RPi CM4 Companion计算机:

1. 断开`FAN`线路。

   ![HB_Pixhawk_CM4_Fan](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_fan.jpg)

1. 移除底板背面的这4颗螺丝。

   ![板子底部显示角落固定盖板的螺丝](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_bottom.jpg)

1. 取下底板外壳，安装CM4，并使用4颗螺丝将其固定(如图所示):

   ![HB_Pixhawk_CM4_Screws](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_screws.jpg)

1. 重新安装外壳。

## 电源模块接线

PM03D电源模块随板提供。

RPi CM4和飞控必须分开供电：

- 飞控通过CLIK-Mate线缆连接至`POWER1`或`POWER2`端口供电
- RPi CM4通过`USB C`（CM4 Slave）连接供电。
  您也可以使用自备电源为RPi CM4底板供电。

下图更详细地展示了接线方式。

![Image showing writing from the PM03D power module to the baseboard](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_wiring_guide.jpg)

## 刷写RPi CM4

本节解释如何将您偏好的Linux发行版（如"Raspberry Pi OS 64bit"）安装到RPi EMCC上。

注意事项：

- 如果使用PX4，需要使用1.13.1或更高版本的PX4，以识别此底板。
- 风扇不会指示RPi CM4的电源/运行状态。
- 插入Power1/2的电源模块不会为RPi部分供电
  可使用PM03D电源模块提供的额外USB-C线缆连接到CM4从机USB-C端口。
- Micro-HDMI端口是输出端口。
- 未配备WiFi设备的RPi CM4板不会自动连接网络
  此时需要将其接入路由器，或在CM4主机端口插入兼容的WiFi扩展坞。

### 烧录 EMMC

将 RPi 镜像烧录到 EMMC 的操作步骤如下：

1. 将拨码开关设置为 `RPI` 模式

   ![](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/cm4_dip_switch.png)

1. 通过 USB-C _CM4 Slave_ 接口连接电脑，用于供电和烧录 RPi

   ![](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/cm4_usbc_slave_port.png)

1. 获取 `usbboot`，编译并运行它

   ```sh
   sudo apt install libusb-1.0-0-dev
   git clone --depth=1 https://github.com/raspberrypi/usbboot
   cd usbboot
   make
   sudo ./rpiboot
   ```

1. 现在可以使用 `rpi-imager` 安装您偏好的 Linux 发行版
   确保添加 WiFi 和 SSH 设置（隐藏在齿轮/高级符号后）

   ```sh
   sudo apt install rpi-imager
   rpi-imager
   ```

1. 完成后，拔下 USB-C CM4 Slave 接口（这将卸载卷，并关闭 CM4 电源）
1. 将拨码开关切换回 `EMMC` 模式
1. 通过 USB-C CM4 Slave 接口供电以启动 CM4
1. 要检查是否成功启动/运行，可以通过以下方式：
   - 检查 HDMI 输出是否正常
   - 通过 SSH 连接（如果已在 rpi-imager 中设置，并且 WiFi 可用）

## 配置 PX4 与 CM4 的 MAVLink 串口连接

::: info
如果使用 [以太网](#ethernet-connection-optional) 连接飞控和 RPi，此设置无需配置。
:::

Pixhawk飞控模块通过 [内部连接到 RPi CM4](#rpi-cm4-fc-serial-connection) 使用 `TELEM2` (`/dev/ttyS4`)。
飞控和 RPi CM4 都需要配置以通过该端口进行通信。

### FC串口设置

FC应默认正确连接到`TELEM2`端口。  
如果未正确连接，可以按照所示参数配置端口。

要在此FC上启用MAVLink实例：

1. 通过底板上标记为`FC`的USB Type C端口连接运行QGroundControl的计算机  

   ![显示FC USB-C连接器的底板图片](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_fc_usb_c.jpg)

1. [设置参数](../advanced_config/parameters.md):  

   - `MAV_1_CONFIG` = `102`  
   - `MAV_1_MODE` = `2`  
   - `SER_TEL2_BAUD` = `921600`  

1. 重启FC。

### RPi串口设置

在RPi侧：

1. 连接到RPi（使用WiFi、路由器或WiFi Dongle）。
1. 通过运行`RPi-config`启用RPi串口

   - 进入`3 Interface Options`，然后选择`I6 Serial Port`。
     选择以下选项：
     - `login shell accessible over serial → No`
     - `serial port hardware enabled` → `Yes`

1. 完成操作后，重启RPi。
   这将在`/boot/config.txt`中添加`enable_uart=1`，并从`/boot/cmdline.txt`中移除`console=serial0,115200`。
1. 现在MAVLink流量应可在`/dev/serial0`上以921600的波特率访问。

## 尝试使用 MAVSDK-Python

1. 确保 CM4 连接到互联网，例如通过 WiFi 或以太网。
1. 安装 MAVSDK Python：

   ```sh
   python3 -m pip install mavsdk
   ```

1. 从 [MAVSDK-Python 示例](https://github.com/mavlink/MAVSDK-Python/tree/main/examples) 复制一个示例。
1. 将 `system_address="udp://:14540"` 修改为 `system_address="serial:///dev/serial0:921600"`
1. 尝试运行示例。串口的权限应已通过 `dialout` 用户组提供。

## 以太网连接（可选）

飞控模块通过[与RPi CM4内部连接](#rpi-cm4-fc-serial-connection)从`TELEM2`（串口）进行通信。

您也可以使用提供的电缆在它们之间建立本地以太网连接。
以太网连接提供了比USB或其他串行连接更快、更可靠且更灵活的通信方式。

::: info
关于以太网的一般设置信息请参见：[PX4以太网设置](../advanced_config/ethernet_setup.md)。

此处的设置与此类似，不同之处在于我们为PX4使用了以下`netplan`配置：

```sh
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    eth0:
      addresses:
        - 10.41.10.1/24
      nameservers:
        addresses: [10.41.10.1]
      routes:
        - to: 10.41.10.0/24  # 本地路由以访问此子网上的设备
          scope: link        # 将作用域限制在本地子网
```

这将`eth0`设置为从RPi的本地以太网链路通道（而不是[以太网设置](../advanced_config/ethernet_setup.md#ubuntu-ethernet-network-setup)中假设的`enp2s0`）。

请注意我们也可以使用WiFi进行连接，但通过设置专用路由，我们保留了WiFi用于互联网通信。
:::

### 连接电缆

要在CM4与飞控之间建立本地以太网连接，需要使用提供的8针至4针连接器将两个以太网接口连接起来。

![HB_Pixhawk_CM4_Ethernet_Cable](../../assets/companion_computer/holybro_pixhawk_rpi_cm4_baseboard/baseboard_ethernet_cable.png)

电缆的针脚分配为：

| CM4 Eth 8 Pin | FC ETH 4 Pin |
| ------------- | ------------ |
| A             | B            |
| B             | A            |
| C             | D            |
| D             | C            |
| -             | N/A          |
| -             | N/A          |
| -             | N/A          |
| -             | N/A          |

### CM4的IP设置

由于在此配置中没有活动的DHCP服务器，需要手动设置IP地址：

首先通过SSH连接到CM4（可连接到CM4的WiFi或使用WiFi适配器）。
插入以太网线缆后，`eth0`网络接口似乎会从DOWN状态切换到UP状态。

可以使用以下命令检查状态：

```sh
ip address show eth0
```

也可以尝试手动启用：

```sh
sudo ip link set dev eth0 up
```

这会设置一个链路本地地址。
在本例中显示如下：

```sh
$: ip address show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether e4:5f:01:bf:e0:17 brd ff:ff:ff:ff:ff:ff
    inet 10.41.10.1/24 brd 10.41.10.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::e65f:1ff:febf:e017/64 scope link
       valid_lft forever preferred_lft forever
```

这意味着CM4的以太网IP地址是`10.41.10.1`。

#### Ping测试

首先从CM4向PX4发送Ping请求（使用PX4的默认地址）：

```sh
$ ping 10.41.10.2
```

应输出类似以下内容：

```sh
PING 10.41.10.2 (10.41.10.2) 56(84) bytes of data.
64 bytes from 10.41.10.2: icmp_seq=1 ttl=64 time=0.187 ms
64 bytes from 10.41.10.2: icmp_seq=2 ttl=64 time=0.109 ms
64 bytes from 10.41.10.2: icmp_seq=3 ttl=64 time=0.091 ms
^C
--- 10.41.10.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2049ms
rtt min/avg/max/mdev = 0.091/0.129/0.187/0.041 ms
```

:::info
如果此步骤失败，请检查是否启用了[防火墙](https://wiki.ubuntu.com/UncomplicatedFirewall)。
:::

然后从飞控向CM4发送Ping请求。
在Nuttx Shell中输入以下命令：

```sh
nsh> ping 10.41.10.1
```

应输出类似以下内容：

```sh
PING 10.41.10.1 56 bytes of data
56 bytes from 10.41.10.1: icmp_seq=0 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=1 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=2 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=3 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=4 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=5 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=6 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=7 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=8 time=0.0 ms
56 bytes from 10.41.10.1: icmp_seq=9 time=0.0 ms
10 packets transmitted, 10 received, 0% packet loss, time 10010 ms
rtt min/avg/max/mdev = 0.000/0.000/0.000/0.000 ms
```

#### MAVLink/MAVSDK 测试

为此，我们需要将MAVLink实例设置为发送流量到CM4的IP地址：

对于初始测试，我们可以执行：

```sh
mavlink start -o 14540 -t 10.41.10.1
```

这将通过UDP向端口14540（MAVSDK/MAVROS端口）发送MAVLink流量到该IP地址，意味着MAVSDK只需监听该默认端口上的任何UDP流量即可。

要运行MAVSDK示例，请通过pip安装mavsdk，并尝试来自[MAVSDK-Python/examples](https://github.com/mavlink/MAVSDK-Python/tree/main/examples)的示例。

#### XRCE-Client 以太网设置

接下来我们启用新以太网链路上的 `XRCE-DDS`。

你可以[修改所需的参数](../advanced_config/parameters.md)在QGroundControl参数编辑器中，或使用[MAVLINK shell](../debug/mavlink_shell.md)中的 `param set` 命令。
下面的设置假设你使用shell设置参数。

首先确保 `MAV_2_CONFIG` 没有配置为使用以太网端口（`1000`），因为这会与XRCE-DDS冲突（参见[在以太网上启用MAVLINK](../advanced_config/ethernet_setup.md#px4-mavlink-serial-port-configuration)）：

```sh
nsh>
param set MAV_2_CONFIG     0           # 当值为1000时改为0
```

然后在以太网端口启用uXRCE-DDS（参见[启动uXRCE-DDS客户端](../middleware/uxrce_dds.md#starting-the-client)）：

```sh
param set UXRCE_DDS_AG_IP  170461697   # 10.41.10.1的int32版本
param set UXRCE_DDS_CFG    1000        # 将uXRCE-DDS客户端的串口配置设置为以太网
param set UXRCE_DDS_DOM_ID 0           # 设置uXRCE-DDS域ID
param set UXRCE_DDS_KEY    1           # 设置uXRCE-DDS会话密钥
param set UXRCE_DDS_PRT    8888        # 设置uXRCE-DDS UDP端口
param set UXRCE_DDS_PTCFG  0           # 设置uXRCE-DDS参与者配置
param set UXRCE_DDS_SYNCC  0           # 禁用uXRCE-DDS系统时钟同步
param set UXRCE_DDS_SYNCT  1           # 启用uXRCE-DDS时间戳同步
```

然后运行Agent：

```sh
MicroXRCEAgent udp4 -p 8888
```

如果一切配置正确，预期输出如下：

```sh
[1731210063.537033] info     | UDPv4AgentLinux.cpp | init                     | running...             | port: 8888
[1731210063.538279] info     | Root.cpp           | set_verbose_level        | logger setup           | verbose_level: 4
[1731210066.577413] info     | Root.cpp           | create_client            | create                 | client_key: 0x00000001, session_id: 0x81
[1731210066.577515] info     | SessionManager.hpp | establish_session        | session established    | client_key: 0x00000001, address: 10.41.10.2:58900
[1731210066.583965] info     | ProxyClient.cpp    | create_participant       | participant created    | client_key: 0x00000001, participant_id: 0x001(1)
[1731210066.584754] info     | ProxyClient.cpp    | create_topic             | topic created          | client_key: 0x00000001, topic_id: 0x800(2), participant_id: 0x001(1)
[1731210066.584988] info     | ProxyClient.cpp    | create_subscriber        | subscriber created     | client_key: 0x00000001, subscriber_id: 0x800(4), participant_id: 0x001(1)
[1731210066.589864] info     | ProxyClient.cpp    | create_datareader        | datareader created     | client_key: 0x00000001, datareader_id: 0x800(6), subscriber_id: 0x800(4)
[1731210066.591007] info     | ProxyClient.cpp    | create_topic             | topic created          | client_key: 0x00000001, topic_id: 0x801(2), participant_id: 0x001(1)
[1731210066.591164] info     | ProxyClient.cpp    | create_subscriber        | subscriber created     | client_key: 0x00000001, subscriber_id: 0x801(4), participant_id: 0x001(1)
[1731210066.591912] info     | ProxyClient.cpp    | create_datareader        | datareader created     | client_key: 0x00000001, datareader_id: 0x801(6), subscriber_id: 0x801(4)
[1731210066.592701] info     | ProxyClient.cpp    | create_topic             | topic created          | client_key: 0x00000001, topic_id: 0x802(2), participant_id: 0x001(1)
[1731210066.592846] info     | ProxyClient.cpp    | create_subscriber        | subscriber created     | client_key: 0x00000001, subscriber_id: 0x802(4), participant_id: 0x001(1)
[1731210066.593640] info     | ProxyClient.cpp    | create_datareader        | datareader created     | client_key: 0x00000001, datareader_id: 0x802(6), subscriber_id: 0x802(4)
[1731210066.597046] info     | ProxyClient.cpp    | create_datareader        | datareader created     | client_key: 0x00000001, datareader_id: 0x804(6), subscriber_id: 0x804(4)
```

## 另请参阅

- [通过Holybro与PX4的对话获取Pixhawk Raspberry Pi CM4底板](https://px4.io/get-the-pixhawk-raspberry-pi-cm4-baseboard-by-holybro-talking-with-px4/) (px4.io blog):
  - 教程展示了如何通过有线以太网将Pixhawk 6X + Raspberry Pi连接到CM4底板。