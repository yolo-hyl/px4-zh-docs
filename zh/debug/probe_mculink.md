# MCU-Link调试探针

[MCU-Link调试探针](https://www.nxp.com/design/design-center/software/development-software/mcuxpresso-software-and-tools-/mcu-link-debug-probe:MCU-LINK) 是一种低成本、高速且功能强大的调试工具，可作为独立的调试和控制台通信器，用于与Pixhawk板配合使用。

关键特性：

- 仅需一根USB-C连接线即可实现复位、SWD、SWO和串口通信，体积小巧！
- 最高可达9.6MBit/s的SWO连接。最高5MBaud的串口速率。1.2V至5V目标电压。USB2高速480Mbps连接。
- 通过NXP LinkServer或pyOCD软件驱动，支持广泛设备。
- 相比Pixhawk调试适配器（约20€）搭配JLink EDU mini（约55€）或JLink BASE（约400€）的方案，成本更低（<15€），且硬件规格更优。

[Pixhawk调试适配器](https://holybro.com/products/pixhawk-debug-adapter) 提供了将Pixhawk连接到MCU-Link的便捷方式（该探针本身不附带适配Pixhawk飞控的适配器）。

::: info
这些说明已在以下型号上测试过：FMUv6X-RT, FMUv6X, FMUv6c, FMUv5X。
:::

## 使用NXP LinkServer进行调试配置

MCU-Link为NXP（FMUv6X-RT）芯片提供了 [LinkServer](https://www.nxp.com/design/design-center/software/development-software/mcuxpresso-software-and-tools-/linkserver-for-microcontrollers:LINKERSERVER) GDB服务器：

[下载](https://www.nxp.com/design/design-center/software/development-software/mcuxpresso-software-and-tools-/linkserver-for-microcontrollers:LINKERSERVER#downloads) LinkServer并选择对应操作系统，按照安装说明进行安装。

Windows系统中LinkServer安装路径为 `C:\NXP\LinkServer_x.x.x`  
Linux系统中LinkServer安装路径为 `/usr/local/LinkServer/LinkServer`

针对FMUv6X-RT可使用以下命令进行烧录：

```sh
/usr/local/LinkServer/LinkServer flash MIMXRT1176xxxxx:MIMXRT1170-EVK-CM7-ONLY load build/px4_fmu-v6xrt_default/px4_fmu-v6xrt_default.elf
```

您可以在新终端中启动GDB服务器：

```sh
/usr/local/LinkServer/LinkServer gdbserver MIMXRT1176xxxxx:MIMXRT1170-EVK-CM7-ONLY
```

然后通过GDB连接到3333端口：

```sh
arm-none-eabi-gdb build/px4_fmu-v6xrt_default/px4_fmu-v6xrt_default.elf -ex "target extended-remote :3333"
```

使用GDB将二进制文件加载到Pixhawk中：

```sh
(gdb) load
```

## 使用pyOCD进行调试配置

MCU-Link通过 [pyOCD提供GDB服务器](https://pyocd.io/)：

```sh
python3 -m pip install -U pyocd
```

您可以在新终端中启动GDB服务器：

```sh
pyocd gdb -t mimxrt1170_cm7
```

目标设备需要指定为以下之一：

- FMUv6X-RT: `mimxrt1170_cm7`
- FMUv6X: `stm32h743xx`
- FMUv6C: `stm32h743xx`
- FMUv5X: `stm32f767zi`

随后通过GDB连接到3333端口：

```sh
arm-none-eabi-gdb build/px4_fmu-v6xrt_default/px4_fmu-v6xrt_default.elf -ex "target extended-remote :3333"
```

使用GDB将二进制文件加载到Pixhawk中：

```sh
(gdb) load
```