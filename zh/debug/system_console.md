# PX4 系统控制台

PX4 的 _System Console_（系统控制台）提供对系统的底层访问、调试输出以及系统启动过程的分析功能。

:::tip
如果系统无法启动，应使用控制台进行调试。
否则，[MAVLink Shell](../debug/mavlink_shell.md) 可能更合适，因为它的设置更简单，且可用于[许多相同任务](../debug/consoles.md#console_vs_shell)。
:::

## 控制台连接

控制台通过一个（板载特定的）UART 实现，该 UART 可通过 [3.3V FTDI](https://www.digikey.com/en/products/detail/TTL-232R-3V3/768-1015-ND/1836393) 电缆连接到计算机的 USB 端口。
这允许通过终端应用程序访问控制台。

Pixhawk 控制器制造商应通过符合 [Pixhawk Connector Standard](#pixhawk_debug_port) 的专用 _调试端口_ 暴露控制台 UART 和 SWD（JTAG）调试接口。
不幸的是，一些板载设备早于该标准推出或不符合标准。

::: info
针对多种不同板载设备的开发者，可能希望使用 [debug adapter](../debug/swd_debug.md#debug-adapters) 来简化板载设备与 FTDI 电缆和 [debug probes](../debug/swd_debug.md#debug-probes-for-px4-hardware) 的连接。
:::

以下部分概述/链接了许多常见板载设备的连接和系统控制台信息。

### 板载特定连接

系统控制台 UART 引脚/调试端口通常在 [autopilot overview pages](../flight_controller/index.md) 中有文档说明（部分链接如下）：

- [3DR Pixhawk v1 Flight Controller](../flight_controller/pixhawk.md#console-port)（同样适用于
  [mRo Pixhawk](../flight_controller/mro_pixhawk.md#debug-ports)，[Holybro pix32](../flight_controller/holybro_pix32.md#debug-port))
- [Pixhawk 3](../flight_controller/pixhawk3_pro.md#debug-port)
- [Pixracer](../flight_controller/pixracer.md#debug-port)

<a id="pixhawk_debug_port"></a>

### Pixhawk 调试端口

Pixhawk 飞行控制器通常配备符合 [Pixhawk Connector Standard Debug Port](../debug/swd_debug.md#pixhawk-connector-standard-debug-ports) 的调试端口，该端口为 10 针 [Pixhawk Debug Full](../debug/swd_debug.md#pixhawk-debug-full) 或 6 针 [Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini) 端口。

这些端口包含用于连接 FTDI 电缆的控制台 TX 和 RX 引脚。
[Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini) 到 FTDI 的引脚映射如下：

| Pixhawk 调试端口 | -                        | FTDI | -                                 |
| ------------------ | ------------------------ | ---- | --------------------------------- |
| 1 (red)            | TARGET PROCESSOR VOLTAGE |      | N/C (used for SWD/JTAG debugging) |
| 2 (blk)            | CONSOLE TX (OUT)         | 5    | FTDI RX (yellow)                  |
| 3 (blk)            | CONSOLE RX (IN)          | 4    | FTDI TX (orange)                  |
| 4 (blk)            | SWDIO                    |      | N/C (used for SWD/JTAG debugging) |
| 5 (blk)            | SWCLK                    |      | N/C (used for SWD/JTAG debugging) |
| 6 (blk)            | GND                      | 1    | FTDI GND (black)                  |

[SWD Debug Port](../debug/swd_debug.md) 页面和各个飞行控制器页面包含更多关于调试端口引脚布局的信息。

## 打开控制台

连接好控制台后，使用默认的串口工具或以下默认设置：

### Linux / Mac OS: Screen

在 Ubuntu 上安装 screen（Mac OS 已预装）：

```sh
sudo apt-get install screen
```

- 串口：Pixhawk v1 / Pixracer 使用 57600 波特率

以 BAUDRATE 波特率、8 数据位、1 停止位连接到正确的串口（使用 `ls /dev/tty*` 并观察在拔插 USB 设备时的变化）。常见名称为 Linux 的 `/dev/ttyUSB0` 和 `/dev/ttyACM0`，Mac OS 为 `/dev/tty.usbserial-ABCBD`。

```sh
screen /dev/ttyXXX BAUDRATE 8N1
```

### Windows: PuTTY

下载 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 并启动。

然后选择 '串口连接' 并设置端口参数为：

- 57600 波特率
- 8 数据位
- 1 停止位