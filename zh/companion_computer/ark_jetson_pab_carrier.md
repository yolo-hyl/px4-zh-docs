# ARK Jetson PAB 载板

[ARK Jetson Pixhawk Autopilot Bus (PAB) 载板](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-jetson-pab-carrier) 是 NVIDIA Jetson Orin NX/Nano 以及任何 [Pixhawk Autopilot Bus (PAB)](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf) 兼容飞控（如 [ARKV6X](../flight_controller/ark_v6x.md)）的载板。

![ARK Jetson PAB 载板](../../assets/companion_computer/ark_jetson_pab_carrier/ark_jetson_pab_carrier.jpg)

## 购买渠道

- [ARK Jetson PAB 载板](https://arkelectron.com/product/ark-jetson-pab-carrier/)
- [ARK Jetson Orin NX NDAA 套件](https://arkelectron.com/product/ark-jetson-orin-nx-ndaa-bundle/)

## 技术规格

- **电源需求**

  - 5V
  - 最低 4A（取决于使用场景和外设）

- **附加功能**

  - Pixhawk Autopilot Bus (PAB) 外形规格 ([PAB 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf))
  - MicroSD 插槽
  - 美国制造，符合 NDAA 标准
  - 集成 1W 加热器，适用于极端条件下的传感器稳定性

- **物理参数**

  - 重量:
    - 无 Jetson 和飞控 - 80g
    - 有 Jetson，无散热器或飞控 - 108g
    - 有 Jetson 和散热器，无飞控 - 160g
    - 有 Jetson、散热器、飞控、M.2 SSD 和 M.2 WiFi 模块 - 174g
  - 尺寸（无 Jetson 和飞控）：116 mm x 72 mm x 23 mm

- **引脚定义**
  - 详细引脚映射请参考 [ARK Jetson PAB 载板引脚定义文档](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-jetson-pab-carrier/pinout)。
    ![Jetson 载板引脚定义](../../assets/companion_computer/ark_jetson_pab_carrier/ark_jetson_carrier_pinout.png)

## 飞控连接

Jetson 和飞控通过串口和 USB 接口通信，均支持板对板直接连接，经测试最高可达 3 Mbps。

| 类型   | Jetson 设备路径 | 飞控           |
| :----- | :-------------- | :------------- |
| USB    | /dev/ttyACM0    | USB            |
| 串口   | /dev/ttyTHS1    | TELEM2         |

飞控的 USB 连接与 Jetson 的外部 micro USB 接口复用。
当连接 micro USB 线缆时，飞控将与 Jetson 断开，Jetson 的 USB 接口从主机模式切换为设备模式。
移除 micro USB 线缆后，需要重启才能将 USB 接口切换回主机模式并重新连接飞控。

要启用 ARKV6X 飞控的 USB 接口，Jetson 需要将 `VBUS_SENSE` 引脚拉高。
该引脚连接到 pin 206 GPIO07。
在 Jetpack 5 中，ARK-OS 提供了辅助 Python 脚本来驱动该引脚。
在 Jetpack 6 中，脚本无法切换 GPIO 并保持状态。
作为变通方案，Jetson 在启动时通过 pinmux 将 `VBUS_SENSE` 引脚设置为高电平。

- [在 ARK-OS 中启用 VBUS](https://github.com/ARK-Electronics/ARK-OS/blob/main/platform/jetson/scripts/vbus_enable.py)

### UART

Jetson 与飞控的 UART 连接使用 Jetson UART1，在 Jetpack 5 中显示为 `/dev/ttyTHS0`，在 Jetpack 6 中显示为 `/dev/ttyTHS1`。
该连接对应 Pixhawk 控制器的 `TELEM2` 接口，已测试最高可达 3 Mbps（可能支持更高波特率）。

### 飞控复位

通过连接到 Jetson pin 228 GPIO13 的复位信号可对飞控执行硬复位。
当飞控处于武装状态时，Jetson 无法执行复位操作。
复位信号由飞控的 `nARMED` 信号控制。

提供两个辅助脚本用于复位飞控：

- **复位并等待引导加载程序**：在复位前将 `VBUS_SENSE` 引脚拉高，保持 5 秒进入引导加载程序。
  适用于需要硬重启的固件更新场景。
  - [reset_fmu_wait_bl.py](https://github.com/ARK-Electronics/ARK-OS/blob/main/platform/jetson/scripts/reset_fmu_wait_bl.py)
- **快速复位，跳过引导加载程序**：在复位前将 `VBUS_SENSE` 引脚拉低，避免等待。
  - [reset_fmu_fast.py](https://github.com/ARK-Electronics/ARK-OS/blob/main/platform/jetson/scripts/reset_fmu_fast.py)

## 固件烧录指南

如果您购买了 [ARK Jetson Orin NX NDAA 套件](https://arkelectron.com/product/ark-jetson-orin-nx-ndaa-bundle/)，Jetpack 6 (Ubuntu 22.04) 和 [ARK-OS](https://github.com/ARK-Electronics/ARK-OS) 已预装。

### ARK Jetson 内核 GitHub 仓库

此 [仓库](https://github.com/ARK-Electronics/ark_jetson_kernel) 包含内核下载和编译的辅助脚本。
请按照 README 更新到最新 Jetpack 或执行初始烧录。

要烧录内核，请通过 Micro USB 将 Jetson 连接到主机 PC，并按住 Force Recovery 按钮启动 Jetson。

![Jetson 载板烧录指南](../../assets/companion_computer/ark_jetson_pab_carrier/ark_jetson_flashing_guide.png)

## 参见

- [ARK Jetson PAB 文档](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-jetson-pab-carrier) (ARK Docs)