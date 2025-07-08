# Septentrio mosaic-go

Septentrio mosaic-go 接收器是用于其 mosaic-X5 和 mosaic-H 接收器模块的评估套件。
由于体积小巧且重量轻，它们非常适用于自动飞行器应用。
可用的型号包括 [mosaic-go](https://www.septentrio.com/en/products/gps/gnss-receiver-modules/mosaic-go-evaluation-kit)
和 [mosaic-go heading](https://www.septentrio.com/en/products/gps/gnss-receiver-modules/mosaic-h-evaluation-kit)。

![Mosaic go 高精度GNSS接收器模块](../../assets/hardware/gps/septentrio_sbf/mosaic-go.png)

其特性包括以下内容：

- 高更新率（>100 Hz）和低延迟，这对自主应用的控制系统至关重要
- 可靠的厘米级定位
- 通过P(Y)码的L2完整支持
- 尺寸：71 x 59 x 12 mm ± 1mm
- 重量：58g ± 1g## 购买

mosaic-go 套件可在 Septentrio 的 [官方商店](https://web.septentrio.com/l/858493/2022-04-19/xgrnz) 购买：

- [mosaic-go 航向GNSS模块评估套件](https://web.septentrio.com/l/858493/2022-04-19/xgrp9)
- [mosaic-go GNSS模块接收机评估套件](https://web.septentrio.com/l/858493/2022-04-19/xgrpd)

Septentrio 其他 PX4 支持的设备：

- [AsteRx OEM with Robotics Interface Board](../gps_compass/septentrio_asterx-rib.md)

## mosaic-go 评估套件内容

- 1 个焊接在接口板上的 mosaic-H 或 mosaic-X5 模块，封装在金属外壳内
- 1 条 USB 数据线
- 6 针 COM1 开放式电缆
- 4 针 COM2 开放式电缆
- 帮助用户指南卡## 物理接口

| 类型            | 标签        | 用途                                      |
| --------------- | ------------ | ---------------------------------------- |
| USB Micro-B     | USB          | USB通信和供电              |
| RSV USB Micro-B | RSV          | 预留且不应使用          |
| SMA             | RF-IN\{1,2\} | 主（和辅助）天线连接 |
| 6针JST       | 串口       | 串口通信和供电           |
| 4针JST       | 串口       | 串口通信                     |
| microSD         | TF CARD      | 串口通信                     |

> 双天线功能仅在基于mosaic-H的接收器上可用。

### 6针连接器

_连接器类型：GH连接器，1.25mm间距，6针。配合连接器外壳：GHR-06V-S。_

| 引脚名称 | 方向 | 电平      | 描述               | 注释                                                           |
| -------- | --------- | ---------- | ------------------------- | ----------------------------------------------------------------- |
| VCC      | PWR       | 4.75V-5.5V | 主电源         |                                                                   |
| GND      |           | 0          | 接地                    |                                                                   |
| TXD1     | Out       | 3V3_LVTTL  | 串口COM1发送线 | 直接连接到内部mosaic的TXD1                      |
| RXD1     | In        | 3V3_LVTTL  | 串口COM1接收线  | 直接连接到内部mosaic的RXD1                      |
| PPS      | Out       | 3V3_LVTTL  | PPS输出                 | mosaic的PPSO转换为3.3V                                |
| EVENT    | In        | 3V3_LVTTL  | 事件计时器输入         | 通过3V3到1V8电平转换器连接到mosaic的EVENTA |

### 4针连接器

_连接器类型：GH连接器，1.25mm间距，4针。配合连接器外壳：GHR-04V-S。_

| 引脚名称 | 方向 | 电平     | 描述               | 注释                                         |
| -------- | --------- | --------- | ------------------------- | ----------------------------------------------- |
| NRST     | In        | 3V3_LVTTL | 复位输入               | 直接连接到内部mosaic的nRST_IN |
| TXD2     | Out       | 3V3_LVTTL | 串口COM2发送线 | 直接连接到内部mosaic的TXD2    |
| RXD2     | In        | 3V3_LVTTL | 串口COM2接收线  | 直接连接到内部mosaic的RXD2    |
| GND      |           | 0         | 接地                    |                                                 |## PX4 配置

PX4 配置相关内容请参见 [Septentrio GNSS 接收机](../gps_compass/septentrio.md)。

## 硬件连接示例 Pixhawk 4

1. 确保接收机供电至少3.3V。可通过USB Micro-B接口或6针JST接口供电。
2. 将一个或两个GNSS天线连接到mosaic-go的RF-IN端口。
3. 将6针连接器（COM1）连接到Pixhawk的_GPS MODULE_端口。
   这将为mosaic-go供电，通过此单线连接可将单天线和双天线信息发送到Pixhawk 4。

![接线图，Pixhawk 4 - mosaic-go](../../assets/hardware/gps/septentrio_sbf/mosaic-go_wiring.png "Wiring diagram, Pixhawk 4 - mosaic-go")

::: warning
确保JST电缆接线正确，因为这不是标准电缆：

![JST电缆接线](../../assets/hardware/gps/septentrio_sbf/jst_cable.png)
:::

### 双天线

姿态（航向/俯仰）可通过主天线和aux1 GNSS天线之间基线的方向计算得出。

![多天线姿态确定设置](../../assets/hardware/gps/septentrio_sbf/multi-antenna_attitude_setup.png)

要启用多天线姿态确定，请按照以下步骤操作：

1. 使用长度相近的电缆将两根天线安装到机体上。默认天线配置如图所示。
   该配置要求将天线沿机体纵轴对齐，主天线位于AUX1天线后方。
   为获得最佳精度，请尽量增加天线间距，并避免天线ARPs之间的显著高度差。
2. 实际应用中，两根天线ARPs可能不在同一高度，或主-aux1基线可能不完全平行或垂直于机体纵轴。
   这会导致计算出的姿态角出现偏移。
   这些偏移可通过Septentrio驱动在PX4中提供的航向参数进行补偿。

::: info
为获得最佳航向结果，两根天线之间的距离应至少为30cm/11.8英寸（理想距离为50cm/19.7英寸或更大）。

如需了解更多双天线设置的配置信息，请参考我们的[知识库](https://support.septentrio.com/l/858493/2022-04-19/xgrqd)或[硬件手册](https://web.septentrio.com/l/858493/2022-04-19/xgrql)。
:::

### Web应用

mosaic-H GPS/GNSS接收模块附带完整的接口、命令和数据消息文档。
包含的GNSS接收控制与分析软件[RxTools](https://web.septentrio.com/l/858493/2022-04-19/xgrqp)可进行接收机配置、监控以及数据记录和分析。

该接收机包含直观的网页用户界面，便于操作和监控，可从任何移动设备或计算机控制接收机。
网页界面还使用易于阅读的质量指标，适合在作业期间监控接收机运行状态。

![Septentrio网页用户界面](../../assets/hardware/gps/septentrio_sbf/septentrio_mosaic_a5_h_t_clas_gnss_module_receiverwebui.png)

## LED状态

| LED颜色       | 供电 | 存储卡已插入 | PVT解算 | 日志记录启用 |
| ------------- | :--: | :----------: | :-----: | :----------: |
| 红色          |  ✓   |              |         |              |
| 绿色          |  ✓   |      ✓       |         |              |
| 蓝色          |  ✓   |      ✓       |    ✓    |              |
| 紫色          |  ✓   |              |    ✓    |              |
| 紫色 + 蓝色   |  ✓   |      ✓       |    ✓    |      ✓       |
| 红色 + 绿色   |  ✓   |      ✓       |         |      ✓       |

:::tip
关于mosaic-go及其模块的详细信息，请参考[硬件手册](https://web.septentrio.com/l/858493/2022-04-19/xgrrd)或[Septentrio Support](https://support.septentrio.com/l/858493/2022-04-19/xgrrl)页面。
:::