# CRSF 遥测 (TBS Crossfire Telemetry)

CRSF 是一种遥测协议，可用于 [遥控器控制](../getting_started/rc_transmitter_receiver.md) 以及通过兼容的遥控器获取机体/飞控的遥测信息。

该协议由 [Team BlackSheep](https://www.team-blacksheep.com/) 为其 Crossfire 遥控系统开发，同时被 [ExpressLRS (ELRS)](https://www.expresslrs.org/) 遥控系统使用。它是一种双向协议，仅需一个 UART 即可同时传输遥控信号和遥测数据。

[支持的遥测消息详见此处](#telemetry-messages)，包括：飞行模式、电池电量、GPS 数据、遥控信号强度、速度、高度等。

::: info
如果不需要遥测功能，可将 TBS Crossfire 连接到 `RCIN` 接口并配置接收机使用 S.BUS。
Crossfire 遥控系统也可作为 [遥测电台](../telemetry/index.md) 使用。
:::

::: warning
PX4 默认不包含 CRSF 协议支持。
[下方说明](#px4-configuration) 解释了如何构建并上传包含所需模块的自定义 PX4 固件。
:::

## 无线电系统设置

要使用CRSF遥测功能，需要配备[TBS Crossfire无线电系统](#tbs-radio-systems)或[ExpressLRS无线电系统](#expresslrs-radio-systems)，这些系统包含[遥控器](#rc-controllers)（带发射模块）和接收模块（需来自同一厂商）。

::: info
历史上，遥控无线电系统由地面控制器向机体接收器发送信号组成。
尽管现在很多无线电系统是双向通信的，但地面模块仍可能被称为发射机（transmitter），机体端模块仍可能被称为接收器（receiver）。
:::

通常需要分别设置和配置发射机与接收机，然后将它们进行绑定（_bind_）。

发射机可能是[遥控器](#rc-controllers)的集成部分，也可能是一个单独模块插入控制器使用。
如果是单独模块，可能需要升级发射模块固件到支持CRSF的版本（如OpenTX或EdgeTx）。
无论哪种情况，都需要配置发射机以启用CRSF功能。

接收机必须通过[接线](#wiring)连接到飞控的备用端口（UART）。
然后即可将发射机和接收机进行绑定。

上述步骤的详细说明请参考：

- [TBS Crossfire手册](https://www.team-blacksheep.com/tbs-crossfire-manual.pdf)
- [Express LRS: 快速入门](https://www.expresslrs.org/quick-start/getting-started/)

### 接线

选定飞控UART的TX和RX引脚应连接到接收机的独立通道。
信号通常是不反相的，可直接连接（无需在电缆中添加反相逻辑）。
不过建议查阅接收机具体型号的说明书！

#### TBS接收机接线

对于TBS接收机，飞控UART和接收机的接线方式如下（假设使用TBS Nano RX）：

| 飞行控制器UART | Nano RX |
| ------------- | ------- |
| TX            | Ch2     |
| RX            | Ch1     |

#### ExpressLRS接收机接线

对于ExpressLRS接收机，飞控UART的接线方式如下（详细接线说明请见[此处](https://www.expresslrs.org/quick-start/receivers/wiring-up/)）：

| 飞行控制器UART | ExpressLRS |
| ------------- | ---------- |
| TX            | RX         |
| RX            | TX         |
| VCC           | VCC        |
| GND           | GND        |## PX4配置

### 固件配置/构建

CRSF遥测支持未包含在任何PX4固件中。
要使用此功能，必须构建并上传包含[crsf-rc](../modules/modules_driver_radio_control.md#crsf-rc)模块且移除[rc_input](../modules/modules_driver_radio_control.md#rc-input)模块的自定义固件。

步骤如下：

1. [设置开发环境](../dev_setup/dev_env.md)以构建PX4。

   在此过程中，您将通过`git`将源代码克隆到 **PX4-Autopilot** 目录中。

1. 打开终端并`cd`进入`PX4-Autopilot`目录：

   ```sh
   cd PX4-Autopilot
   ```

1. 使用`boardconfig`选项为您的`make`目标启动[PX4板配置工具（`menuconfig`）](../hardware/porting_guide_config.md#px4-menuconfig-setup)（此处目标为[ARK Electronics ARKV6X](../flight_controller/ark_v6x.md)飞控）：

   ```sh
   make ark_fmu-v6x_default boardconfig
   ```

1. 在PX4板配置工具中：

   - 禁用默认的`rc_input`模块
     1. 导航到`drivers`子菜单，然后向下滚动高亮`rc_input`。
     1. 按Enter键移除`rc_input`复选框的`*`。
   - 启用`crsf_rc`模块
     1. 滚动高亮`RC`子菜单，按Enter键打开。
     1. 滚动高亮`crsf_rc`并按Enter键启用。

   保存并退出PX4板配置工具。

1. [构建PX4源代码](../dev_setup/building_px4.md)并应用更改（假设您使用ARKV6X）：

   ```sh
   make ark_fmu-v6x_default
   ```

这将构建您的自定义固件，现在需要将其上传到飞控。

### 固件上传

上传自定义固件前，首先通过USB将飞控连接到开发计算机。

您可以通过构建过程中的`upload`选项上传固件：

```sh
make ark_fmu-v6x_default upload
```

或使用QGroundControl安装固件，具体方法请参见[Firmware > Installing PX4 master, beta, or custom firmware](../config/firmware.md#installing-px4-main-beta-or-custom-firmware)。

### 参数配置

[查找并设置](../advanced_config/parameters.md)以下参数：

1. [RC_CRSF_PRT_CFG](../advanced_config/parameter_reference.md#RC_CRSF_PRT_CFG) — 设置为连接CRSF接收器的端口（例如 `TELEM1`）。

   此参数[配置串口](../peripherals/serial_configuration.md)使用CRSF协议。
   注意某些串口可能已有[默认串口映射](../peripherals/2023-04-05-serial_configuration.md#default-serial-port-configuration)或[默认MAVLink串口映射](../peripherals/mavlink_peripherals.md#default-mavlink-ports)，必须先取消映射才能分配给CRSF。
   例如，若使用`TELEM1`或`TELEM2`，首先需要修改[MAV_0_CONFIG](../advanced_config/parameter_reference.md#MAV_0_CONFIG)或[MAV_1_CONFIG](../advanced_config/parameter_reference.md#MAV_1_CONFIG)以停止占用这些端口。

   无需设置端口波特率，此参数由驱动程序配置。

1. [RC_CRSF_TEL_EN](../advanced_config/parameter_reference.md#RC_CRSF_TEL_EN) — 启用以激活Crossfire遥测。

### 遥控器设置

[遥控器配置](../config/radio.md)解释如何将遥控器的姿态控制杆（横滚、俯仰、偏航、油门）映射到通道，并校准所有其他发射机控制/RC通道的最小值、最大值、中立点和反向设置。

## 遥控器

遥控器可能包含与遥控设备集成的发射模块，也可能是一个单独的模块，需要插入到控制器中。

支持TBS Crossfire和ExpressLRS发射模块的遥控器：

- [FrSky Taranis X9D Plus](https://www.frsky-rc.com/product/taranis-x9d-plus-2/) 具有外部模块插槽，可用于兼容"JR模块插槽"的TBS或ExpressLRS发射模块。
  需要安装支持CRSF的OpenTX软件，并启用外部模块和CRSF功能。
- [Radiomaster TX16S](https://www.radiomasterrc.com/collections/tx16s-mkii) 内置ExpressLRS发射模块。
  同时具有外部模块插槽，可用于兼容"JR模块插槽"的TBS或ExpressLRS发射模块。
  支持运行OpenTX和EdgeTx软件，两者均可支持CRSF。

## TBS Radio Systems

[TBS Crossfire Radio Systems are listed here](https://www.team-blacksheep.com/shop/cat:cat_crossfire#product_listing).
A few options are listed below.

发射模块：

- [TBS CROSSFIRE TX - LONG RANGE R/C TRANSMITTER](https://www.team-blacksheep.com/products/prod:crossfire_tx)

接收器：

- [TBS Crossfire Nano RX](http://team-blacksheep.com/products/prod:crossfire_nano_rx) - 专为小型四旋翼无人机设计。

## ExpressLRS无线电系统

Express LRS在[Hardware Selection](https://www.expresslrs.org/hardware/hardware-selection/)页面提供无线电系统指南。  
以下列出了一些已测试的选项。

发射模块：

- TBD

接收器：

- [ExpressLRS Matek Diversity RX](http://www.mateksys.com/?portfolio=elrs-r24)

这在[Reptile Dragon 2组装日志](../frames_plane/reptile_dragon_2.md)中使用。  
请参见章节 [ELRS Rx](../frames_plane/reptile_dragon_2.md#elrs-rx) 和 [Radio Setup](../frames_plane/reptile_dragon_2.md#radio-setup)。# 遥测消息

下表列出了支持的遥测消息及其来源（该表格摘自 [TBS Crossfire Manual: "Available sensors with OpenTX"](https://www.team-blacksheep.com/tbs-crossfire-manual.pdf))。

| 数据点 | 描述 | 数据来源 |
|---------|--------------------------------------------------|--------------------------------|
| 1RSS | 上行链路 - 天线1接收信号强度(RSSI) | TBS CROSSFIRE RX |
| 2RSS | 上行链路 - 天线2接收信号强度(RSSI) | TBS CROSSFIRE RX |
| RQly | 上行链路 - 链路质量(有效数据包) | TBS CROSSFIRE RX |
| RSNR | 上行链路 - 信噪比 | TBS CROSSFIRE RX |
| RFMD | 上行链路 - 更新速率，0=4Hz，1=50Hz，2=150Hz | TBS CROSSFIRE RX |
| TPWR | 上行链路 - 发射功率 | TBS CROSSFIRE TX |
| TRSS | 下行链路 - 天线信号强度 | TBS CROSSFIRE TX |
| TQly | 下行链路 - 链路质量(有效数据包) | TBS CROSSFIRE TX |
| TSNR | 下行链路 - 信噪比 | TBS CROSSFIRE TX |
| ANT | 仅用于调试的传感器 | TBS CROSSFIRE TX |
| GPS | GPS坐标 | TBS GPS / FC |
| Alt | GPS高度 | TBS GPS / FC |
| Sats | 已捕获GPS卫星 | TBS GPS / FC |
| Hdg | 磁向位 | TBS GPS / FC |
| RXBt | 电池电压 | TBS GPS / FC/ CROSSFIRE RX/ CORE |
| Curr | 电流消耗 | TBS GPS / FC// CORE |
| Capa | 当前消耗 | TBS GPS / FC/ CORE |
| Ptch | 飞控俯仰角度 | FC |
| Roll | 飞控滚转角度 | FC |
| Yaw | 飞控偏航角度 | FC |
| FM | 飞行模式 | FC |
| VSPD | 气压计 | FC |## 另请参阅

- [TBS Crossfire 手册](https://www.team-blacksheep.com/tbs-crossfire-manual.pdf)
- [ExpressLRS 文档](https://www.expresslrs.org/quick-start/getting-started/)
- [FrSky 通信遥测](../peripherals/frsky_telemetry.md)
- [遥控器设置](../config/radio.md)
- [遥控系统](../getting_started/rc_transmitter_receiver.md)