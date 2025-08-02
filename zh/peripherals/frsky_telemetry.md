# FrSky 通信遥测

FrSky 通信遥测功能允许你在一个兼容的遥控发射器上获取机体[遥测状态](#messages)信息。

可用的[遥测信息列表在此](#messages)，包括：飞行模式、电池电量、遥控信号强度、速度、高度等。
部分遥控器还能提供音频和振动反馈，这对低电量等故障保护警告特别有用。

PX4 同时支持[S.Port](#s_port)（新协议）和 D（旧协议）类型的 FrSky 通信接口。

## 硬件设置

FrSky遥测需要：

- 一个[FrSky兼容的遥控器](#transmitters)，如FrSky Taranis X9D Plus。
- 一个[支持FrSky遥测的接收机](#receivers)，如XSR和X8R。
- 一根将FrSky接收机Smart Port（SPort）连接到飞控UART的线缆。

首先[连接接收机的遥控通道](../getting_started/rc_transmitter_receiver.md#connecting-receivers)，例如将接收机和飞控的S.Bus端口相连。

然后通过单独连接接收机的SPort到飞控的任意空UART，并[配置PX4在此UART上运行FrSky遥测](#configure)来设置FrSky遥测。

具体设置方式略有不同，取决于接收机的SPort是否有非反相输出引脚以及Pixhawk的版本。

### Pixhawk FMUv4（及更早版本）

对于Pixhawk FMUv4及更早版本，UART端口和接收机遥测端口通常不兼容（[Pixracer](../flight_controller/pixracer.md)除外）。

通常SPort接收机具有_反相_的S.Port信号，需要使用转换线缆将S.Port拆分为非反相的TX和RX以连接到Pixhawk UART。如下图所示：

![FrSky-Taranis-Telemetry](../../assets/hardware/telemetry/frsky_telemetry_overview.jpg)

:::tip
当连接到反相S.Port时，通常更便宜且更简单的方式是购买[现成的线缆](#ready_made_cable)，该线缆包含适配器并配有自动驾驶仪和接收机的合适连接器。[自制线缆](#diy_cables)需要电子组装的专业知识。
:::

如果使用具有_非反相输出_引脚的S.Port接收机，可以直接连接UART的TX引脚。

<!-- FYI only: 非反相输出可在单线模式下使用，因此不需要同时使用RX和TX线。
讨论链接：https://github.com/PX4/PX4-user_guide/pull/755#pullrequestreview-464046128 -->

然后[配置PX4](#configure)。

### Pixhawk FMUv5/STM32F7及后续版本

对于Pixhawk FMUv5及后续版本，PX4可以直接读取反相（或非反相）的S.Port信号，无需特殊线缆。

::: info
更广泛地说，这是STM32F7或后续版本自动驾驶仪的通用特性（例如，[Durandal](../flight_controller/durandal.md)使用STM32H7，可直接读取反相或非反相的S.Port信号）。
:::

只需将UART的TX引脚连接到SPort的反相或非反相引脚（PX4会自动检测并处理任一类型），然后[配置PX4](#configure)。

<a id="configure"></a>

## PX4 配置

使用 [TEL_FRSKY_CONFIG](../advanced_config/parameter_reference.md#TEL_FRSKY_CONFIG) 配置 FrSky 运行的串口 [Configure the serial port](../peripherals/serial_configuration.md)。  
无需设置串口的波特率，因为这是由驱动程序配置的。

::: info
您可以使用任意可用的UART，但通常使用 `TELEM 2` 作为 FrSky 遥测串口（除 [Pixracer](../flight_controller/pixracer.md) 外，其默认已预配置为使用 FrSky 串口）。
:::

:::tip
如果 _QGroundControl_ 中不可用该配置参数，则可能需要 [将驱动程序添加到固件中](../peripherals/serial_configuration.md#parameter_not_in_firmware)：  
```
drivers/telemetry/frsky_telemetry
```
:::

无需其他配置；连接 FrSky 遥测并在检测到 D 或 S 模式时自动启动。

<a id="transmitters"></a>

## 兼容的遥控器

你需要一个能够接收遥测数据流（并已绑定到FrSky接收器）的遥控器。

流行的替代方案包括：

- FrSky Taranis X9D Plus（推荐）
- FrSky Taranis X9D
- FrSky Taranis X9E
- FrSky Taranis Q X7
- Turnigy 9XR Pro

上述遥控器无需进一步配置即可显示遥测数据。以下章节将解释如何自定义遥测显示（例如，创建更好的UI/UX）。

### Taranis - LuaPilot 设置

运行 OpenTX 2.1.6 或更高版本的兼容 Taranis 接收器（例如 X9D Plus）可以使用 LuaPilot 脚本来修改显示的遥测数据（如下图所示）。

![Taranis 上的遥测屏幕](../../assets/hardware/telemetry/taranis_telemetry.jpg)

安装脚本的说明请见：[LuaPilot Taranis Telemetry script > Taranis Setup OpenTX 2.1.6 或更高版本](http://ilihack.github.io/LuaPilot_Taranis_Telemetry/)

如果你使用文本编辑器打开 `LuaPil.lua` 脚本，可以编辑配置。建议的修改包括：

- `local BattLevelmAh = -1` - 使用机体的电池电量计算
- `local SayFlightMode = 0` - PX4 飞行模式没有 WAV 文件

<a id="messages"></a>

## 遥测消息

FrySky遥测可以传输来自PX4的大部分有用状态信息。
S-Port和D-Port接收器传输不同的消息集合，如下节所列。

<a id="s_port"></a>

### S-Port

S-Port接收器传输以下来自PX4的消息（来自[此处](https://github.com/iNavFlight/inav/blob/master/docs/Telemetry.md#available-smartport-sport-sensors)）:

- **AccX, AccY, AccZ:** 加速度计值。
- **Alt:** 气压计高度，相对于家位置。
- **Curr:** 实际电流消耗（安培）。
- **Fuel:** 若设置了`battery_capacity`变量且`smartport_fuel_percent = ON`，则表示剩余电池百分比，否则表示消耗的mAh。
- **GAlt:** GPS高度，海平面为零。
- **GPS:** GPS坐标。
- **GSpd:** 当前水平地面速度，由GPS计算。
- **Hdg:** 偏航角（度-北方为0°）。
- **VFAS:** 实际电池电压值（FrSky安培传感器电压）。
- **VSpd:** 垂直速度（厘米/秒）。
- **Tmp1:** [飞行模式](../flight_modes/index.md#flight-modes)，以整数形式发送：18 - 手动，23 - 高度保持，22 - 位置保持，27 - 任务，26 - 悬停，28 - 返航，19 - 特技，24 - 离线，20 - 稳定，25 - 起飞，29 - 降落，30 - 跟随我。
- **Tmp2:** GPS信息。最右边数字表示GPS定位类型（0=无，2=2D，3=3D）。其他数字表示卫星数量。

::: info
以下“标准”S-Port消息不被PX4支持：**ASpd**, **A4**。
:::

<!-- FYI:
Values of FRSKY_ID_TEMP1 and FRSKY_ID_TEMP1 set:
- https://github.com/PX4/PX4-Autopilot/blob/main/src/drivers/telemetry/frsky_telemetry/frsky_telemetry.cpp#L85  (get_telemetry_flight_mode)
- https://github.com/PX4/PX4-Autopilot/blob/main/src/drivers/telemetry/frsky_telemetry/frsky_data.cpp#L234-L237
Lua map of flight modes:
- https://github.com/ilihack/LuaPilot_Taranis_Telemetry/blob/master/SCRIPTS/TELEMETRY/LuaPil.lua#L790
-->

### D-port

D-Port接收器传输以下消息（来自[此处](https://github.com/cleanflight/cleanflight/blob/master/docs/Telemetry.md)）:

- **AccX, AccY, AccZ:** 加速度计值。
- **Alt:** 气压计高度，初始位置为零。
- **Cels:** 平均单体电池电压（电池电压除以单体数量）。
- **Curr:** 实际电流消耗（安培）。
- **Fuel:** 若设置了容量则表示剩余电池百分比，否则表示消耗的mAh。
- **Date:** 通电时间。
- **GAlt:** GPS高度，海平面为零。
- **GPS:** GPS坐标。
- **GSpd:** 当前速度，由GPS计算。
- **Hdg:** 偏航角（度-北方为0°）。
- **RPM:** 武器状态时为油门值，否则为电池容量。注意在Taranis中需要将桨叶数量设置为12。
- **Tmp1:** 飞行模式（与S-Port相同）。
- **Tmp2:** GPS信息（与S-Port相同）。
- **VFAS:** 实际电池电压值（FrSky安培传感器电压）。
- **Vspd:** 垂直速度（厘米/秒）。

<a id="receivers"></a>

## FrSky 遥测接收器

Pixhawk/PX4 支持 D（旧）和 S（新）系列 FrSky 遥测协议。下表列出了所有通过 D/S.PORT 支持遥测的 FrSky 接收器（理论上所有设备均可正常工作）。

:::tip
请注意，下表列出的 X 系列接收器是推荐选择（例如 XSR、X8R）。测试团队尚未对 R 系列和 G 系列进行测试/验证，但它们应能正常工作。
:::

| 接收器型号    | 范围  | 组合输出               | 数字遥测输入       | 尺寸                | 重量   |
| ------------- | ----- | ---------------------- | ------------------ | ------------------- | ------ |
| D4R-II        | 1.5km | CPPM (8)               | D.Port             | 40x22.5x6mm         | 5.8g   |
| D8R-XP        | 1.5km | CPPM (8)               | D.Port             | 55x25x14mm          | 12.4g  |
| D8R-II Plus   | 1.5km | 无                     | D.Port             | 55x25x14mm          | 12.4g  |
| X4R           | 1.5km | CPPM (8)               | Smart Port         | 40x22.5x6mm         | 5.8g   |
| X4R-SB        | 1.5km | S.Bus (16)             | Smart Port         | 40x22.5x6mm         | 5.8g   |
| X6R / S6R     | 1.5km | S.Bus (16)             | Smart Port         | 47.42×23.84×14.7mm  | 15.4g  |
| X8R / S8R     | 1.5km | S.Bus (16)             | Smart Port         | 46.25 x 26.6 x 14.2mm | 16.6g  |
| XSR / XSR-M   | 1.5km | S.Bus (16)/CPPM (8)    | Smart Port         | 26x19.2x5mm         | 3.8g   |
| RX8R          | 1.5km | S.Bus (16)             | Smart Port         | 46.25x26.6x14.2mm   | 12.1g  |
| RX8R PRO      | 1.5km | S.Bus (16)             | Smart Port         | 46.25x26.6x14.2mm   | 12.1g  |
| R-XSR         | 1.5km | S.Bus (16)/CPPM (8)    | Smart Port         | 16x11x5.4mm         | 1.5g   |
| G-RX8         | 1.5km | S.Bus (16)             | Smart Port + 内置变距 | 55.26*17*8mm        | 5.8g   |
| R9            | 10km  | S.Bus (16)             | Smart Port         | 43.3x26.8x13.9mm    | 15.8g  |
| R9 slim       | 10km  | S.Bus (16)             | Smart Port         | 43.3x26.8x13.9mm    | 15.8g  |

::: info
上表源自 http://www.redsilico.com/frsky-receiver-chart 和 FrSky [产品文档](https://www.frsky-rc.com/product-category/receivers/)。
:::

<a id="ready_made_cable"></a>

## 现成线缆

适用于 Pixhawk FMUv4 及更早版本（除 Pixracer 外）的现成线缆可从以下来源获取：

- [Craft and Theory](http://www.craftandtheoryllc.com/telemetry-cable)。提供 DF-13 兼容的 _PicoBlade connectors_（适用于 FMUv2/3DR Pixhawk, FMUv2/HKPilot32）和 _JST-GH connectors_（适用于 FMUv3/Pixhawk 2 "The Cube" 和 FMUv4/PixRacer v1）版本。

  <a href="http://www.craftandtheoryllc.com/telemetry-cable"><img src="../../assets/hardware/telemetry/craft_and_theory_frsky_telemetry_cables.jpg" alt="Purchase cable here from Craft and Theory"></a>

<a id="diy_cables"></a>

## DIY线缆  

你可以自行制作适配线缆。  
你需要为自动驾驶仪选择合适的连接器（例如，FMUv3/Pixhawk 2 "The Cube" 和 FMUv4/PixRacer v1 使用 _JST-GH 连接器_，旧版自动驾驶仪则使用 DF-13 兼容的 _PicoBlade 连接器_）。  

Pixracer 集成了 S.PORT 与 UART 信号转换的电路，但其他飞控板需要 UART 到 S.PORT 适配器。  
这些适配器可从以下渠道获取：  

- [FrSky FUL-1](https://www.frsky-rc.com/product/ful-1/): [unmannedtech.co.uk](https://www.unmannedtechshop.co.uk/frsky-transmitter-receiver-upgrade-adapter-ful-1/)  
- SPC: [getfpv.com](http://www.getfpv.com/frsky-smart-port-converter-cable.html), [unmannedtechshop.co.uk](https://www.unmannedtechshop.co.uk/frsky-smart-port-converter-spc/)  

不同飞控板的连接方式详见下文。  

### Pixracer 连接 S-port 接收机  

将 Pixracer 的 FrSky TX 和 RX 线焊接在一起，连接到 X 系列接收机的 S.port 引脚。  
无需连接 GND，因为连接到 S.Bus（常规遥控器连接）时已完成接地。  

S-port 连接方式如下图所示（使用提供的 I/O 连接器）：  

![Grau b Pixracer FrSkyS.Port Connection](../../assets/flight_controller/pixracer/grau_b_pixracer_frskys.port_connection.jpg)  

![Pixracer FrSkyS.Port Connection](../../assets/flight_controller/pixracer/pixracer_FrSkyTelemetry.jpg)  

### Pixracer 连接 D-port 接收机  

:::tip  
目前大多数用户已优先使用 S.PORT。  
:::  

将 Pixracer 的 FrSky TX 线（FS out）连接到接收机的 RX 线，将 Pixracer 的 FrSky RX 线（FS in）连接到接收机的 TX 线。  
无需连接 GND，因为连接到 RC/SBus（常规遥控器）时已完成接地。  

<!-- 图片可补充在此处 -->  

### Pixhawk Pro  

[Pixhawk 3 Pro](../flight_controller/pixhawk3_pro.md) 可通过 TELEM4 连接（无需额外软件配置）。  
你需要通过 UART 到 S.PORT 适配板或 [成品线缆](#ready_made_cable) 进行连接。  

### Pixhawk FMUv5 及更新版本  

只需将任意 UART 的 TX 引脚连接到 SPort 反向或非反向引脚（PX4 会自动检测并处理两种类型）。  

### 其他飞控板  

大多数其他飞控板通过 TELEM2 UART 连接到 FrSky 遥测接收机。  
包括例如：[Pixhawk 1](../flight_controller/pixhawk.md)、[mRo Pixhawk](../flight_controller/mro_pixhawk.md)、Pixhawk2。  

你需要通过 UART 到 S.PORT 适配板或 [成品线缆](#ready_made_cable) 进行连接。  

<!-- 理想情况下在此处添加示意图 -->

## 附加信息

如需更多信息，请参考以下链接：

- [FrSky Taranis 遥测](https://github.com/Clooney82/MavLink_FrSkySPort/wiki/1.2.-FrSky-Taranis-Telemetry)
- [Taranis X9D: 配置遥测](https://www.youtube.com/watch?v=x14DyvOU0Vc) (视频教程)
- [Px4 FrSky 遥测配置与 Pixhawk2 及 X8R 接收机](https://discuss.px4.io//t/px4-frsky-telemetry-setup-with-pixhawk2-and-x8r-receiver/6362) (DIY 线缆)