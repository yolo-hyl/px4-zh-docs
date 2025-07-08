# 电源模块与电源分配板

_电源模块_ 为飞控（FC）提供稳压电源，并提供电池电压和电流信息。电压/电流信息用于计算消耗的功率，从而估算剩余电池容量。这使得飞控能够在电量不足时提供安全警告和其他动作：[安全 > 低电量安全机制](../config/safety.md#battery-level-failsafe)

_电源分配板（PDB）_ 是用于简化将电池输出分配到无人机各部分（特别是电机）的电路板。PDB有时会集成飞控电源模块、电机电调（ESC）以及舵机供电的电池消除电路（BEC）。

PX4 电池/电源模块的配置（通过 ADC 接口）详见：[电池估算调校](../config/battery.md)

::: tip
为简化组装，请使用飞控制造商推荐的电源模块或 PDB，并根据您的功率需求选择合适规格。

Pixhawk 接口标准要求 VCC 线必须提供至少 2.5A 持续电流，且默认电压为 5.3V。
实践中不同飞控可能有不同建议或要求，因此如果您未使用（或无法使用）推荐模块，请确认模块符合飞控的规格要求。
:::

本节提供多款电源模块和电源分配板的信息（详见飞控制造商文档获取更多选项）：

- 模拟电压和电流电源模块：
  - [CUAV HV PM](../power_module/cuav_hv_pm.md)
  - [Holybro PM02](../power_module/holybro_pm02.md)
  - [Holybro PM07](../power_module/holybro_pm07_pixhawk4_power_module.md)
  - [Holybro PM06 V2](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
  - [Sky-Drones SmartAP PDB](../power_module/sky-drones_smartap-pdb.md)
- 数字（I2C）电压和电流电源模块（适用于 Pixhawk FMUv6X 和 FMUv5X 系列控制器）：
  - [ARK PAB 电源模块](../power_module/ark_pab_power_module.md)
  - [ARK 12S PAB 电源模块](../power_module/ark_12s_pab_power_module.md)
  - [Holybro PM02D](../power_module/holybro_pm02d.md)
  - [Holybro PM03D](../power_module/holybro_pm03d.md)
- [DroneCAN](../dronecan/index.md) 电源模块
  - [CUAV CAN PMU](../dronecan/cuav_can_pmu.md)
  - [Pomegranate Systems 电源模块](../dronecan/pomegranate_systems_pm.md)
  - [RaccoonLab 电源连接器和 PMU](../dronecan/raccoonlab_power.md)