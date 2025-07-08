# 通过USB更新Pixhawk V6X-RT引导加载程序

本主题说明如何在[无需调试探针](../flight_controller/pixhawk6x-rt.md)的情况下，通过USB为[Pixhawk FMUv6X-RT](../flight_controller/pixhawk6x-rt.md)烧录引导加载程序。

## 概述

_PX4 Bootloader_ 用于加载[Pixhawk主控板](../flight_controller/pixhawk_series.md)（PX4FMU, PX4IO）的固件。

Pixhawk控制器通常预装了合适的引导加载程序版本。但在某些情况下，该引导加载程序可能缺失，或者存在需要更新的旧版本。设备也可能因故障而无法使用，此时需要擦除设备并烧录新的引导加载程序。

大多数飞行控制器需要调试探针来更新引导加载程序，如[Bootloader Update > Debug Probe Bootloader Update](../advanced_config/bootloader_update.md#debug-probe-bootloader-update)中所述。你可以使用此方法更新Pixhawk FMUv6X-RT，但如果你没有调试探针，可按照本主题中的说明操作。

## 构建PX4 FMUv6X-RT引导加载程序

可以在PX4-Autopilot文件夹内使用`make`命令和具有`_bootloader`后缀的特定开发板目标进行构建。对于FMUv6X-RT，命令为：

```sh
make px4_fmu-v6xrt_bootloader
```

这将生成位于`build/px4_fmu-v6xrt_bootloader/px4_fmu-v6xrt_bootloader.bin`的引导加载程序二进制文件，可通过SWD或ISP进行烧录。如果你要构建引导加载程序，应该已经熟悉其中一种选项。

如果需要HEX文件而非ELF文件，请使用objcopy：

```sh
arm-none-eabi-objcopy -O ihex build/px4_fmu-v6xrt_bootloader/px4_fmu-v6xrt_bootloader.elf px4_fmu-v6xrt_bootloader.hex
```

## 通过USB烧录引导加载程序

Pixhawk V6X-RT内置ROM中的引导加载程序。要通过USB烧录新的引导加载程序，需下载[NXP MCUXpresso Secure Provisioning工具](https://www.nxp.com/design/design-center/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-secure-provisioning-tool:MCUXPRESSO-SECURE-PROVISIONING)。该工具支持Windows、Linux和macOS。

1. 安装 _MCUXpresso Secure Provisioning_ 应用并启动该应用：

   ![通过Secure provisioning烧录引导加载程序 - 步骤1](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step1.png)

1. 首次启动时需创建“New Workspace”。
   选择`i.mX RT11xx`，然后选择`MIMXRT1176`

   ![通过Secure provisioning烧录引导加载程序 - 步骤2](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step2.png)

1. 创建“New Workspace”后，点击 **FlexSPI NOR - simplified** 按钮

   ![通过Secure provisioning烧录引导加载程序 - 步骤3](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step3.png)

1. 在_Boot Memory Configuration_窗口中，将"Device type"更改为`Macronix Octal DDR`并点击 **OK**。

![通过Secure provisioning烧录引导加载程序 - 步骤4](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step4.png)

1. 在菜单栏选择 **Tools > Flash Programmer**：

   ![通过Secure provisioning烧录引导加载程序 - 步骤5](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step5.png)

1. 你将看到弹出窗口提示Pixhawk V6X-RT未进入"ISP引导加载程序模式"。

   ![通过Secure provisioning烧录引导加载程序 - 步骤6](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step6.png)

   要使Pixhawk V6X-RT进入"ISP引导加载程序模式"，有两种方法：

   1. 启动QGC并连接Pixhawk，选择 **Analayze Tools** 然后 **MAVLINK Console**。
      在控制台中输入 `reboot -i`。
      这将使Pixhawk V6X-RT进入"ISP引导加载程序模式"

      ![ISP引导加载程序模式](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_enter_isp_qgc.png)

   2. 如果主板已损坏且无法连接QGC，需打开FMUM模块并在给主板供电时按下BOOT按钮（如下图红色圈出）。

      <img src="../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_enter_isp_button.jpg" style="zoom:67%;" />

      点击 **YES** 启动 _Flash Programmer Tool_。

1. 当Flash Programming开始时，会弹出配置目标内存的提示，点击 **Yes**

   ![通过Secure provisioning烧录引导加载程序 - 步骤7](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step7.png)

1. 目标内存配置成功后，可以点击 **Erase All** 按钮

   ![通过Secure provisioning烧录引导加载程序 - 步骤8](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step8.png)

1. 擦除闪存后，点击 **Load ...** 按钮，然后点击 **Browse** 按钮

   ![通过Secure provisioning烧录引导加载程序 - 步骤9](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step9.png)

1. 找到 `px4_fmu-v6xrt_bootloader.bin` 文件并点击 **Open**，然后点击 **Load**。

   ![通过Secure provisioning烧录引导加载程序 - 步骤10](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step10.png)

1. 如果加载成功，右下角应显示 "Success: load from file"

   ![通过Secure provisioning烧录引导加载程序 - 步骤11](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step11.png)

1. 点击 **Write** 按钮以烧录PX4引导加载程序

![通过Secure provisioning烧录引导加载程序 - 步骤12](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step12.png)

1. 成功时应显示 "Success: Write memory 0x30000000 - 0x3XXXXXXX" 注意：由于引导加载程序的更新，数值可能不同。

   ![通过Secure provisioning烧录引导加载程序 - 步骤13](../../assets/advanced_config/bootloader_6xrt/bootloader_update_v6xrt_step13.png)

现在拔下Pixhawk V6X-RT并重新给主板供电。
引导加载程序更新完成后，可以使用 _QGroundControl_ 通过[Load PX4 Firmware](../config/flight_controller.md#load_px4_firmware) 加载PX4固件。