# PX4以太网设置

以太网连接提供了比USB或其他串口连接更快速、可靠和灵活的通信方式。

可用于连接地面站、伴飞计算机和其他MAVLink系统。当连接使用原生以太网的系统时（例如IP无线电），特别推荐该方式。

本主题涵盖以下内容：

- [PX4以太网设置](#px4-ethernet-setup)
  - [支持的飞控](#supported-flight-controllers)
  - [设置以太网网络](#setting-up-the-ethernet-network)
    - [PX4以太网网络设置](#px4-ethernet-network-setup)
    - [Ubuntu以太网网络设置](#ubuntu-ethernet-network-setup)
    - [伴飞计算机以太网网络设置](#companion-computer-ethernet-network-setup)
  - [PX4 MAVLink串口配置](#px4-mavlink-serial-port-configuration)
  - [QGroundControl设置示例](#qgroundcontrol-setup-example)
  - [MAVSDK-Python设置示例](#mavsdk-python-setup-example)
  - [ROS 2设置示例](#ros-2-setup-example)

## 支持的飞行控制器

PX4 支持在具有以太网端口的 [Pixhawk 5X-standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-011%20Pixhawk%20Autopilot%20v5X%20Standard.pdf) 飞行控制器（及后续版本）上使用以太网连接。  
其他开发板也可能支持此功能。

支持的飞行控制器包括：

- [CUAV Pixhawk V6X](../flight_controller/cuav_pixhawk_v6x.md)
- [Holybro Pixhawk 5X](../flight_controller/pixhawk5x.md)
- [Holybro Pixhawk 6X](../flight_controller/pixhawk6x.md)
- [RaccoonLab FMUv6X Autopilot](../flight_controller/raccoonlab_fmu6x.md)

## 设置以太网网络

要通过以太网连接系统，需要将它们配置在同一个IP网络中，确保每个系统具有唯一的IP地址并能发现其他系统。这可以通过DHCP服务器分配地址，或手动配置网络中每个系统的地址来实现。

我们无法提供适用于本地网络的通用"开箱即用"配置。因此，以下以在IP网络中使用静态地址范围`10.41.10.Xxx`为例进行说明：PX4默认静态地址为`10.41.10.2`，计算机地址为`10.41.10.1`。若要连接辅助计算机或其他系统，可采用类似方法分配静态地址。

::: info
网络配置本身并无特殊之处（除非修改网络设置所使用的工具），其工作原理与任何家庭或企业网络相似。因此了解IP网络的工作原理非常重要！
:::

### PX4以太网网络设置

<!-- NuttX网络管理器信息: https://github.com/PX4/PX4-Autopilot/pull/16330 -->

PX4使用 [netman](../modules/modules_system.md#netman) 模块应用和更新网络设置。

默认配置首先从DHCP请求IP地址，若失败则回退到默认静态地址`10.41.10.2`。您可显式设置任何静态IP地址（包括默认地址），以跳过初始DHCP检查并加快连接速度。

::: info
若要使用PX4的默认静态IP地址，可直接跳转到下一节。
:::

网络设置定义在SD卡的配置文件`/fs/microsd/net.cfg`中。这是一个文本文件，每行定义一个`name=value`参数对。配置文件示例如下：

```ini
DEVICE=eth0
BOOTPROTO=fallback
IPADDR=10.41.10.2
NETMASK=255.255.255.0
ROUTER=10.41.10.254
DNS=10.41.10.254
```

其中参数含义为：

- `DEVICE`: 接口名称，默认为`eth0`。
- `BOOTPROTO`: 获取PX4 IP地址的协议。有效值为：`dhcp`（动态获取）、`static`（静态地址）、`fallback`（先尝试DHCP，失败后回退到静态地址）。
- `IPADDR`: 静态IP地址（当BOOTPROTO为`static`或`fallback`时使用）。
- `NETMASK`: 网络掩码。
- `ROUTER`: 默认路由地址。
- `DNS`: DNS服务器地址。

通过_QGroundControl_设置上述示例配置的步骤：

1. 用USB线将飞控连接到计算机。
1. 打开 **QGroundcontrol > Analyze Tools > MAVLink Console**
1. 在_MA VLink Console_中输入类似以下命令（将值写入配置文件）：

   ```sh
   echo DEVICE=eth0 > /fs/microsd/net.cfg
   echo BOOTPROTO=fallback >> /fs/microsd/net.cfg
   echo IPADDR=10.41.10.2 >> /fs/microsd/net.cfg
   echo NETMASK=255.255.255.0 >>/fs/microsd/net.cfg
   echo ROUTER=10.41.10.254 >>/fs/microsd/net.cfg
   echo DNS=10.41.10.254 >>/fs/microsd/net.cfg
   ```

1. 设置完成后断开USB连接。
1. 重启飞控以应用设置。

注意：上述设置为飞控分配了以太网地址。您还需[配置以太网端口](#px4-mavlink-serial-port-configuration)使用MAVLink。

### Ubuntu以太网网络设置

若地面站（或辅助计算机）使用Ubuntu，可通过 [netplan](https://netplan.io/) 配置网络。以下示例配置文件`/etc/netplan/01-network-manager-all.yaml`将与上述PX4配置在相同网络中运行。更多示例和说明请参考[netplan](https://netplan.io/)文档。

Ubuntu计算机设置步骤：

1. 在终端中创建并打开`netplan`配置文件：`/etc/netplan/01-network-manager-all.yaml`
   以下示例使用_nano_文本编辑器：

   ```
   sudo nano /etc/netplan/01-network-manager-all.yaml
   ```

1. 将以下配置信息复制粘贴到文件中（注意缩进非常重要）：

   ```
   network:
     version: 2
     renderer: NetworkManager
     ethernets:
         enp2s0:
             addresses:
                 - 10.41.10.1/24
             nameservers:
                 addresses: [10.41.10.1]
             routes:
                 - to: 10.41.10.1
                   via: 10.41.10.1
   ```

   保存并退出编辑器。

1. 在Ubuntu终端中输入以下命令应用_netplan_配置：

   ```
   sudo netplan apply
   ```

### 辅助计算机以太网网络设置

辅助计算机的设置取决于其操作系统。Linux系统可能支持`netplan`，此时设置方式与上述相同，但需使用唯一IP地址。

## PX4 MAVLink 串口配置

以太网端口配置设置 _串行链接_ 的属性（PX4 将以太网连接视为串行链接）。  
这包括流式传输的 MAVLink 消息集、数据速率、远程系统可监听的 UDP 端口等。

::: info
必须单独配置 PX4 的 IP 地址和其他 _网络设置_ ([如前所示](#px4-ethernet-network-setup))。
:::

PX4 使用以下参数配置串口以通过 MAVLink 连接到地面站：

| 参数 | 值 | 描述 |
| ---- | -- | ---- |
| [MAV_2_CONFIG](../advanced_config/parameter_reference.md#MAV_2_CONFIG) | 1000 | 配置以太网端口 |
| [MAV_2_BROADCAST](../advanced_config/parameter_reference.md#MAV_2_BROADCAST) | 1 | 广播 `HEARTBEAT` 消息 |
| [MAV_2_MODE](../advanced_config/parameter_reference.md#MAV_2_MODE) | 0 | 发送标准 MAVLink 消息集（即地面站集） |
| [MAV_2_RADIO_CTL](../advanced_config/parameter_reference.md#MAV_2_RADIO_CTL) | 0 | 禁用 MAVLink 流量的软件限速 |
| [MAV_2_RATE](../advanced_config/parameter_reference.md#MAV_2_RATE) | 100000 | 最大发送速率 |
| [MAV_2_REMOTE_PRT](../advanced_config/parameter_reference.md#MAV_2_REMOTE_PRT) | 14550 | 远程端口 14550（地面站） |
| [MAV_2_UDP_PRT](../advanced_config/parameter_reference.md#MAV_2_UDP_PRT) | 14550 | 网络端口 14550（地面站） |

通常伴飞计算机使用端口 `14540`（而非 `14550`），并流式传输 `Onboard` 配置文件指定的 MAVLink 消息集。  
可以通过将 [MAV_2_REMOTE_PRT](../advanced_config/parameter_reference.md#MAV_2_REMOTE_PRT) 和 [MAV_2_UDP_PRT](../advanced_config/parameter_reference.md#MAV_2_UDP_PRT) 改为 `14540`，以及将 [MAV_2_MODE](../advanced_config/parameter_reference.md#MAV_2_MODE) 改为 `2`（Onboard）来配置此设置。  
但请注意，此配置仍会使用地面站配置文件进行工作。

有关 MAVLink 串口配置的更多信息，请参见 [MAVLink Peripherals (GCS/OSD/Companion)](../peripherals/mavlink_peripherals.md)

## QGroundControl 设置示例

假设您已 [设置以太网网络](#setting-up-the-ethernet-network) 使地面站计算机和 PX4 运行在同一个网络上，并且

要通过以太网将 QGroundControl 连接到 PX4：

1. [设置以太网网络](#setting-up-the-ethernet-network) 使地面站计算机和 PX4 运行在同一个网络上。
1. 使用以太网电缆将地面站计算机和 PX4 连接。
1. 启动 QGroundControl 并 [定义通信链接](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/settings_view.html) (**Application Settings > Comm Links**)，分别指定 _服务器地址_ 和端口为 PX4 中分配的 IP 地址和端口。

   假设值设置如本主题其余部分所述，设置将如下所示：

   ![QGC 通信链接的以太网设置](../../assets/qgc/settings/comm_link/px4_ethernet_link_config.png)

1. 如果选择此链接，QGroundControl 应该会连接。

::: info
[PX4 以太网端口配置](#px4-ethernet-network-setup) 不应需要（默认值对地面站是合适的）。
:::

## MAVSDK-Python 设置示例

要在伴飞计算机上设置运行 MAVSDK-Python：

1. [设置以太网网络](#setting-up-the-ethernet-network) 使伴飞计算机和 PX4 使用相同网络。
1. 修改 [PX4 以太网端口配置](#px4-ethernet-network-setup) 以连接到伴飞计算机。
   您可能需要将参数 [MAV_2_REMOTE_PRT](../advanced_config/parameter_reference.md#MAV_2_REMOTE_PRT) 和 [MAV_2_UDP_PRT](../advanced_config/parameter_reference.md#MAV_2_UDP_PRT) 设置为 `14540`，并将 [MAV_2_MODE](../advanced_config/parameter_reference.md#MAV_2_MODE) 设置为 `2` (Onboard)。
1. 按照 [MAVSDK-python](https://github.com/mavlink/MAVSDK-Python) 中的说明安装和使用 MAVSDK。

   例如，您的代码将通过以下方式连接到 PX4：

   ```python
   await drone.connect(system_address="udp://10.41.10.2:14540")
   ```

::: info
如果未修改 PX4 以太网端口配置，MAVSDK 可以通过端口 `14550` 连接到 PX4。
不过不建议这样做，因为默认配置是针对与 GCS 通信优化的（而非伴飞计算机）。
:::

## ROS 2 设置示例

::: 信息 先决条件:

- 您使用的飞控硬件支持 PX4 固件，并包含 [uXRCE-DDS](../middleware/uxrce_dds.md) 中间件。
  注意 PX4 v1.14 及更高版本默认包含所需的 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 模块。
- [ROS 2](../ros2/user_guide.md) 已正确在计算单元上配置。
- 您已按照本页面顶部讨论的以太网网络和端口设置进行了操作。
  :::

要设置 ROS 2：

1. 通过以太网将飞控与计算单元连接。
2. [在 PX4 上启动 uXRCE-DDS 客户端](../middleware/uxrce_dds.md#starting-the-client)，可手动操作或自定义系统启动脚本。
   注意您必须使用计算单元的 IP 地址和代理监听的 UDP 端口（上述示例配置将计算单元 IP 地址设为 `10.41.10.1`，下一阶段将代理 UDP 端口设为 `8888`）。
3. [在计算单元上启动 micro XRCE-DDS 代理](../middleware/uxrce_dds.md#starting-the-agent)。
   例如，在终端中输入以下命令以在 UDP 端口 `8888` 上启动代理：

   ```sh
   MicroXRCEAgent udp4 -p 8888
   ```

4. 在新终端中运行 [监听节点](../ros2/user_guide.md#running-the-example) 以确认连接已建立：

   ```sh
   source ~/ws_sensor_combined/install/setup.bash
   ros2 launch px4_ros_com sensor_combined_listener.launch.py
   ```

   如果设置正确，终端应显示以下输出：

   ```sh
   RECEIVED SENSOR COMBINED DATA
   =============================
   ts: 855801598
   gyro_rad[0]: -0.00339938
   gyro_rad[1]: 0.00440091
   gyro_rad[2]: 0.00513893
   gyro_integral_dt: 4997
   accelerometer_timestamp_relative: 0
   accelerometer_m_s2[0]: -0.0324082
   accelerometer_m_s2[1]: 0.0392213
   accelerometer_m_s2[2]: -9.77914
   accelerometer_integral_dt: 4997
   ```

## 参见

- [Get The Pixhawk Raspberry Pi CM4 Baseboard By Holybro Talking With PX4](https://px4.io/get-the-pixhawk-raspberry-pi-cm4-baseboard-by-holybro-talking-with-px4/) (px4.io blog):  
  - 教程展示如何通过有线以太网将 Pixhawk 6X + Raspberry Pi 连接到 CM4 主板。  
  - 博客重复了本主题的大量内容。