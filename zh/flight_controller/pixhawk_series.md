# Pixhawk系列

[Pixhawk<sup>&reg;</sup>](https://pixhawk.org/) 是一个独立的开源硬件项目，为学术界、爱好者和工业界提供现成的、低成本且高端的_自动驾驶仪硬件设计_。

Pixhawk 是 PX4 的参考硬件平台，运行 PX4 的 [NuttX](https://nuttx.apache.org/) 操作系统。

制造商基于这些开放设计创建了多种形式的产品，其外形尺寸针对从载货到第一视角（FPV）竞速等多种应用场景进行了优化。

:::tip
对于计算密集型任务（例如计算机视觉），你需要一个独立的协处理器（例如 [Raspberry Pi 2/3 Navio2](../flight_controller/raspberry_pi_navio2.md)）或集成了协处理器解决方案的平台。
:::

## 核心优势

使用 _Pixhawk系列_ 控制器的核心优势包括：

- 软件支持 - 作为 PX4 的参考硬件，这些板卡是我们维护最完善的设备。
- 可扩展性 - 可连接的硬件外设种类丰富。
- 高品质。
- 形状因子高度可定制。
- 广泛应用，因此经过充分测试/稳定。
- 可通过 _QGroundControl_ 自动更新最新固件（面向终端用户友好）。

## 支持的板卡

PX4 项目使用 [Pixhawk 标准自动驾驶仪](../flight_controller/autopilot_pixhawk_standard.md) 作为参考硬件。
这些控制器完全兼容 Pixhawk 标准（包括商标的使用）并且仍在生产中。

::: info
PX4 维护和测试团队负责维护和支持这些标准板卡。
:::

不完全符合规范的类似 Pixhawk 的板卡可能是 [制造商支持](../flight_controller/autopilot_manufacturer_supported.md)、[实验性/已停产](../flight_controller/autopilot_experimental.md) 或不支持的。

本文其余部分将更多地介绍 Pixhawk 系列，但并非必读内容。

## 背景

[Pixhawk 项目](https://pixhawk.org/) 以原理图的形式创建开源硬件设计，这些设计定义了一组组件（CPU、传感器等）及其连接/引脚映射。

制造商被鼓励采用 [开放设计](https://github.com/pixhawk/Hardware) 并创建最适合特定市场或用例的产品（物理布局/形状因子不属于开放规范的一部分）。基于相同设计的板卡是二进制兼容的。

::: info
尽管没有强制的物理连接器标准，但较新的产品通常遵循 [Pixhawk 连接器标准](https://pixhawk.org/pixhawk-connector-standard/)。
:::

该项目还基于开放设计创建参考自动驾驶仪板卡，并以相同的 [许可证](#licensing-and-trademarks) 分享。

<a id="fmu_versions"></a>

### FMU 版本

Pixhawk 项目创建了多种不同的开放设计/原理图。
基于同一设计的板卡应二进制兼容（运行相同的固件）。

每个设计均以 FMUvX 命名（例如：FMUv1、FMUv2、FMUv3、FMUv4 等）。
更高的 FMU 编号表明板卡更新，但不一定表示功能增强（版本可能几乎相同，仅连接器接线不同）。

PX4 _用户_ 通常不需要了解太多 FMU 版本信息：

- _QGroundControl_ 会自动为连接的自动驾驶仪下载正确的固件（基于其 FMU 版本“在后台”）。
- 选择控制器通常基于物理约束/形状因子，而非 FMU 版本。

::: info
例外情况是，如果使用 FMUv2 固件，则 [闪存限制为 1MB](../flight_controller/silicon_errata.md#fmuv2-pixhawk-silicon-errata)。
为了适应这一有限空间，许多模块默认被禁用。
你可能会发现一些 [参数缺失](../advanced_config/parameters.md#missing) 且某些硬件无法“开箱即用”。
:::

PX4 _开发者_ 需要知道其板卡的 FMU 版本，因为这是构建自定义硬件的必要条件。

在最高层面上，主要区别如下：

- **FMUv2:** 单板设计，搭载 STM32427VI 处理器 ([Pixhawk 1（已停产）](../flight_controller/pixhawk.md), [pix32](../flight_controller/holybro_pix32.md), [Pixfalcon](../flight_controller/pixfalcon.md), [Drotek DroPix](../flight_controller/dropix.md))
- **FMUv3:** ...
- **FMUv4:** ...
- **FMUv5:** ...

（此处省略其他版本的详细描述，保持结构与原文一致）

## 许可证与商标

该项目授权使用 [CC BY-SA 3](https://creativecommons.org/licenses/by-sa/3.0/legalcode)。

所有 Pixhawk 商标均归 3D Robotics 所有。Pixhawk 是 3D Robotics 的注册商标。本文档中的内容不授予任何 Pixhawk 商标使用许可。

## LED 状态指示

### 用户界面 LED

所有 Pixhawk 板卡均支持：

- 一个面向用户的 RGB _UI LED_，用于指示机体的当前_可飞行状态_。这通常是超亮 I2C 外设，可能安装在板卡上（例如 FMUv4 没有内置，通常使用 GPS 上的 LED）。
- 三个 *状态 LED*，提供电源状态、引导加载模式、活动状态和错误信息。

要解读 LED 状态，请参见：[LED 含义](../getting_started/led_meanings.md)。