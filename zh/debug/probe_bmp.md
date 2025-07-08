# Black Magic Probe (和 Dronecode Probe)

[Black Magic Probe](https://black-magic.org) 是一种易于使用的嵌入式微控制器 JTAG/SWD 调试器，基本支持即插即用。由于 Black Magic Probe 是通用调试探针，你需要一个适配器才能连接到 Pixhawk 飞控，可通过以下链接购买：

- [Drone Code Debug Adapter](https://1bitsquared.com/products/drone-code-debug-adapter)（1 BIT SQUARED）

## Dronecode Probe

[Dronecode Probe](https://kb.zubax.com/display/MAINKB/Dronecode+Probe+documentation) 是专为调试 PX4 自主飞行控制器而设计的 Black Magic Probe 特化版本。

该探针的 USB 接口提供了两个独立的虚拟串口接口：一个用于连接 [系统控制台](system_console.md)（UART），另一个用于嵌入式 GDB 服务器（SWD 接口）。该探针提供了 DCD-M 连接线，可连接到 [Pixhawk Debug Mini](swd_debug.md#pixhawk-debug-mini)。

::: info
探针附带的 _6-pos DF13_ 接头不能用于 SWD 调试（该接头用于连接系统控制台）。
:::

## 使用探针

::: info
要调试 STM32F7 或更高版本（FMUv5 及更新型号），Dronecode 探针/Blackmagic 探针可能需要固件升级。你可在此处找到如何升级 [blackmagic 探针](https://github.com/blacksphere/blackmagic/wiki/Upgrading-Firmware)。
:::

要使用 Dronecode 探针与 GDB 一起工作，请使用当前烧录到自动驾驶仪上的精确 ELF 文件启动 GDB：

```sh
arm-none-eabi-gdb build/px4_fmu-v5_default/px4_fmu-v5_default.elf
```

然后，需要在 Linux 上选择 Dronecode 探针接口，例如：

```sh
target ext /dev/serial/by-id/usb-Black_Sphere_Technologies_Black_Magic_Probe_f9414d5_7DB85DAC-if00
```

接着扫描目标设备：

```sh
monitor swdp_scan
```

你应该会看到类似以下内容：

```sh
Target voltage: 3.3V
Available Targets:
No. Att Driver
 1      STM32F76x M7
```

请注意，某些自动驾驶仪显示 0.0V 时，后续步骤仍能正常工作。

现在可以连接到该目标设备：

```sh
attach 1
```

此时你应该已经成功连接。