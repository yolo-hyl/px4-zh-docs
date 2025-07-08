# RaccoonLab 电源连接器和电源管理单元

## CAN 电源连接器

CAN 电源连接器专为轻型无人机（UAV）等设备设计，通过 [CAN 电源线](https://docs.raccoonlab.co/guide/pmu/wires/) 实现 CAN 总线供电。

设备分为两种类型：

1. `CAN-MUX` 设备通过 XT30 接口向 CAN 总线供电。
   此类型设备有两种变体，接口数量不同。
2. `电源连接节点` 设计用于向负载和 CAN 总线传递最大 60A 电流，可测量负载的电压和电流。
   该设备作为 Cyphal/DroneCAN 节点运行。

请参考 RaccoonLab 官方文档的 [CAN 电源连接器](https://docs.raccoonlab.co/guide/pmu/power/) 页面。

**连接示例图**

示例如下：

- 第一个示例展示如何通过 MUX 使用外部高压电源和 5V CAN 电源为不同节点供电。
  [NODE](raccoonlab_nodes.md)（左侧较大节点）连接至高压电源（此处为 30 V）。
  在这种情况下，[uNODE](raccoonlab_nodes.md)（左侧小节点）直接由飞控供电。
- 第二个示例展示如何连接多个 [uNODEs](raccoonlab_nodes.md)，这些节点由飞控（5V）供电。
  如果这些节点由独立的 DCDC 供电，该 DCDC 也应连接至其中一个接口。

![RaccoonLab CAN 电源连接器示例图](../../assets/hardware/power_module/raccoonlab_can/raccoonlab_power_connector_example.png)

## 电源管理单元

![raccoonlab pmu ](../../assets/hardware/power_module/raccoonlab_can/raccoonlab_pmu.jpg)

该电路板通过 DroneCAN 接口监控电池（电压和电流），并允许控制充电、电源和负载。
对于需要控制无人机电源（包括主板计算机和充电过程）的应用场景可能非常有用。

请参考 RaccoonLab 官方文档的 [电源与连接性](https://docs.raccoonlab.co/guide/pmu/) 页面。

## 购买渠道

[RaccoonLab 商店](https://raccoonlab.co/store)

[Cyphal 商店](https://cyphal.store/search?q=raccoonlab)