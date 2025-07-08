# CAN

[控制器局域网（CAN）](https://en.wikipedia.org/wiki/CAN_bus) 是一种可靠的有线网络，允许无人机组件（如飞控、电调、传感器和其他外设）彼此通信。
由于其设计具有民主性和差分信号，即使在长电缆长度（大型机体）上也非常可靠，并避免了单点故障。
CAN 还允许外设的状态反馈和通过总线进行便捷的固件升级。

PX4 支持两种与 CAN 设备通信的软件协议：

- [DroneCAN](../dronecan/index.md)：PX4 推荐用于大多数常见配置。
  它得到了 PX4 的良好支持，是一款成熟的产品，具有广泛的外设支持，并经过多年的测试。
- [Cyphal](https://opencyphal.org)：PX4 支持正在进行中。
  Cyphal 是一个更新的协议，允许更大的灵活性和配置，特别是在较大和更复杂的机体上。
  目前尚未被广泛采用。

::: info
DroneCAN 和 Cyphal 都源自一个名为 UAVCAN 的早期项目。
2022 年该项目分裂为两个：原始版本的 UAVCAN（UAVCAN v0）更名为 DroneCAN，而更新的 UAVCAN v1 更名为 Cyphal。
这两种协议的区别请参见 [Cyphal vs. DroneCAN](https://forum.opencyphal.org/t/cyphal-vs-dronecan/1814)。
:::

:::warning
PX4 目前不支持其他 CAN 软件协议，如 KDECAN。
:::

## 接线

DroneCAN 和 Cyphal/CAN（事实上，所有 CAN 网络）的接线方式相同。

设备可以按任意顺序串联连接。
链路两端的两条数据线之间应连接一个 120Ω 的终端电阻。
飞控和某些 GNSS 模块内置终端电阻，因此应放置在链路的两端。
否则，可以使用终端电阻，如 [Zubax Robotics 提供的此款](https://shop.zubax.com/products/uavcan-micro-termination-plug?variant=6007985111069)，或者如果你有 JST-GH 压接工具，可以自行焊接一个。

下图展示了一个 CAN 总线连接飞控、4 个 CAN 电调和一个 GNSS 的示例。

![CAN 接线](../../assets/can/uavcan_wiring.svg)

该图未显示任何电源接线。
请参考制造商的说明书确认组件是否需要单独供电或可由 CAN 总线本身供电。

更多信息请参见 [Cyphal/CAN 设备互连](https://kb.zubax.com/pages/viewpage.action?pageId=2195476)（kb.zubax.com）。
尽管该文章是针对 Cyphal 协议编写的，但同样适用于 DroneCAN 硬件和任何其他 CAN 配置。
对于更复杂的场景，请参见 [关于 CAN 总线拓扑和终端](https://forum.opencyphal.org/t/on-can-bus-topology-and-termination/1685)。

### 连接器

Pixhawk 标准兼容的 CAN 设备使用 4 针 JST-GH 连接器。
在串联接线时，两个连接器分别用于输入和输出（飞控和某些内置终端的 GNSS 设备只有一个 JST-GH 连接器）。

其他（非 Pixhawk 兼容）设备可能使用不同的连接器。
但只要设备固件支持 DroneCAN 或 Cyphal，就可以使用。

### 冗余

DroneCAN 和 Cyphal/CAN 支持使用第二个（冗余）CAN 接口。
这完全是可选的，但可以提高连接的可靠性。
所有 Pixhawk 飞控都配备 2 个 CAN 接口；如果你的外设也支持 2 个 CAN 接口，建议都接上以提高安全性。

## 固件

CAN 外设可能运行专有或开源固件（请查阅制造商指南确认所需设置）。

PX4 可构建为在支持的 CAN 硬件上运行开源 DroneCAN 固件。
更多信息请参见 [PX4 DroneCAN 固件](../dronecan/px4_cannode_fw.md)。

## 支持与配置

[DroneCAN 设置与配置](../dronecan/index.md)

[PX4 DroneCAN 固件](../dronecan/px4_cannode_fw.md)

## 视频

### DroneCAN

DroneCAN（UAVCANv0）简介及 QGroundControl 中的实际配置示例：

<lite-youtube videoid="IZMTq9fTiOM" title="Intro to DroneCAN (UAVCANv0) and practical example with setup in QGroundControl"/>

### Cyphal

无人机用 UAVCAN v1（Cyphal）—— PX4 开发者峰会虚拟 2020

<lite-youtube videoid="6Bvtn_g8liU" title="UAVCAN v1 for drones — PX4 Developer Summit Virtual 2020"/>

---

在 NXP UAVCAN 板上使用 UAVCAN v1 与 PX4 的入门指南 —— PX4 开发者峰会虚拟 2020

<lite-youtube videoid="MwdHwjaXYKs" title="Getting started using UAVCAN v1 with PX4 on the NXP UAVCAN Board"/>

---

UAVCAN：一种高度可靠的发布-订阅协议，适用于硬实时车内网络 —— PX4 开发者峰会虚拟 2019

<lite-youtube videoid="MBtROivYPik" title="UAVCAN: a highly dependable publish-subscribe protocol for hard ..."/>