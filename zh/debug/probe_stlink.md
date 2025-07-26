

# STLink 调试探针

[STLinkv3-MINIE](https://www.st.com/en/development-tools/stlink-v3minie.html) 是一款价格低廉、速度较快且功能强大的调试探针，可作为 PX4 开发者的独立调试和控制台通信设备：

- 仅需一根 USB-C 接口即可实现复位、SWD、SWO 和串口通信，体积非常小巧！
- 最高 24MHz 的 SWD 和 SWO 连接，最高 16 MBaud 串口。支持 1.65V 至 3.6V 目标电压。USB2 高速 480 Mbps 连接。
- 由 STLink 或 OpenOCD 软件驱动，支持广泛的设备。
- 价格远低于 Pixhawk 调试适配器（约 20€）和 JLink EDU mini（约 55€）或 JLink BASE（约 400€），同时硬件规格更优（<15€）。

STLink 调试探针本身不带与 Pixhawk 飞行控制器配合使用的适配器。
下方的 [Pixhawk 调试端口适配器](#Pixhawk 调试端口适配器) 部分会说明如何自制适配器（需要焊接）。

::: info
[CUAV C-ADB Pixhawk 调试适配器](../debug/swd_debug.md#cuav-c-adb-pixhawk-debug-adapter)（约 65€）附带 STLinkv3-MINIE！
该适配器配有 [Pixhawk 调试全功能](../debug/swd_debug.md#pixhawk-debug-full) 10 针 SH 接口的连接器（但不支持 [Pixhawk 调试迷你版](../debug/swd_debug.md#pixhawk-debug-mini)）。
:::

## 调试配置

STLink提供了[通过OpenOCD的GDB服务器](https://openocd.org/doc-release/html/index.html)：

```sh
sudo apt install openocd  # Ubuntu
brew install open-ocd  # macOS
```

您可以在新的终端窗口中启动 GDB 服务器：

```sh
openocd -f interface/stlink.cfg -f target/stm32f7x.cfg
```

配置文件需要设置为：

- FMUv2-v4: `-f target/stm32f4x.cfg`
- FMUv5: `-f target/stm32f7x.cfg`
- FMUv6: `-f target/stm32h7x.cfg`

然后可以通过 3333 端口连接 GDB：

```sh
arm-none-eabi-gdb build/px4_fmu-v5x_default/px4_fmu-v5x_default.elf -ex "target extended-remote :3333"
```

有关更高级的调试选项，请参见[嵌入式调试工具][emdbg]。

## Pixhawk 调试端口适配器

要连接到 Pixhawk 调试端口，您需要焊接一个适配器（除非使用 [CUAV 调试适配器](../debug/swd_debug.md#cuav-c-adb-pixhawk-debug-adapter)）。

此焊接指南需要以下材料：

- 1x [STLinkv3-MINIE](https://www.st.com/en/development-tools/stlink-v3minie.html)。
- 1x 用于连接 [JST SM10B (Full)](https://www.digikey.com/products/en?keywords=A10SR10SR30K203A) 或 [JST SM06B (Mini)](https://www.digikey.com/products/en?keywords=A06SR06SR30K152A) 的电缆连接器。

  我们建议购买两端都带有连接器的完整电缆。

- 1x 电烙铁和焊锡。
- 一些镊子、剪线钳和尖嘴钳。

Pixhawk 调试端口在 [DS-009](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 中标准化，需要连接到 STLinkv3-MINIE 的板对板（BTB）卡边连接器 CN2。
引脚映射如下：

| #Full | #Mini | Pixhawk 调试    | STLinkv3 | #BTB |
| ----: | ----: | :--------------- | :------- | ---: |
|     1 |     1 | **VREF**         | VCC      |   10 |
|     2 |     2 | Console TX (out) | TX (in)  |    8 |
|     3 |     3 | Console RX (in)  | RX (out) |    7 |
|     4 |     4 | **SWDIO**        | TMS      |    3 |
|     5 |     5 | **SWCLK**        | CLK      |    4 |
|     6 |       | SWO              | TDO      |    5 |
|     7 |       | GPIO1            |          |      |
|     8 |       | GPIO2            |          |      |
|     9 |       | nRST             | RST      |    9 |
|    10 |     6 | **GND**          | GND      |    6 |

STLinkv3 不支持 GPIO1/2 引脚，我们建议使用数字 ITM 分析代替 SWO，因为前者更灵活且支持精确周期时间戳。

您可以选择焊接短电缆或长电缆到 BTB 连接器。
短电缆更适合高速通信，但焊接难度更大。
我们建议先焊接长电缆并测试与目标设备的通信速度。

::: info
本指南是针对 10 引脚全尺寸调试端口编写的。
如果要焊接 6 引脚迷你版本，只需省略不需要的信号。
STLink 支持任何基于 SWD/JTAG 的调试接口，因此您可以根据需要的连接器调整本指南。
调试探针价格低廉，可以为每个连接器单独准备一个，而不是使用适配器。
:::

这是 STLinkv3-MINIE 的交付状态。

![](../../assets/debug/stlinkv3_minie_p1.jpeg)

展开 PCB 并检查是否有损坏。
插入电源并确认其能否正常开机。

![](../../assets/debug/stlinkv3_minie_p2.jpeg)

### 短电缆

短电缆需要使用电线剪和剥线钳，并需要更多焊接技巧。  
但它可以使整个调试探针更小。

组装一个不含GPIO1/2的10针接头。如果已拥有组装好的电缆，请用镊子小心移除GPIO1/2线缆，方法是提起固定线缆的插针。  
将线缆剪短至约2厘米（约1英寸）长度，并剥去绝缘层。

![](../../assets/debug/stlinkv3_minie_p3.jpeg)

对STLink上的BTB接头和线缆进行搪锡处理。

![](../../assets/debug/stlinkv3_minie_p4.jpeg)

首先焊接GND和VCC信号以使接头与边缘平行对齐。  
然后焊接TX和RX引脚。最后焊接RST连接。

![](../../assets/debug/stlinkv3_minie_p5.jpeg)

将STLink翻转过来，焊接剩余的三根线。  
首先焊接SWDIO->TMS，然后焊接SWCLK->CLK，最后焊接SWO->TDO。

![](../../assets/debug/stlinkv3_minie_p6.jpeg)

### 长电缆

当使用预组装电缆时，长电缆特别有用，因为它消除了剪断或剥线的需要。

小心地从电缆的一端连接器上移除两条GPIO1/2电缆。
然后从另一端连接器上移除所有电缆。
最终你会在电线末端得到八个压接连接器。

![](../../assets/debug/stlinkv3_minie_p7.jpeg)

对压接电缆连接器和BTB连接器进行搪锡处理，并将压接连接器直接焊接到STLinkv3上。
注意避免电缆之间短路，因为压接连接器体积较大。

![](../../assets/debug/stlinkv3_minie_p8.jpeg)

### 测试

您现在应测试调试探针以确保没有电路短路。

1. 通过 Pixhawk 调试端口将探针插入目标设备。
2. 使用您选择的程序测试串口。
3. 通过 [OpenOCD][https://openocd.org] 或 [STLink](https://www.st.com/en/development-tools/stsw-link004.html) 软件测试 SWD 和 RST 连接。
4. 通过 [Orbuculum][https://github.com/orbcode/orbuculum] 测试 SWO 连接。

请参阅 [Embedded Debug Tools][emdbg] 以获取有关 PX4 FMUv5 和 FMUv6 飞控软件支持的更多信息。

### 使其更小

此步骤移除STLinkv3-MINIE背面的14针调试接口，并在设备周围添加收缩管以改善操作性，防止调试器与金属部件或PCB接触短路。  
此步骤为可选操作，需要以下工具：  

- 1x 20mm收缩管，长约5cm  
- 1x 平口钳（用于通过USB-C接口夹持STLinkv3）  
- 1x 细口剪线钳或电烙铁  
- 1x 热风枪  

使用钳子轻轻拔下STDC14连接器的塑料部分，仅保留连接器引脚。  

![](../../assets/debug/stlinkv3_minie_p9.jpeg)  

用细口钳剪断连接器引脚，注意不要损坏PCB板或其上的任何元件。  
或可选择用电烙铁将这些引脚焊下，但耗时较长。  

![](../../assets/debug/stlinkv3_minie_p10.jpeg)  

旋转STLinkv3，剪断另一排引脚，同样要小心避免损坏设备。  

![](../../assets/debug/stlinkv3_minie_p11.jpeg)  

裁切约5cm（2英寸）长的收缩管，使其与USB-C接口平齐并略微延伸至末端。  

![](../../assets/debug/stlinkv3_minie_p12.jpeg)  

用平口钳夹住PCB和收缩管，抓握位置为USB-C接口的**底部金属部分**。  
注意不要意外挤压USB-C接口中部的塑料部分！  

![](../../assets/debug/stlinkv3_minie_p13.jpeg)  

使用热风枪使收缩管完全包裹调试探针。  
确保收缩管均匀收缩并保护整个PCB板。  

![](../../assets/debug/stlinkv3_minie_p14.jpeg)  

可选操作：您可以打印自定义的logo并裁剪至适当尺寸粘贴。  
注意高温可能导致墨水流动，因此可能需要尝试不同打印机设置以获得最佳效果。  

![](../../assets/debug/stlinkv3_minie_p15.jpeg)  

[emdbg]: https://pypi.org/project/emdbg/

## 另请参见

- [STLINK-V3MINIE 调试器/编程器 适用于 STM32 微控制器的微型探针](https://www.st.com/resource/en/user_manual/um2910-stlinkv3minie-debuggerprogrammer-tiny-probe-for-stm32-microcontrollers-stmicroelectronics.pdf) (用户手册)