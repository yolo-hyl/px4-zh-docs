# PX4 引导程序烧录至 Betaflight 系统

本文档介绍了如何将 PX4 引导程序烧录到已安装 Betaflight 的板载中（例如 [OmnibusF4 SD](../flight_controller/omnibus_f4_sd.md) 或 [Kakute F7](../flight_controller/kakutef7.md)）。

有三种工具可用于烧录 PX4 引导程序：_Betaflight Configurator_、[dfu-util](http://dfu-util.sourceforge.net/) 命令行工具，或图形化 [dfuse](https://www.st.com/en/development-tools/stsw-stm32080.html)（仅限 Windows）。

::: info
_Betaflight Configurator_ 是最简单的工具，但较新版本可能不支持非 Betaflight 引导程序更新。
建议首先尝试此方法，但如果固件更新失败，请使用其他方法。
:::

## 使用 Betaflight Configurator 更新引导程序

::: info
_Betaflight Configurator_ 可能从2023年5月起不支持 PX4 引导程序更新。
旧版本可能有效，但具体版本未知。
:::

使用 _Betaflight Configurator_ 安装 PX4 引导程序的步骤：

1. 下载或构建适用于目标板的 [引导程序固件](#bootloader-firmware)。
1. 下载适用于您平台的 [Betaflight Configurator](https://github.com/betaflight/betaflight-configurator/releases)。

   :::tip
   如果使用 _Chrome_ 浏览器，一个简单的跨平台替代方案是安装 [此扩展](https://chrome.google.com/webstore/detail/betaflight-configurator/kdaghagfopacdngbohiknlhcocjccjao)。
   :::

1. 将板载连接到 PC 并启动 Configurator。
1. 点击 **Load Firmware [Local]** 按钮
   ![Betaflight Configurator - Local Firmware](../../assets/flight_controller/omnibus_f4_sd/betaflight_configurator.jpg)
1. 从文件系统中选择引导程序二进制文件并烧录板载。

现在应该可以在板载上安装 PX4 固件了。

## 使用 DFU 更新引导程序

本节介绍如何使用 [dfu-util](http://dfu-util.sourceforge.net/) 或图形化 [dfuse](https://www.st.com/en/development-tools/stsw-stm32080.html) 工具（仅限 Windows）烧录 PX4 引导程序。

首先需要下载或构建适用于目标板的 [引导程序固件](#bootloader-firmware)（以下称为 `<target.bin>`）。

::: info
以下所有方法都是安全的，因为 STM32 MCU 不会被损坏！
即使烧录失败，DFU 也无法被覆盖，且始终允许安装新固件。
:::

### DFU 模式

两种工具都需要板载处于 DFU 模式。
进入 DFU 模式时，请按住启动按钮同时将 USB 线连接到计算机。
板载供电后即可松开按钮。

### dfu-util

::: info
[Holybro Kakute H7 v2](../flight_controller/kakuteh7v2.md)、[Holybro Kakute H7](../flight_controller/kakuteh7.md) 和 [mini](../flight_controller/kakuteh7mini.md) 飞控可能需要先运行附加命令来擦除闪存参数（以修复参数保存问题）：

```
dfu-util -a 0 --dfuse-address 0x08000000:force:mass-erase:leave -D build/<target>/<target>.bin
```

该命令可能会生成错误，可以忽略。
完成后，再次进入 DFU 模式完成常规烧录。
:::

烧录引导程序到飞控：

```
dfu-util -a 0 --dfuse-address 0x08000000 -D  build/<target>/<target>.bin
```

重启飞控并让它正常启动（无需按住启动按钮）。

### dfuse

dfuse 手册可在此处找到：https://www.st.com/resource/en/user_manual/cd00155676.pdf

使用工具烧录 `<target>.bin` 文件。

## 引导程序固件

上述工具烧录预构建的引导程序固件。
大多数目标可通过正常 PX4 源代码构建引导程序固件，其他目标则需使用引导程序仓库中的源代码构建。

具有 PX4-Autopilot `make` 目标的飞控可从 PX4-Autopilot 源代码构建引导程序。可通过运行以下 `make` 命令并记录以 `_bootloader` 结尾的 `make` 目标来获取适用的控制器列表：

```
$make list_config_targets

...
cuav_nora_bootloader
cuav_x7pro_bootloader
cubepilot_cubeorange_bootloader
holybro_durandal-v1_bootloader
holybro_kakuteh7_bootloader
holybro_kakuteh7v2_bootloader
holybro_kakuteh7mini_bootloader
matek_h743-mini_bootloader
matek_h743-slim_bootloader
modalai_fc-v2_bootloader
mro_ctrl-zero-classic_bootloader
mro_ctrl-zero-h7_bootloader
mro_ctrl-zero-h7-oem_bootloader
mro_pixracerpro_bootloader
px4_fmu-v6u_bootloader
px4_fmu-v6x_bootloader
```

为这些飞控构建时，请下载并构建 [PX4-Autopilot 源代码](https://github.com/PX4/PX4-Autopilot)，然后使用以下命令构建目标：

```sh
git clone --recursive https://github.com/PX4/PX4-Autopilot.git
cd PX4-Autopilot
make <target> # 例如：holybro_kakuteh7mini_bootloader
```

对于其他飞控，请下载 [PX4/Bootloader](https://github.com/PX4/Bootloader) 仓库并使用相应目标构建源代码：

```
git clone --recursive  https://github.com/PX4/Bootloader.git
cd Bootloader
make <target> # 例如：omnibusf4sd_bl 或 kakutef7_bl
```

## 重新安装 Betaflight

要切换回 _Betaflight_：

1. 备份 PX4 参数。
   可通过 [导出](../advanced/parameters_and_configurations.md#exporting-and-loading-parameters) 到 SD 卡完成。
1. 保持按住 **bootloader** 按钮的同时连接 USB 线
1. 使用 _Betaflight-configurator_ 按常规方法烧录 _Betaflight_