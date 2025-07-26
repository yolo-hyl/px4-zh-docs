# Omnibus F4 SD

:::warning
此飞控已[停产](../flight_controller/autopilot_experimental.md)，目前不再商业销售。
:::

:::warning
PX4 未生产此（或任何）自动驾驶仪。
如需支持或合规性问题，请联系制造商。
:::

_**Omnibus F4 SD**_ 是一款专为竞速飞行器设计的控制器板。  
与典型竞速板相比，它具有一些额外功能，例如 SD 卡和更快的 CPU。

<img src="../../assets/flight_controller/omnibus_f4_sd/board.jpg" width="400px" title="Omnibus F4 SD" />

与 [Pixracer](../flight_controller/pixracer.md) 相比，其主要区别如下：

- 价格更低  
- I/O 端口更少（尽管仍然可以连接 GPS 或 Flow 传感器等外设）  
- 外部 GPS 需在 I²C 总线外接上拉电阻，详见下文 [I²C](#I2C)  
- RAM 更少（192 KB 对比 256 KB）和 FLASH 容量更小（1 MB 对比 2 MB）  
- 与 _Pixracer_ 尺寸相同，但外形更紧凑（因其连接器更少）  
- 集成 OSD（尚未在软件中实现）

:::tip
您仍然可以使用所有常规的 PX4 功能进行竞速飞行！
:::

::: info
此飞控已获得[厂商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 核心特性

- 主系统芯片: [STM32F405RGT6](https://www.st.com/en/microcontrollers/stm32f405rg.html)  
  - CPU: 168 MHz ARM Cortex M4 单精度浮点单元  
  - RAM: 192 KB SRAM  
  - FLASH: 1 MB  
- 标准竞速板型：36x36 mm，标准30.5 mm孔距  
- MPU6000 加速度计/陀螺仪  
- BMP280 气压计（部分板载未装配）  
- microSD（用于日志记录）  
- Futaba S.BUS 和 S.BUS2 / Spektrum DSM2 和 DSMX / Graupner SUMD / PPM 输入 / Yuneec ST24  
- OneShot PWM 输出（可配置）  
- 内置电流传感器  
- 内置OSD芯片（通过SPI的AB7456）

## 购买地点

该开发板由不同供应商生产，存在一些变种（例如是否带气压计）。

:::tip
PX4兼容支持Betaflight OMNIBUSF4SD目标的开发板（如果产品页面显示_OMNIBUSF4SD_，该板应能与PX4配合使用）。
:::

:::tip
任何带有Omnibus F4标识的衍生版（例如仿制品）也都应该可以使用。但这些开发板的电源分配质量参差不齐。
:::

以下开发板经过测试且已知可用：

- [Hobbywing XRotor Flight Controller F4](https://www.hobbywing.com/en/products/info.html?id=164)

  ::: info
  该开发板可直接安装在[Hobbywing XRotor Micro 40A 4in1 ESC](https://www.hobbywing.com/en/products/info.html?id=116)上而无需焊接。该电调板还可为Omnibus开发板供电。
  :::

  购买渠道：

  - [Hobbywing XRotor F4 Flight Controller w/OSD](https://www.getfpv.com/hobbywing-xrotor-f4-flight-controller-w-osd.html) (getfpv)

- Original Airbot Omnibus F4 SD

  购买渠道：

  - [Airbot (CN manufacturer)](https://store.myairbot.com/omnibusf4prov3.html)
  - [Ready To Fly Quads (US reseller)](https://quadsrtf.com/product/flip-32-f4-omnibus-rev-2/)

配件包括：

- [ESP8266 WiFi Module](../telemetry/esp8266_wifi_module.md) 用于MAVLink遥测
  需要连接的引脚：GND, RX, TX, VCC和CH-PD（CH-PD接3.3V）。波特率为921600。

## 接口

不同厂商（基于此设计）的板卡可能具有显著不同的布局。  
不同版本的布局/丝印如下所示。

### Airbot Omnibus F4 SD

以下是 Airbot Omnibus F4 SD (V1) 的丝印，显示顶部和底部。

![Omnibus F4 SD v1 丝印顶部](../../assets/flight_controller/omnibus_f4_sd/silk-top.jpg)
![Omnibus F4 SD v1 丝印底部](../../assets/flight_controller/omnibus_f4_sd/silk-bottom.jpg)

### Hobbywing XRotor Flight Controller F4

以下是 Hobbywing XRotor Flight Controller F4 的丝印。

![Hobbywing XRotor Flight Controller F4 丝印](../../assets/flight_controller/omnibus_f4_sd/hobbywing_xrotor_silk.png)

## 引脚分配

### 无线电控制

RC通过以下端口之一连接：

- UART1
- SBUS/PPM端口（通过反相器连接，内部连接至UART1）

::: info
某些Omnibus F4板通过跳线将MCU的SBUS和PPM连接至单个引脚。使用前请将跳线或焊桥设置到对应的MCU引脚。
:::

### UARTs

- UART6: GPS端口

  - TX: MCU引脚PC6
  - RX: MCU引脚PC7

  - Airbot Omnibus F4 SD引脚分配位于端口J10（TX6/RX6）：

  ![Omnibus F4 SD UART6](../../assets/flight_controller/omnibus_f4_sd/uart6.jpg)

- UART4

  - TX: MCU引脚PA0
  - RX: MCU引脚PA1
  - 57600波特率
  - 可配置为`TELEM 2`端口
  - Airbot Omnibus F4 SD引脚分配：
    - TX: RSSI引脚
    - RX: PWM输出5

  ![Omnibus F4 SD UART4](../../assets/flight_controller/omnibus_f4_sd/uart4.jpg)

  ![Omnibus F4 SD UART4俯视图](../../assets/flight_controller/omnibus_f4_sd/uart4-top.jpg)

### I2C

可通过以下方式访问一个I2C端口：

- SCL: MCU引脚PB10（可能标记为TX3）
- SDA: MCU引脚PB11（可能标记为RX3）

::: info
两个信号（时钟和数据）都需要外部上拉电阻
例如可使用2.2kΩ上拉电阻连接外部磁力计
:::

- Airbot Omnibus F4 SD引脚分配位于端口J10（SCL [时钟]/SDA [数据]）：
  <img src="../../assets/flight_controller/omnibus_f4_sd/uart6.jpg" title="Omnibus F4 SD UART6" />

以下是示例实现。我使用Spektrum插头从DSM端口获取3.3V，通过2.2kΩ电阻将3.3V+连接到每条线路。

![Omnibus F4 SD上拉](../../assets/flight_controller/omnibus_f4_sd/pullup-schematic.jpg)

![Omnibus F4 SD上拉实现](../../assets/flight_controller/omnibus_f4_sd/pullup.jpg)

## 串口映射

| UART   | 设备       | 端口       |
| ------ | ---------- | ---------- |
| USART1 | /dev/ttyS0 | SerialRX   |
| USART4 | /dev/ttyS1 | TELEM1     |
| USART6 | /dev/ttyS2 | GPS        |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->

## 遥控遥测

Omnibus支持通过[FrSky 遥测](../peripherals/frsky_telemetry.md)或[CRSF Crossfire 遥测](#crsf_telemetry)将遥测数据发送到遥控器。

<a id="crsf_telemetry"></a>

### CRSF Crossfire 遥测

[TBS CRSF 遥测](../telemetry/crsf_telemetry.md)可用于将飞行控制器（机体姿态、电池、飞行模式和GPS数据）的遥测数据发送到如Taranis等遥控器。

与[FrSky 遥测](../peripherals/frsky_telemetry.md)相比的优势包括：

- 仅需单个UART即可同时用于遥控和遥测。
- CRSF协议优化了低延迟性能。
- 150 Hz遥控器刷新率。
- 信号无需反相，因此无需（外部）反相逻辑电路。

::: info
如果使用CRSF遥测，需要构建自定义的PX4固件。
相比之下，FrSky遥测可以使用预构建的固件。
:::

对于Omnibus，我们推荐使用[TBS Crossfire Nano RX](http://team-blacksheep.com/products/prod:crossfire_nano_rx)，因为其专为小型四旋翼设计。

在手持遥控器（如Taranis）上还需要一个[发射模块](http://team-blacksheep.com/shop/cat:rc_transmitters#product_listing)。
该模块可插入遥控器后部。

::: info
上述引用链接包含TX/RX模块的文档。
:::

#### 设置

将Nano RX与Omnibus引脚按如下方式连接：

| Omnibus UART1 | Nano RX |
| ------------- | ------- |
| TX            | Ch2     |
| RX            | Ch1     |

接下来更新TX/RX模块以使用CRSF协议并设置遥测功能。
具体操作详见[TBS Crossfire 手册](https://www.team-blacksheep.com/tbs-crossfire-manual.pdf)（搜索'Setting up radio for CRSF'）。

#### PX4 CRSF 配置

需要构建自定义固件才能使用CRSF。
更多信息请参见[CRSF 遥测](../telemetry/crsf_telemetry.md#px4-configuration)。

## 电路图

电路图由 [Airbot](https://myairbot.com/) 提供：[OmnibusF4-Pro-Sch.pdf](http://bit.ly/obf4pro)。

<a id="bootloader"></a>

## PX4 引导加载程序更新

开发板预装了 [Betaflight](https://github.com/betaflight/betaflight/wiki)。
在安装 PX4 固件之前，必须烧录 _PX4 引导加载程序_。
下载 [omnibusf4sd_bl.hex](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/omnibus_f4_sd/omnibusf4sd_bl_d52b70cb39.hex) 引导加载程序二进制文件，并阅读 [此页面](../advanced_config/bootloader_update_from_betaflight.md) 获取烧录说明。

## 构建固件

要[构建 PX4](../dev_setup/building_px4.md) 为此目标：

```
make omnibus_f4sd_default
```

## 安装PX4固件

您可以使用预构建固件或自定义固件。

:::warning  
如果您在无线电系统中使用[CRSF Telemetry](../telemetry/crsf_telemetry.md#px4-configuration)（如上所述），则必须使用自定义固件。
:::

固件可以通过以下常规方式之一进行安装：

- 构建并上传源代码

  ```
  make omnibus_f4sd_default upload
  ```

- 使用 _QGroundControl_ [加载固件](../config/firmware.md)。

## 配置

除了[基本配置](../config/index.md)外，以下参数也很重要：

| 参数                                                              | 设置                                                                                                                 |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| [SYS_HAS_MAG](../advanced_config/parameter_reference.md#SYS_HAS_MAG)   | 由于该板没有内部磁力计，因此应将其禁用。如果连接了外部磁力计，可以启用它。 |
| [SYS_HAS_BARO](../advanced_config/parameter_reference.md#SYS_HAS_BARO) | 如果您的板没有气压计，请禁用此参数。                                                                   |

## 更多信息

[This page](https://blog.dronetrest.com/omnibus-f4-flight-controller-guide/) 提供了引脚分配和设置说明的详细概述。