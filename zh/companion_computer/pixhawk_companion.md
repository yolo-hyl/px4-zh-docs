# 使用协处理器与Pixhawk控制器

PX4在Pixhawk系列飞控上运行时，可通过任何可用的可配置串口连接到协处理器，包括以太网口（如果支持）。

详见[协处理器](../companion_computer/index.md)了解支持的硬件和通用设置。

## 协处理器软件

协处理器需要运行可与飞控通信的软件，并将流量路由到地面站和云端。

常见选项列在[协处理器 > 协处理器设置](../companion_computer/index.md#companion-computer-software)中。

## 以太网设置

如果飞控支持，推荐使用以太网连接。
详见[以太网设置](../advanced_config/ethernet_setup.md)获取操作指南。

## 串口设置

这些说明解释了在不使用以太网时如何设置连接。

### Pixhawk配置

PX4期望协处理器通过`TELEM2`进行外部控制连接。
该端口默认配置为使用MAVLink接口。

如果使用MAVLink，通常无需其他PX4侧配置。
要在其他端口使用MAVLink，或在`TELEM2`上禁用它，参见[MAVLink外设（GCS/OSD/协处理器）](../peripherals/mavlink_peripherals.md)和[串口配置](../peripherals/serial_configuration.md)。

要在`TELEM2`上使用[ROS 2/uXRCE-DDS](../ros2/user_guide.md)替代MAVLink，请在端口上禁用MAVLink，然后在`TELEM2`上启用uXRCE-DDS客户端（参见[uXRCE-DDS > 启动客户端](../middleware/uxrce_dds.md#starting-the-client)）。

### 串口硬件设置

如果通过串口连接，请按照以下说明接线。
所有Pixhawk串口均工作在3.3V，且兼容5V电平。

:::warning
许多现代协处理器的硬件UART仅支持1.8V电平，3.3V电平可能导致损坏。
请使用电平转换器。
通常可用的硬件串口已关联其他功能（调制解调器或控制台），需要在Linux中重新配置后才能使用。
:::

一种安全且易于设置的方案是使用FTDI芯片USB转串口适配板，将Pixhawk的`TELEM2`连接到协处理器的USB口。
`TELEM2`到FTDI的接线映射如下：

| TELEM2 |           | FTDI | &nbsp;                 |
| ------ | --------- | ---- | ---------------------- |
| 1      | +5V (红)  |      | 请勿连接！             |
| 2      | Tx (出)   | 5    | FTDI RX (黄) (入)      |
| 3      | Rx (入)   | 4    | FTDI TX (橙) (出)      |
| 4      | CTS (入)  | 6    | FTDI RTS (绿) (出)     |
| 5      | RTS (出)  | 2    | FTDI CTS (棕) (入)     |
| 6      | GND       | 1    | FTDI GND (黑)          |

您也可能直接将`TELEM2`连接到协处理器的串口。
树莓派的示例见[Raspberry Pi协处理器与Pixhawk](../companion_computer/pixhawk_rpi.md)。

### Linux下的USB串口软件设置

在Linux中，USB FTDI的默认名称类似`\dev\ttyUSB0`。
如果USB上连接了第二个FTDI或Arduino，会注册为`\dev\ttyUSB1`。
为避免插拔顺序导致的混淆，建议根据USB设备的供应商和产品ID创建从`ttyUSBx`到友好名称的符号链接。

使用`lsusb`可获取供应商和产品ID。

```sh
$ lsusb

Bus 006 Device 002: ID 0bda:8153 Realtek Semiconductor Corp.
Bus 006 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 005 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 004 Device 002: ID 05e3:0616 Genesys Logic, Inc.
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 004: ID 2341:0042 Arduino SA Mega 2560 R3 (CDC ACM)
Bus 003 Device 005: ID 26ac:0011
Bus 003 Device 002: ID 05e3:0610 Genesys Logic, Inc. 4-port hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 001 Device 002: ID 0bda:8176 Realtek Semiconductor Corp. RTL8188CUS 802.11n WLAN Adapter
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Arduino是`Bus 003 Device 004`，Pixhawk是`Bus 003 Device 005`。

::: info
如果无法找到设备，请尝试拔插USB设备。
:::

在`/etc/udev/rules.d/99-pixhawk.rules`中创建规则文件：

```sh
SUBSYSTEM=="tty", ATTRS{idVendor}=="26ac", ATTRS{idProduct}=="0011", SYMLINK+="pixhawk"
SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0042", SYMLINK+="arduino"
```

然后重新加载规则：

```sh
sudo udevadm control --reload-rules
```

现在您可以通过`/dev/pixhawk`或`/dev/arduino`访问设备。