# SWD 调试端口

PX4 运行在 ARM Cortex-M 微控制器上，该控制器包含专用硬件，支持通过 [_Serial Wire Debug (SWD)_][swd] 接口进行交互式调试，以及通过 [_Serial Wire Output (SWO)_][itm] 和 [_TRACE_ 引脚][etm] 实现非侵入式性能分析和高带宽跟踪。

SWD 调试接口允许直接、低级别的硬件访问微控制器的处理器和外设，因此不依赖于设备上的任何软件。  
因此可以用于调试引导程序和操作系统（如 NuttX）等。

## 调试信号

调试需要四个信号（加粗显示），其余为推荐信号。

| 名称       | 类型   | 描述                                                                                     |
| :--------- | :----- | :---------------------------------------------------------------------------------------- |
| **GND**    | 电源   | 共用电位，公共地线。                                                                      |
| **VREF**   | 电源   | 目标参考电压，允许调试探针在信号上使用电平转换器。                                         |
| **SWDIO**  | I/O    | 串行线调试数据引脚。                                                                      |
| **SWCLK**  | 输入   | 串行线调试时钟引脚。                                                                      |
| nRST       | 输入   | 复位引脚是可选的（n = 低电平有效）。                                                      |
| SWO        | 输出   | 单线跟踪异步数据输出，可输出ITM和DWT数据。                                                |
| TRACECK    | 输出   | 并行总线的跟踪时钟。                                                                      |
| TRACED[0-3] | 输出   | 跟踪同步数据总线，可为1、2或4位。                                                         |

硬件复位引脚是可选的，因为大多数设备也可以通过SWD线路复位。不过，通过按钮快速复位设备对于开发非常有用。

SWO引脚可以输出低开销、实时的性能分析数据，并带有纳秒级时间戳，因此强烈建议在调试时保持其可访问性。

TRACE引脚需要专用调试探针来处理高带宽和后续的数据流解码。通常这些引脚不可访问，通常只用于调试特定的时序问题。

<a id="debug-ports"></a>

## 自动驾驶仪调试端口

飞控通常提供一个调试端口，该端口同时暴露了 [SWD Interface](#调试信号) 和 [System Console](system_console) 接口。

[Pixhawk Connector Standards](#pixhawk-standard-debug-ports) 规范了每个FMU版本必须使用的端口。  
但由于仍有许多板卡采用不同的引脚分布或连接器，我们建议您查阅 [自动驾驶仪文档](../flight_controller/index.md) 以确认端口位置和引脚定义。

部分自动驾驶仪的调试端口位置和引脚分布如下所示：

<a id="port-information"></a>

| 自动驾驶仪                                                                                     | 调试端口                                                                                                                                                        |
| :--------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Holybro Pixhawk 6X-RT (FMUv6X-RT)                                                              | [Pixhawk Debug Full](#Pixhawk Debug Full)                                                                                                                         |
| Holybro Pixhawk 6X (FMUv6x)                                                                    | [Pixhawk Debug Full](#Pixhawk Debug Full)                                                                                                                         |
| Holybro Pixhawk 5X (FMUv5x)                                                                    | [Pixhawk Debug Full](#Pixhawk Debug Full)                                                                                                                         |
| [Holybro Durandal](../flight_controller/durandal.md#debug-port)                                | [Pixhawk Debug Mini](#Pixhawk Debug Mini)                                                                                                                         |
| [Holybro Kakute F7](../flight_controller/kakutef7.md#debug-port)                               | 焊盘                                                                                                                                                       |
| [Holybro Pixhawk 4 Mini](../flight_controller/pixhawk4_mini.md#debug-port) (FMUv5)            | [Pixhawk Debug Mini](#Pixhawk Debug Mini)                                                                                                                         |
| [Holybro Pixhawk 4](../flight_controller/pixhawk4.md#debug_port) (FMUv5)                       | [Pixhawk Debug Mini](#Pixhawk Debug Mini)                                                                                                                         |
| [Drotek Pixhawk 3 Pro](../flight_controller/pixhawk3_pro.md#debug-port) (FMU-v4pro)           | [Pixhawk Debug Mini](#Pixhawk Debug Mini)                                                                                                                         |
| [CUAV V5+](../flight_controller/cuav_v5_plus.md#debug-port)                                    | 6-pin JST GH<br>Digikey: [BM06B-GHS-TBT(LF)(SN)(N)][bm06b-ghs-tbt(lf)(sn)(n)] (vertical mount), [SM06B-GHS-TBT(LF)(SN)(N)][sm06b-ghs-tbt(lf)(sn)(n)] (side mount) |
| [CUAV V5nano](../flight_controller/cuav_v5_nano.md#debug_port)                                 | 6-pin JST GH<br>Digikey: [BM06B-GHS-TBT(LF)(SN)(N)][bm06b-ghs-tbt(lf)(sn)(n)] (vertical mount), [SM06B-GHS-TBT(LF)(SN)(N)][sm06b-ghs-tbt(lf)(sn)(n)] (side mount) |
| [3DR Pixhawk](../flight_controller/pixhawk.md#swd-port)                                        | ARM 10-pin JTAG Connector (也用于FMUv2板卡，包括：_mRo Pixhawk_, _HobbyKing HKPilot32_)                                                             |

<a id="pixhawk-standard-debug-ports"></a>

## Pixhawk 连接器标准调试端口

Pixhawk 项目为不同版本的 Pixhawk FMU 定义了标准引脚排列和连接器类型：

:::tip
请查阅[具体板卡](#port-information)以确认所使用的端口。
:::

| FMU 版本 | Pixhawk 版本                                                 | 调试端口                                |
| :---------- | :-------------------------------------------------------------- | :---------------------------------------- |
| FMUv2       | [Pixhawk / Pixhawk 1](../flight_controller/pixhawk.md#swd-port) | 10针ARM调试                          |
| FMUv3       | Pixhawk 2                                                       | 6针SUR调试                           |
| FMUv4       | Pixhawk 3                                                       | [Pixhawk Debug Mini](#Pixhawk Debug Mini) |
| FMUv5       | Pixhawk 4 FMUv5                                                 | [Pixhawk Debug Mini](#Pixhawk Debug Mini) |
| FMUv5X      | Pixhawk 5X                                                      | [Pixhawk Debug Full](#Pixhawk Debug Full) |
| FMUv6       | Pixhawk 6                                                       | [Pixhawk Debug Full](#Pixhawk Debug Full) |
| FMUv6X      | Pixhawk 6X                                                      | [Pixhawk Debug Full](#Pixhawk Debug Full) |
| FMUv6X-RT   | Pixhawk 6X-RT                                                   | [Pixhawk Debug Full](#Pixhawk Debug Full) |

::: info
FMU与Pixhawk版本的对应关系（仅）在FMUv5X之后保持一致。
:::

### Pixhawk Debug Mini

[Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)定义了 _Pixhawk Debug Mini_，这是一个 _6针SH调试端口_，提供对SWD引脚和[系统控制台](system_console)的访问。

该端口用于FMUv4和FMUv5。

引脚定义如下（调试所需引脚加粗）：

| 引脚 | 信号     |
| --: | :--------- |
|   1 | **VREF**   |
|   2 | 控制台TX |
|   3 | 控制台RX |
|   4 | **SWDIO**  |
|   5 | **SWDCLK** |
|   6 | **GND**    |

调试端口定义包含以下焊盘（位于连接器旁）：

| 焊盘 | 信号 | 电压 |
| --: | :----- | :------ |
|   1 | nRST   | +3.3V   |
|   2 | GPIO1  | +3.3V   |
|   3 | GPIO2  | +3.3V   |

插座为_6针JST SH_ - Digikey编号：[BM06B-SRSS-TBT(LF)(SN)](https://www.digikey.com/products/en?keywords=455-2875-1-ND)（垂直安装），[SM06B-SRSS-TBT(LF)(SN)](https://www.digikey.com/products/en?keywords=455-1806-1-ND)（侧装）。

可使用[类似此电缆](https://www.digikey.com/products/en?keywords=A06SR06SR30K152A)连接到调试端口。

![6针JST SH电缆](../../assets/debug/cable_6pin_jst_sh.jpg)

### Pixhawk Debug Full

[Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)定义了 _Pixhawk Debug Full_，这是一个 _10针SH调试端口_，提供对SWD引脚和[系统控制台](system_console)的访问。这实质上将[Pixhawk Debug Mini](#Pixhawk Debug Mini)旁的焊盘移入连接器，并新增了一个SWO引脚。

该端口规范用于FMUv5x、FMUv6和FMUv6x。

引脚定义如下（调试所需引脚加粗）：

| 引脚 | 信号     |
| --: | :--------- |
|   1 | **VREF**   |
|   2 | 控制台TX |
|   3 | 控制台RX |
|   4 | **SWDIO**  |
|   5 | **SWDCLK** |
|   6 | SWO        |
|   7 | GPIO1      |
|   8 | GPIO2      |
|   9 | nRST       |
|  10 | **GND**    |

GPIO1/2引脚为自由引脚，可用于生成软件信号，配合逻辑分析仪进行时序分析。

插座为_10针JST SH_ - Digikey编号：[BM10B-SRSS-TB(LF)(SN)](https://www.digikey.com/products/en?keywords=455-1796-2-ND)（垂直安装）或 [SM10B-SRSS-TB(LF)(SN)](https://www.digikey.com/products/en?keywords=455-1810-2-ND)（侧装）。

可使用[类似此电缆](https://www.digikey.com/products/en?keywords=A10SR10SR30K203A)连接到调试端口。

<!-- FIXME: better to have image showing proper connections for SWD+SWO -->

![10针JST SH电缆](../../assets/debug/cable_10pin_jst_sh.jpg)

<a id="debug-probes"></a>

## PX4硬件的调试探针

飞控通常提供一个[单调试端口](#自动驾驶仪调试端口)，该端口同时暴露了[SWD接口](#调试信号)和[系统控制台](system_console)。

目前有几种经过测试并支持的调试探针可用于连接这两个接口之一或全部：

- [SEGGER J-Link](../debug/probe_jlink.md)：商用探针，无内置串口控制台，需要适配器。
- [Black Magic Probe](../debug/probe_bmp.md)：集成GDB服务器和串口控制台，需要适配器。
- [STLink](../debug/probe_stlink)：最佳性价比，集成串口控制台，需焊接适配器。
- [MCU-Link](../debug/probe_mculink)：最佳性价比，集成串口控制台，需要适配器。

连接调试端口的适配器可能随飞控或调试探针附带。其他选项如下所示。

## 调试适配器

### Holybro Pixhawk 调试适配器

当调试使用 Pixhawk 标准调试接口的控制器时，[Holybro Pixhawk Debug Adapter](https://holybro.com/products/pixhawk-debug-adapter) 是 _强烈推荐_ 的工具。

这是连接的最简便方式：

- 使用 [Pixhawk Debug Full](#Pixhawk Debug Full) (10针SH) 或 [Pixhawk Debug Mini](#Pixhawk Debug Mini) (6针SH) 调试端口的飞控器
- 支持 10针 ARM 兼容接口标准的 SWD 调试探针，如 [Segger JLink EDU mini](../debug/probe_jlink.md) 或 20针兼容的 Segger JLink 和 STLink

![Holybro Pixhawk Debug Adapter](../../assets/debug/holybro_pixhawk_debug_adapter.png)

### CUAV C-ADB Pixhawk 调试适配器

[CUAV C-ADB 二次开发 Pixhawk 飞控调试适配器](https://store.cu2av.net/shop/cuav-c-adb/) 配备了 [STLinkv3-MINIE 调试探针](../debug/probe_stlink.md)

该适配器提供以下接口：

- 连接到 [Pixhawk Debug Full](#Pixhawk Debug Full) (10针SH) 和 CUAV 标准 DSU 接口（不包括 [Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini) (6针SH)）

适配器上的 M2 接口是 14针 CN4 STDC14（更多信息请参阅 [STLinkv3-MINIE 用户手册](https://www.st.com/resource/en/user_manual/um2910-stlinkv3minie-debuggerprogrammer-tiny-probe-for-stm32-microcontrollers-stmicroelectronics.pdf)）
连接 M2 和 STLinkv3-MINIE 的线缆随适配器提供

![CUAV C-ADB 适配器连接到 STLinkv3-MINIE](../../assets/debug/cuav_c-adb_debug_adapter/hero.jpg)

### 调试探针适配器

一些 SWD [调试探针](#debug-probes) 附带适配器/线缆以连接常见的 Pixhawk [调试端口](#debug-ports)
已知提供连接器的探针如下：

- [DroneCode Probe](../debug/probe_bmp.md#dronecode-probe): 配备连接 [Pixhawk Debug Mini](#Pixhawk Debug Mini) 的接口

### 板级特定适配器

一些厂商提供线缆以方便连接 SWD 接口和 [系统控制台](../debug/system_console)

- [CUAV V5nano](../flight_controller/cuav_v5_nano.md#debug_port) 和 [CUAV V5+](../flight_controller/cuav_v5_plus.md#debug-port) 包含此调试线缆：

![6针 JST SH 线缆](../../assets/debug/cuav_v5_debug_cable.jpg)

### 自定义线缆

您也可以创建自定义线缆以连接不同板或探针：

- 将调试探针的 `SWDIO`、`SWCLK` 和 `GND` 引脚连接到调试端口的对应引脚
- 如果调试探针支持，连接 `VREF` 引脚
- 如果存在，连接剩余引脚

请参阅 [STLinkv3-MINIE](probe_stlink) 了解如何焊接自定义线缆的指南

:::tip
在可能的情况下，我们强烈建议您创建或获取适配板而非自定义线缆来连接 SWD/JTAG 调试器和计算机
这可以降低因不良接线导致调试问题的风险，并且适配器通常提供连接多个流行飞控板的通用接口
:::

<!-- 上文中使用的参考链接 -->

[swd]: https://developer.arm.com/documentation/ihi0031/a/The-Serial-Wire-Debug-Port--SW-DP-
[itm]: https://developer.arm.com/documentation/ddi0403/d/Appendices/Debug-ITM-and-DWT-Packet-Protocol?lang=en
[etm]: https://developer.arm.com/documentation/ihi0064/latest/
[bm06b-ghs-tbt(lf)(sn)(n)]: https://www.digikey.com/products/en?keywords=455-1582-1-ND
[sm06b-ghs-tbt(lf)(sn)(n)]: https://www.digikey.com/products/en?keywords=455-1568-1-ND