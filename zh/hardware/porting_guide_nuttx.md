# NuttX 开发板移植指南

为了在 NuttX 上移植 PX4 到新的硬件目标，该硬件目标必须被 NuttX 支持。  
NuttX 项目维护了一份优秀的[移植指南](https://cwiki.apache.org/confluence/display/NUTTX/Porting+Guide)，用于将 NuttX 移植到新的计算平台。

以下指南假设您正在使用已支持的硬件目标，或已移植 NuttX（包括 [PX4 基础层](https://github.com/PX4/PX4-Autopilot/tree/main/platforms/nuttx/src/px4)）。

所有开发板的配置文件（包括链接脚本和其他必要设置）位于 [/boards](https://github.com/PX4/PX4-Autopilot/tree/main/boards/) 路径下的供应商和开发板特定目录中（即 **boards/_VENDOR_/_MODEL_/**）。

以下示例使用 FMUv5，因为它是一个近期的 NuttX 基于飞行控制器的[参考配置](../hardware/reference_design.md)：

- 从 **PX4-Autopilot** 目录运行 `make px4_fmu-v5_default` 将构建 FMUv5 配置
- 基础 FMUv5 配置文件位于：[/boards/px4/fmu-v5](https://github.com/PX4/PX4-Autopilot/tree/main/boards/px4/fmu-v5)。
  - 开发板特定头文件（NuttX 引脚和时钟配置）：[/boards/px4/fmu-v5/nuttx-config/include/board.h](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/nuttx-config/include/board.h)。
  - 开发板特定头文件（PX4 配置）：[/boards/px4/fmu-v5/src/board_config.h](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/src/board_config.h)。
  - NuttX OS 配置（通过 NuttX menuconfig 创建）：[/boards/px4/fmu-v5/nuttx-config/nsh/defconfig](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/nuttx-config/nsh/defconfig)。
  - 构建配置：[boards/px4/fmu-v5/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/default.px4board)。

## NuttX Menuconfig 配置

要修改 NuttX OS 配置，可以使用 [menuconfig](https://bitbucket.org/patacongo/nuttx/src/master/)，通过 PX4 快捷方式：

```sh
make px4_fmu-v5_default menuconfig
make px4_fmu-v5_default qconfig
```

在 Ubuntu 上使用 [ubuntu.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/ubuntu.sh) <!-- NEED px4_version --> 安装新版本 PX4 时，还需要从 [NuttX 工具](https://bitbucket.org/nuttx/tools/src/master/) 安装 _kconfig_ 工具。

::: info
如果使用 [px4-dev-nuttx](https://hub.docker.com/r/px4io/px4-dev-nuttx/) docker 容器或按照我们的正常说明安装到 macOS（这些包含`kconfig-mconf`），则不需要以下步骤。
:::

从任意目录运行以下命令：

```sh
git clone https://bitbucket.org/nuttx/tools.git
cd tools/kconfig-frontends
sudo apt install gperf
./configure --enable-mconf --disable-nconf --disable-gconf --enable-qconf --prefix=/usr
make
sudo make install
```

`--prefix=/usr` 确定具体的安装位置（必须包含在 `PATH` 环境变量中）。  
`--enable-mconf` 和 `--enable-qconf` 选项将分别启用 `menuconfig` 和 `qconfig` 选项。

运行 `qconfig` 可能需要安装额外的 Qt 依赖项。

### 引导程序

首先需要一个引导程序，这取决于硬件目标：

- STM32H7：引导程序基于 NuttX，包含在 PX4 固件中。  
  示例请参见 [此处](https://github.com/PX4/PX4-Autopilot/tree/main/boards/holybro/durandal-v1/nuttx-config/bootloader)。
- 对于所有其他目标，使用 https://github.com/PX4/Bootloader。  
  示例添加新目标的方法请参见 [此处](https://github.com/PX4/Bootloader/pull/155/files)。  
  然后查看 [构建和烧录说明](../software_update/stm32_bootloader.md)。

### 固件移植步骤

1. 确保您有一个可用的 [开发环境](../dev_setup/dev_env.md) 并安装了 NuttX `menuconfig` 工具（见上文）。
1. 下载源代码并确保可以构建现有目标：

   ```sh
   git clone --recursive https://github.com/PX4/PX4-Autopilot.git
   cd PX4-Autopilot
   make px4_fmu-v5
   ```

1. 找到使用相同（或密切相关）CPU 类型的现有目标并复制它。  
   例如对于 STM32F7：

   ```sh
   mkdir boards/manufacturer
   cp -r boards/px4/fmu-v5 boards/manufacturer/my-target-v1
   ```

   将 **manufacturer** 替换为制造商名称，**my-target-v1** 替换为您的开发板名称。

接下来需要遍历 **boards/manufacturer/my-target-v1** 下的所有文件并根据您的开发板进行更新。

1. **firmware.prototype**：更新板 ID 和名称
1. **default.px4board**：更新 **VENDOR** 和 **MODEL** 以匹配目录名（**my-target-v1**）。  
   配置串口。
1. 通过 `make manufacturer_my-target-v1 menuconfig` 配置 NuttX（**defconfig**）：调整 CPU 和芯片，配置外设（UART、SPI、I2C、ADC）。
1. **nuttx-config/include/board.h**：配置 NuttX 引脚。  
   对于所有有多个引脚选项的外设，NuttX 需要知道引脚。  
   它们在芯片特定的引脚映射头文件中定义，例如 [stm32f74xx75xx_pinmap.h](https://github.com/PX4/NuttX/blob/px4_firmware_nuttx-8.2/arch/arm/src/stm32f7/hardware/stm32f74xx75xx_pinmap.h)。
1. **src**：遍历 **src** 下的所有文件并根据需要进行更新，特别是 **board_config.h**。
1. **init/rc.board_sensors**：启动连接到开发板的传感器。