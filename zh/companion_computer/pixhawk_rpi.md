# Raspberry Pi 伴飞电脑与 Pixhawk 的连接

本文介绍如何在 Linux Ubuntu 系统上配置运行 [ROS 2](../ros2/user_guide.md) 的 Raspberry Pi（"RPi"）伴飞电脑，通过 Pixhawk 的 `TELEM2` 接口与 RPi 的 TX/RX 引脚之间的串口连接，与 [Pixhawk](../flight_controller/autopilot_pixhawk_standard.md) 飞控进行通信。

这些配置方法可直接扩展到其他 RPi 与飞控的组合配置中。

::: info
RPi 与 Pixhawk 的其他常见连接方式包括：

- RPi 与 Pixhawk 的以太网连接。
  基于 FMUv5x、FMUv6x 及后续版本的 Pixhawk 控制器可能内置以太网接口。
  详情请参见 [PX4 以太网 > 支持的控制器](../advanced_config/ethernet_setup.md#supported-flight-controllers)。
- 通过 RPi USB 接口的串口连接。
  这种方式简单可靠，但需要额外的 FTDI Chip USB 转串口适配板。
  该选项详见 [Pixhawk 伴飞电脑 > 串口配置](../companion_computer/pixhawk_companion.md#serial-port-setup)。
  :::

## 接线

### 串口连接

首先在RPi和PX4之间接好用于外部控制的串口连接。

该设置连接Pixhawk的`TELEM2`端口，通常推荐用于外部控制。
该端口在PX4中最初配置为使用MAVLink，后续在设置ROS 2时会进行更改。
Pixhawk的端口可以位于飞控的任何位置，但通常都有明确的标识，具体位置应参考你的[飞控](../flight_controller/index.md)文档。

将Pixhawk `TELEM2`的`TX`/`RX`/`GND`引脚连接到RPi GPIO板上的对应`RXD`/`TXD`/`Ground`引脚：

| PX4 TELEM2引脚 | RPi GPIO引脚           |
| -------------- | ---------------------- |
| UART5_TX (2)   | RXD (GPIO 15 - pin 10) |
| UART5_RX (3)   | TXD (GPIO 14 - pin 8)  |
| GND (6)        | Ground (pin 6)         |

图中左侧为Pixhawk `TELEM2`端口引脚，右侧为RPi GPIO板引脚。
`TELEM2`端口的引脚通常按从右到左的顺序编号。

| `TELEM2`                                                                                                      | RPi GPIO                                                      |
| ------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| ![引脚编号显示最左侧引脚为1号](../../assets/companion_computer/pixhawk_rpi/pins_numbers.png) | ![](../../assets/companion_computer/pixhawk_rpi/rpi_gpio.png) |

::: info
几乎所有近期的Pixhawk板，如Pixhawk-6C，都使用Pixhawk连接器标准中定义的相同连接器和引脚号。
你可以通过检查具体板卡文档确认引脚布局。

标准`TELEM2`引脚分配如下：

| 引脚      | 信号          | 电压   |
| --------- | --------------- | ------- |
| 1 (红色)   | VCC             | +5V     |
| 2 (黑色)   | UART5_TX (输出) | +3.3V   |
| 3 (黑色)   | UART5_RX (输入) | +3.3V   |
| 4 (黑色)   | UART5_CTS (输入) | +3.3V   |
| 5 (黑色)   | UART5_RTS (输出) | +3.3V   |
| 6 (黑色)   | GND             | GND     |

:::

### TELEM1/遥测电台

Pixhawk的`TELEM1`端口预先配置为通过遥测电台使用MAVLink连接GCS。

你可以将[合适的电台](../telemetry/index.md)插入Pixhawk的`TELEM1`端口，大多数情况下即可直接使用。
通常其他电台需要连接到地面站USB端口。
如果遇到问题，请查看电台文档。

### 电源供应

Pixhawk板通常需要稳定的5V直流电源，通常通过[电源模块和/或电源分配板](../power_module/index.md)从LiPO电池供应到标记为`POWER`（或类似）的端口。

你的飞控说明通常会解释推荐的设置方式。
例如：

- [Holybro Pixhawk 6C > 电压规格](../flight_controller/pixhawk6c.md#voltage-ratings)
- [Holybro Pixhawk 6C 接线快速入门 > 电源](../assembly/quick_start_pixhawk6c.md#power)

Pixhawk控制器可以为少量低功耗外设供电，例如GPS模块和低范围遥测电台。
RPi伴飞计算机、舵机、高功率电台和其他外设需要独立电源，通常通过电池消除电路（BEC）连接到同一电池或另一电池。
某些电源模块包含单独的BEC。

:::warning
过度负载你的Pixhawk很容易将其损坏。
:::

::: info
在PX4设置和配置期间，通过地面站笔记本电脑的USB连接为Pixhawk板供电已足够，而你的伴飞计算机可能通过桌面充电器供电。
:::

## PX4 设置

本指南适用于 PX4 v1.14 及更高版本。

如果需要更新固件，请通过 `USB` 接口将 Pixhawk 连接到笔记本电脑/台式机，然后使用 QGroundControl 按照 [Firmware > Install Stable PX4](../config/firmware.md#install-stable-px4) 的说明更新固件。  
若需要最新的开发者版本，请按照 [Firmware > Installing PX4 Master, Beta or Custom Firmware](../config/firmware.md#installing-px4-main-beta-or-custom-firmware) 的说明将固件更新到 "main" 版本。

::: info
您也可以选择 [setup a development environment](../dev_setup/dev_env.md)，[build](../dev_setup/building_px4.md#building-for-nuttx) 并 [upload](../dev_setup/building_px4.md#uploading-firmware-flashing-the-board) 固件。
:::

<!-- Keeping this line as record - this is only unexpected dependency:
```
sudo apt -y install stlink-tools
```
-->
<!-- Keeping this because we might need it for updating linux instructions
在 Linux 系统上，USB 连接的默认名称是 `/dev/ttyACM0`：

```
sudo chmod a+rw /dev/ttyACM0
cd /PX4-Autopilot
make px4_fmu-v6c_default upload
```
-->## 在RPi上安装Ubuntu

以下步骤展示如何在RPi上安装和设置Ubuntu 22.04。  
注意ROS 2版本与特定Ubuntu版本对应。  
我们使用Ubuntu 22.04以适配ROS 2 "Humble"，若您使用ROS 2 "Foxy"则需安装Ubuntu 20.04。

首先将Ubuntu安装到RPi：

1. 按照官方教程准备Ubuntu 22.04可引导的Ubuntu桌面版SD卡：[如何在Raspberry Pi 4上安装Ubuntu桌面版](https://ubuntu.com/tutorials/how-to-install-ubuntu-desktop-on-raspberry-pi-4#1-overview)  
1. 连接鼠标、键盘和显示器，并将RPi连接到5V电源（外部电源/充电器）。  
1. 将SD卡插入RPi并开机从SD卡启动。  
1. 按照屏幕上的提示安装Ubuntu。

在终端中依次执行以下命令以配置RPi的Ubuntu：

1. 安装 `raspi-config`：

   ```sh
   sudo apt update
   sudo apt upgrade
   sudo apt-get install raspi-config
   ```

1. 启动 `raspi-config`：

   ```sh
   sudo raspi-config
   ```

1. 进入 **Interface Option** 并选择 **Serial Port**。

   - 选择 **No** 禁用串口登录外壳。  
   - 选择 **Yes** 启用串口接口。  
   - 点击 **Finish** 并重启RPi。

1. 使用 `nano` 编辑RPi的固件引导配置文件：

   ```sh
   sudo nano /boot/firmware/config.txt
   ```

1. 在文件末尾添加以下内容（在最后一行之后）：

   ```sh
   enable_uart=1
   dtoverlay=disable-bt
   ```

1. 保存文件并重启RPi。

   - 在 `nano` 中可通过以下快捷键组合保存文件：**ctrl+x**，**ctrl+y**，**Enter**。

1. 验证串口是否可用。  
   此处使用以下终端命令列出串口设备：

   ```sh
   cd /
   ls /dev/ttyAMA0
   ```

   命令结果应包含接收/发送连接 `/dev/ttyAMA0`（注意该串口也以 `/dev/serial0` 形式存在）。

RPi现已配置完成，可与RPi通过 `/dev/ttyAMA0` 串口进行通信。  
请注意，后续章节中我们将安装更多用于MAVLink和ROS 2的软件。

## MAVLink 通信

[MAVLink](https://mavlink.io/en/) 是 PX4 的默认且稳定的通信接口。
运行在伴飞计算机上的 MAVLink 应用可以通过 RPi 上设置的 `/dev/ttyAMA0` 串口连接，并默认自动连接到 Pixhawk 的 `TELEM 2` 接口。

PX4 推荐使用 [MAVSDK](https://mavsdk.mavlink.io/main/en/index.html) 编写 MAVLink 伴飞计算机应用，因为它为多种编程语言提供了操作常用 MAVLink 服务的简单 API。
你也可以使用 [MAVLink](https://mavlink.io/en/#mavlink-project-generatorslanguages) 提供的库，例如 [Pymavlink](https://mavlink.io/en/mavgen_python/)，但需要自行实现部分微服务。

本教程不会深入讲解 MAVLink 控制（相关 SDK 已有详细说明）。
不过我们将安装并使用一个简单的开发者 MAVLink 地面站 `mavproxy`，
用于验证 MAVLink 连接以及物理连接是否正确配置。
MAVSDK 和其他 MAVLink 应用的连接方式与此类似。

首先检查 Pixhawk 的 `TELEM 2` 配置：

1. 使用 USB 线将 Pixhawk 连接到笔记本电脑。
1. 打开 QGroundControl（机体应成功连接）。
1. 在 QGroundControl 中[检查/修改以下参数](../advanced_config/parameters.md)：

   ```ini
   MAV_1_CONFIG = TELEM2
   UXRCE_DDS_CFG = 0 (Disabled)
   SER_TEL2_BAUD = 57600
   ```

   注意这些参数可能已经配置正确。
   关于串口和 MAVLink 配置的更多信息，请参见 [串口配置](../peripherals/serial_configuration.md) 和 [MAVLink 外设](../peripherals/mavlink_peripherals.md)。

然后通过以下终端命令在 RPi 上安装和配置 MAVProxy：

1. 安装 MAVProxy：

   ```sh
   sudo apt install python3-pip
   sudo pip3 install mavproxy
   sudo apt remove modemmanager
   ```

1. 运行 MAVProxy，设置连接端口为 `/dev/ttyAMA0` 且波特率与 PX4 匹配：

   ```sh
   sudo mavproxy.py --master=/dev/serial0 --baudrate 57600
   ```

   ::: info
   上述命令中使用了 `/dev/serial0`，但同样可以使用 `/dev/ttyAMA0`。
   如果通过 USB 连接，则需要将端口设置为 `/dev/ttyACM0`：

   ```sh
   sudo chmod a+rw /dev/ttyACM0
   sudo mavproxy.py --master=/dev/ttyACM0 --baudrate 57600
   ```

   :::

RPi 上的 MAVProxy 现在应通过 RX/TX 引脚连接到 Pixhawk。
你应该能在 RPi 终端中看到连接状态。

这表示我们已验证连接配置正确。
在下一节中，我们将配置 Pixhawk 和 RPi 使用 uXRCE-DDS 和 ROS2 替代 MAVLink。

## ROS 2 和 uXRCE-DDS

[ROS 2 指南](../ros2/user_guide.md) 和 [uXRCE-DDS](../middleware/uxrce_dds.md) 页面涵盖了 uXRCE-DDS 和 ROS 的配置选项，重点介绍了 ROS 2 "Foxy"。
本教程使用 ROS 2 "Humble" 并涵盖与 RPi 配合使用的具体设置。
建议同时阅读这两部分内容！

### Pixhawk/PX4 配置

接下来我们将使用 ROS 2 替代 MAVLink 在 `TELEM2` 上进行配置。
通过 QGroundControl 修改参数即可完成，QGroundControl 可通过 USB 连接，或通过连接到 `TELEM1` 的数传电台连接。

配置步骤如下：

1. 使用 USB 线将 Pixhawk 连接到笔记本电脑并打开 QGroundControl（若尚未连接）。
1. 在 QGroundControl 中[检查/修改以下参数](../advanced_config/parameters.md)：

   ```ini
   MAV_1_CONFIG = 0 (Disabled)
   UXRCE_DDS_CFG = 102 (TELEM2)
   SER_TEL2_BAUD = 921600
   ```

   [MAV_1_CONFIG=0](../advanced_config/parameter_reference.md#MAV_1_CONFIG) 和 [UXRCE_DDS_CFG=102](../advanced_config/parameter_reference.md#UXRCE_DDS_CFG) 分别用于禁用 TELEM2 上的 MAVLink 并启用 TELEM2 上的 uXRCE-DDS 客户端。
   `SER_TEL2_BAUD` 参数设置通信链路的数据速率。  
   你也可以通过 `MAV_1_CONFIG` 或 `MAV_0_CONFIG` 配置连接到 `TELEM1`。

   ::: info
   要应用这些参数的更改，需要重启飞控器。
   :::

1. 检查 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 模块是否正在运行。
   可以通过在 QGroundControl 的 [MAVLink 控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html) 中运行以下命令验证：

   ```sh
   uxrce_dds_client status
   ```

::: info
如果客户端模块未运行，可以在 MAVLink 控制台中手动启动：

```sh
uxrce_dds_client start -t serial -d /dev/ttyS3 -b 921600
```

注意 `/dev/ttyS3` 是 [Holybro Pixhawk 6c](../flight_controller/pixhawk6c.md#serial-port-mapping) 上 TELEM2 的 PX4 端口。
对于其他飞控器，请查看其概述页面中的串口映射部分。
:::

### RPi 上的 ROS 配置

在 RPi 上配置 ROS 2 和 Micro XRCE-DDS Agent 的步骤如下：

1. 按照[官方教程](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html)安装 ROS 2 Humble。
2. 通过 RPi 终端安装 git：

   ```sh
   sudo apt install git
   ```

3. 安装 uXRCE_DDS 代理：

   ```sh
   git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
   cd Micro-XRCE-DDS-Agent
   mkdir build
   cd build
   cmake ..
   make
   sudo make install
   sudo ldconfig /usr/local/lib/
   ```

   参见 [uXRCE-DDS > Micro XRCE-DDS 代理安装](../middleware/uxrce_dds.md#micro-xrce-dds-agent-installation) 获取代理的其他安装方式。

4. 在 RPi 终端中启动代理：

   ```sh
   sudo MicroXRCEAgent serial --dev /dev/serial0 -b 921600
   ```

   注意我们使用了之前设置的串口和与 PX4 相同的波特率。

当代理和客户端都在运行时，你应该能在 MAVLink 控制台和 RPi 终端中看到活动。
可以在 RPi 上通过以下命令查看可用主题：

```sh
source /opt/ros/humble/setup.bash
ros2 topic list
```

至此配置完成。
当连接正常工作后，参见 [ROS 2 指南](../ros2/user_guide.md) 了解更多关于 PX4 和 ROS 2 配合使用的详细信息。