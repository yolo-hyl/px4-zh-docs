# PX4 板级配置 (Kconfig)

PX4 自动驾驶固件可以在构建时进行配置，以适应特定应用场景（固定翼、多旋翼、地面车等），启用新功能（如 Cyphal）或通过禁用某些驱动和子系统来节省 Flash 和 RAM 使用。
此配置通过 _Kconfig_ 实现，这也是 NuttX 使用的 [配置系统](../hardware/porting_guide_nuttx.md#nuttx-menuconfig-setup)。

配置选项（通常在 _kconfig_ 语言中称为"符号"）定义在 **/src** 目录下的 `Kconfig` 文件中。

## PX4 Kconfig 符号命名规范

按照惯例，模块/驱动的符号名称基于模块文件夹路径命名。
例如，位于 `src/drivers/adc/board_adc` 的 ADC 驱动符号必须命名为 `DRIVERS_ADC_BOARD_ADC`。

为驱动/模块特定选项添加符号时，命名规范为模块名后接选项名。
例如 `UAVCAN_V1_GNSS_PUBLISHER` 是 `UAVCAN_V1` 模块的 `GNSS_PUBLISHER` 选项。
选项必须通过 `if` 语句进行保护，以确保仅在模块本身启用时才显示。

完整示例如下：

```
menuconfig DRIVERS_UAVCAN_V1
    bool "UAVCANv1"
    default n
    ---help---
        Enable support for UAVCANv1

if DRIVERS_UAVCAN_V1
    config UAVCAN_V1_GNSS_PUBLISHER
        bool "GNSS Publisher"
        default n
endif #DRIVERS_UAVCAN_V1
```

::: info
构建过程将静默忽略 `*.px4board` 配置文件中缺失或拼写错误的模块。
:::

## PX4 Kconfig 标签继承

每个 PX4 开发板必须包含 `default.px4board` 配置，可以可选包含 `bootloader.px4board` 配置。
但你也可以在不同标签下添加单独的配置，例如 `cyphal.px4board`。
注意，默认情况下 `cyphal.px4board` 的配置会继承 `default.px4board` 中的所有设置。
当修改 `cyphal.px4board` 时，只会存储与 `default.px4board` 相比不同的 Kconfig 键值差值，这有助于简化配置管理。

::: info
当在 `default.px4board` 中修改 Kconfig 键值时，所有具有相同配置的同板衍生配置也会被修改。
:::

## PX4 Menuconfig 配置

[menuconfig](https://pypi.org/project/kconfiglib/#menuconfig-interfaces) 工具用于修改 PX4 开发板配置，添加/移除模块、驱动和其他功能。

该工具提供命令行和图形界面版本，均可通过 PX4 构建快捷方式启动：

```
make px4_fmu-v5_default boardconfig
make px4_fmu-v5_default boardguiconfig
```

::: info
_Kconfiglib_ 和 _menuconfig_ 通过 _kconfiglib_ Python 包提供，该包由标准的 [ubuntu.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/ubuntu.sh) 安装脚本安装。
如果 _kconfiglib_ 未安装，可通过命令 `pip3 install kconfiglib` 进行安装。
:::

命令行和图形界面的示例如下所示。

### menuconfig 图形界面

![menuconfig GUI interface](../../assets/hardware/kconfig-menuconfig.png)

### menuconfig 命令行界面

![menuconfig command line interface](../../assets/hardware/kconfig-guiconfig.png)