

# PX4 参考飞行控制器设计

PX4 参考设计是[Pixhawk系列](../flight_controller/pixhawk_series.md)飞行控制器。该设计自2011年首次发布后，目前已进入第五[代](#reference_design_generations)（第六代板级设计正在进行中）。

## 二进制兼容性

所有基于特定设计制造的开发板都应具备二进制兼容性（即可以运行相同固件）。从2018年起我们将提供二进制兼容性测试套件，用于验证和认证这种兼容性。

FMU第1至3代采用开源硬件设计，而FMU第4和第5代仅提供引脚分配和电源供应规范（电路图由各制造商自行设计）。为更好地确保兼容性，FMUv6及后续版本将回归完整的参考设计模型。

<a id="reference_design_generations"></a>

## 参考设计版本

- FMUv1: 开发板（STM32F407，128 KB RAM，1MB flash，[电路图](https://github.com/PX4/Hardware/tree/master/FMUv1)）（PX4已停止支持）
- FMUv2: Pixhawk（STM32F427，168 MHz，192 KB RAM，1MB flash，[电路图](https://github.com/PX4/Hardware/tree/master/FMUv2)）
- FMUv3: 带2MB flash的Pixhawk变种（3DR Pixhawk 2（Solo），Hex Pixhawk 2.1，Holybro Pixfalcon，3DR Pixhawk Mini，STM32F427，168 MHz，256 KB RAM，2 MB flash，[电路图](https://github.com/PX4/Hardware/tree/master/FMUv3_REV_D)）
- FMUv4: Pixracer（STM32F427，168 MHz，256 KB RAM，2 MB flash，[引脚分配](https://docs.google.com/spreadsheets/d/1raRRouNsveQz8cj-EneWG6iW0dqGfRAifI91I2Sr5E0/edit#gid=1585075739)）
- FMUv4 PRO: Drotek Pixhawk 3 PRO（STM32F469，180 MHz，384 KB RAM，2 MB flash，[引脚分配](https://docs.google.com/spreadsheets/d/1raRRouNsveQz8cj-EneWG6iW0dqGfRAifI91I2Sr5E0/edit#gid=1585075739)）
- FMUv5: Holybro Pixhawk 4（STM32F765，216 MHz，512 KB RAM，2 MB flash，[引脚分配](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165)）
- FMUv5X: （多款产品）（STM32F765，400 MHz，512KB RAM，2 MB flash）（[标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-011%20Pixhawk%20Autopilot%20v5X%20Standard.pdf)）
- FMUv6X: （多款产品）（STM32H753，480 MHz，1 MB RAM，2 MB flash）及变种6i（i.MX RT1050，600 MHz，512 KB RAM，外部flash）（[标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf)）
- FMUv6C: （多款产品）（STM32H743V，480 MHz，1 MB RAM，2 MB flash）（[标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-018%20Pixhawk%20Autopilot%20v6C%20Standard.pdf)）
- FMUv6U: （多款产品）（STM32H753，400 MHz，1 MB RAM，2 MB flash）（[标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-016%20Pixhawk%20Autopilot%20v6U%20Standard.pdf)）
- FMUv6X-RT: （多款产品）（NXP i.MX RT1176，32位Arm® Cortex®-M7，1GHz 32位Arm® Cortex®-M4，400MHz二级核心，2 MB RAM，64 MB flash）及变种6i（i.MX RT1050，600 MHz，512 KB RAM，外部flash）（[标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-020%20Pixhawk%20Autopilot%20v6X-RT%20Standard.pdf)）

从FMUv5X开始，所有新标准均发布在GitHub的[Pixhawk/Pixhawk-Standards](https://github.com/pixhawk/Pixhawk-Standards)仓库中。详见[Pixhawk.org](https://pixhawk.org)获取更多信息。

## 主/IO 功能分解

下图显示了Pixhawk系列飞控中FMU与IO板之间的总线和功能职责划分（这些板集成在单一物理模块中）。

![PX4 主/IO 功能分解](../../assets/diagrams/px4_fmu_io_functions.png)

<!-- 可在以下地址找到Draw.io版本文件: https://drive.google.com/file/d/1H0nK7Ufo979BE9EBjJ_ccVx3fcsilPS3/view?usp=sharing -->

部分Pixhawk系列控制器省略IO板以减少空间占用或复杂度，或针对特定使用场景进行优化。在这种情况下IO驱动将不会启动。

::: info
厂商生产的无IO板飞控变体通常以包含IO板的版本名称的"缩略形式"命名：例如 _Pixhawk 4_ **Mini**_, \_CUAV v5 **nano**_。
:::

必须在带IO板的飞控上运行的构建目标会将FMU输出映射到 `AUX`，IO输出映射到 `MAIN`（见上图）。若在无IO板或IO板被禁用的硬件上运行目标，PWM MAIN输出将不可用。例如在[Pixhawk 4](../flight_controller/pixhawk4.md)（含IO）和[Pixhawk 4 Mini](../flight_controller/pixhawk4_mini.md)（无IO）上运行 `px4_fmu-v5_default` 时会观察到这种差异。

:::warning
在[Pixhawk 4 Mini](../flight_controller/pixhawk4_mini.md)上，这会导致飞控上丝印的 `MAIN` 标签与[执行器配置](../config/actuators.md)中显示的 `AUX` 总线产生不匹配。
::: info 如果构建目标仅用于无IO板的飞控，则FMU输出会映射到 `MAIN`（例如，[Pixracer](../flight_controller/pixracer.md) 上的 `px4_fmu-v4_default` 目标）。

PX4 PWM输出在[执行器配置](../config/actuators.md)中被映射到 `MAIN` 或 `AUX` 端口。