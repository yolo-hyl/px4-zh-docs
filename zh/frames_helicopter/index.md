# 直升机

<LinkedBadge type="warning" text="Experimental" url="../airframes/#experimental-vehicles"/>

:::warning
直升机支持功能为[实验性](../airframes/index.md#experimental-vehicles)。
欢迎志愿者维护人员贡献新功能、新机身配置或其他改进！[contribution](../contribute/index.md)

存在以下问题：

- 对不同类型直升机的支持有限。
  例如，PX4不支持共轴/双旋翼直升机，以及RPM调节器和自转飞行等功能。

:::

<!-- image here please of PX4 helicopter -->

## 直升机类型

PX4支持通过最多4个混合器板舵机控制单旋翼混合器板的直升机，并配备：

- 机械分离式尾桨（由电调驱动），或
- 机械联动式尾桨（由尾桨电机舵机控制）。

允许的飞行操作和[飞行模式](../flight_modes_mc/index.md)与多旋翼相同。
需要注意的是（截至当前文档编写时），自主/引导模式下不支持负推力的3D飞行动作。

## 组装

核心自驾仪组件的组装方法对所有机身类型基本相同。
详见[基础组装](../assembly/index.md)。

直升机专用组装主要涉及电机和混合器板舵机的连接与供电。

::: info
请注意飞行控制器无法直接为电机和舵机供电（仅GPS模块、遥控接收机和低功耗遥测模块可通过Pixhawk供电）。
通常使用配电板(PDB)为电机供电，通过独立（或集成式）电池消除电路(BEC)为每个舵机供电。
:::

## 配置

直升机配置和设置详见：

- [直升机配置](../config_heli/index.md)：机身选择、执行器配置与测试、调参。
- [标准配置](../config/index.md)：大多数机身的通用配置和校准步骤。
  包括指南针和陀螺仪等核心组件的配置/校准、遥控器飞行模式映射设置和安全设置。

## 机身构建

暂无可用内容。