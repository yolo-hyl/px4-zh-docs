# 飞行模式配置

本主题解释如何将[飞行模式](../getting_started/px4_basic_concepts.md#flight-modes)和其他功能映射到遥控器的开关上。

:::tip
在设置飞行模式之前，您需要已经完成以下步骤：
- [配置遥控器](../config/radio.md)
- [设置遥控发射机](#遥控发射机设置)以将模式开关的物理位置编码为单个通道。
  我们为流行的*Taranis*发射机提供了示例[如下](#taranis-setup-3-way-switch-configuration-for-single-channel-mode)（如果使用其他发射机，请查阅对应文档）。
:::

## 我应该设置哪些飞行模式和开关？

飞行模式提供不同类型的*自动驾驶辅助飞行*和*完全自主飞行*。
您可以设置[适用于机体的任何飞行模式](../flight_modes/index.md#flight-modes)（或不设置）。

大多数用户应设置以下模式和功能，因为这些模式使机体更易于操作且飞行更安全：

- **位置模式** — 手动飞行时最简单且最安全的模式。
- **返航模式** — 沿安全路径返回起飞点并着陆。
- (仅限VTOL) **VTOL切换开关** — 在VTOL机体上切换固定翼和多旋翼飞行配置。

常见的映射还包括：

- **任务模式** — 此模式执行由地面控制站发送的预编程任务。
- <a id="kill_switch"></a> [杀车开关](../config/safety.md#kill-switch) - 立即停止所有电机输出（机体将坠毁，这在某些情况下可能比继续飞行更可取）。

## 飞行模式选择

PX4允许您指定一个"模式"通道，并选择最多6个飞行模式，这些模式将根据通道的PWM值激活。
您还可以单独指定通道来映射杀车开关、返航模式和外部模式。

要配置单通道飞行模式选择：

1. 启动*QGroundControl*并连接机体。
1. 打开遥控器。
1. 选择 **"Q"图标 > 机体设置 > 飞行模式**（侧边栏）打开_飞行模式设置_。

   ![飞行模式单通道](../../assets/qgc/setup/flight_modes/flight_modes_single_channel.jpg)

1. 设置 *飞行模式参数*:
   * 选择 **模式通道**（此处显示为通道5，但具体取决于您的发射机配置）。
   * 移动用于模式选择的遥控器开关（或多个开关）经过所有可用位置。
     与当前开关位置匹配的模式槽将被高亮显示（此处为*飞行模式1*）。
     ::: info
     虽然可以在6个槽位中设置飞行模式，但只有映射到开关位置的通道才会被高亮/使用。
     :::
   * 为每个开关位置选择要触发的飞行模式。
1. 设置 *开关参数*:
   * 选择要映射到特定动作的通道 - 例如：*返航*模式、*杀车开关*、*外部*模式等。（如果遥控器有备用开关和通道）。
   
1. 测试模式是否映射到正确的遥控器开关:
   * 检查 *通道监控器* 以确认每个开关改变的预期通道。
   * 依次选择遥控器上的每个模式开关，并检查是否激活了所需的飞行模式（*QGroundControl*上激活的模式文本会变为黄色）。

所有值在更改时会自动保存。

## 遥控发射机设置

本节包含针对*Taranis*的一些可能设置配置。
*QGroundControl* 可能[包含其他发射机的设置信息](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/flight_modes.html#transmitter-setup)。

<a id="taranis_setup"></a>

### Taranis 设置：3位开关配置用于单通道模式

如果只需支持在两个或三个模式间切换，可以将模式映射到单个3位开关的位置。
如下展示如何将Taranis 3位"SD"开关映射到通道5。

::: info
此示例展示如何设置流行的*FrSky Taranis*发射机。
其他发射机的设置会有所不同。
:::

打开Taranis UI的 **MIXER** 页面并向下滚动到 **CH5**，如下所示：

![Taranis - 将通道映射到开关](../../assets/qgc/setup/flight_modes/single_channel_mode_selection_1.png)

按 **ENT(ER)** 编辑 **CH5** 配置，然后将 **Source** 更改为 *SD* 按钮。

![Taranis - 配置通道](../../assets/qgc/setup/flight_modes/single_channel_mode_selection_2.png)

完成！
通道5现在将根据 **SD** 开关的三个不同位置输出3种不同的PWM值。

*QGroundControl* 配置如上一节所述。

### Taranis 设置：多开关配置用于单通道模式

大多数发射机没有6位开关，因此如果需要支持的模式数量超过可用开关位置数（最多6个），则必须使用多个开关表示。
通常通过将2位和3位开关的位置编码到单个通道上实现，使每个开关位置产生不同的PWM值。

在FrSky Taranis上，此过程涉及将"逻辑开关"分配给两个真实开关的每个位置组合。
每个逻辑开关随后分配到同一通道的不同PWM值。

下图展示了如何使用 *FrSky Taranis* 发射机完成此操作。

<!-- [youtube](https://youtu.be/scqO7vbH2jo) Video has gone private and is no longer available -->
<!-- @[youtube](https://youtu.be/BNzeVGD8IZI?t=427) - video showing how to set the QGC side - at about 7mins and 3 secs -->

<lite-youtube videoid="TFEjEQZqdVA" title="Taranis 模式开关"/>

*QGroundControl* 配置如[上文所述](#飞行模式选择)。

## 进一步信息

* [飞行模式概述](../flight_modes/index.md)
* [遥控器配置](../config/radio.md)
* [安全设置](../config/safety.md)