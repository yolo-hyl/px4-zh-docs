# Holybro PM03D 电源模块

PM03D 电源模块同时具备电源模块（PM）和电源分配板（PDB）的功能。除了为 Pixhawk v5X 和电调提供稳压电源外，它还会向飞控系统发送电池电压和电流信息，包括飞行控制器和电机的供电状态。

该电源模块通过 I2C 协议连接。专为基于 Pixhawk FMUv5X 和 FMUv6X 开放标准的飞控设计，包括 [Pixhawk 5X](../flight_controller/pixhawk5x.md)。

::: info
该 PM **不兼容** 需要模拟电源模块的飞控，包括：[Pixhawk 4](../flight_controller/pixhawk4.md)、[Durandal](../flight_controller/durandal.md)、[Pix32 v5](../flight_controller/holybro_pix32_v5.md) 等。
:::

![Pixhawk5x 立式图像](../../assets/hardware/power_module/holybro_pm03d/pm03d_pinout.jpg)

## 功能特点

- 即插即用，QGroundcontrol 和 Mission Planner 无需额外设置
- XT-30 接口和焊盘，便于电机电调连接
- 板载稳压器：双路 5.2V 与可选 8V/12V
- 5V/A 接口，用于供电给计算模块或外设设备
- 可选 8V 或 12V 三排针接口，用于外设设备供电

## 技术规格

- **最大输入电压**：6S 电池
- **额定电流**：60A
- **最大电流**：120A（<60 秒）
- **最大电流检测**：164A
- **通信协议**：I2C
- **尺寸**：84 x 78 x 12mm（不含线缆）
- **安装尺寸**：45 x 45mm
- **重量**：59g
- **连接接口**：
 - XT-60 电池接口
 - XT-30 电机及外设设备接口（电池电压）
 - 每个角落的焊盘（电池电压）
 - CLIK-Mate 2.0mm 飞控接口（5.2V/3A 独立 BEC）
 - JST GH 4针接口（5.2V/3A，与三排针共享 BEC）
 - 2x 三排针接口（5.2V/3A，与 JST GH 4针共享 BEC）
 - 2x 三排针接口（通过跳线选择 8V 或 12V，3A）

## 包装内容

- 1块 PM06 板
- 1根 80mm XT60 连接线（已预焊）
- 1个 220uF 63V 电解电容（已预焊）
- 1根 2.0mm 间距 CLIK-Mate 6针线缆（用于供电给飞控）
- 4针 JST GH 转 USB Type C 接口
- 4针 JST GH 转圆头插头（2.1*5.5mm）
- 4针 JST GH 转圆头插头（2.5*5.5mm）
- 4针杜邦线（2根）
- 尼龙隔离柱与螺母

<img src="../../assets/hardware/power_module/holybro_pm03d/pm03d_contents.jpg" width="650px" title="Pixhawk5x 立式图像" />

## 购买渠道

[从 Holybro 商店订购](https://holybro.com/products/pm03d-power-module)

## 接线/连接

![接线图](../../assets/hardware/power_module/holybro_pm02d/pm02d_pinout.png)

更多接线和连接信息请参考：[Holybro Pixhawk 5x 快速接线指南](../assembly/quick_start_pixhawk5x.md)