# PX4 参考飞行控制器设计

PX4 参考设计是 [Pixhawk系列](../flight_controller/pixhawk_series.md) 飞行控制器。该设计于2011年首次发布，目前已进入第五代 [参考设计](#reference_design_generations)（第六代板卡设计正在进行中）。

## 二进制兼容性

同一设计制造的板卡应具有二进制兼容性（即可以运行相同固件）。从2018年起我们将提供二进制兼容性测试套件，用于验证和认证这种兼容性。

FMU第1-3代是开放硬件设计，而FMU第4和第5代仅提供引脚定义和电源规格（原理图由各制造商自行创建）。为更好地确保兼容性，FMUv6及后续版本将回归完整的参考设计模型。

<a id="reference_design_generations"></a>

## 参考设计版本

- FMUv1: 开发板 (STM32F407, 128 KB RAM, 1MB flash, [原理图](https://github.com/PX4/Hardware/tree/master/FMUv1))（已不再被PX4支持）
- FMUv2: Pixhawk (STM32F427, 168 MHz, 192 KB RAM, 1MB flash, [原理图](https://github.com/PX4/Hardware/tree/master/FMUv2))
- FMUv3: 带2MB flash的Pixhawk变种 (3DR Pixhawk 2 (Solo), Hex Pixhawk 2.1, Holybro Pixfalcon, 3DR Pixhawk Mini, STM32F427, 168 MHz, 256 KB RAM, 2 MB flash, [原理图](https://github.com/PX4/Hardware/tree/master/FMUv3_REV_D))
- FMUv4: Pixracer (STM32F427, 168 MHz, 256 KB RAM, 2 MB flash, [引脚定义](https://docs.google.com/spreadsheets/d/1raRRouNsveQz8cj-EneWG6iW0dqGfRAifI91I2Sr5E0/edit#gid=1585075739))
- FMUv4 PRO: Drotek Pixhawk 3 PRO (STM32F469, 180 MHz, 384 KB RAM, 2 MB flash, [引脚定义](https://docs.google.com/spreadsheets/d/1raRRouNsveQz8cj-EneWG6iW0dqGfRAifI91I2Sr5E0/edit#gid=1585075739))
- FMUv5: Holybro Pixhawk 4 (STM32F765, 216 MHz, 512 KB RAM, 2 MB flash, [引脚定义](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165))
- FMUv5X: (多款产品) (STM32F765, 400 MHz, 512KB RAM, 2 MB flash) ([标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-011%20Pixhawk%20Autopilot%20v5X%20Standard.pdf))
- FMUv6X: (多款产品) (STM32H753, 480 MHz, 1 MB RAM, 2 MB flash) 及变种6i (i.MX RT1050, 600 MHz, 512 KB RAM, 外部flash) ([标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf))
- FMUv6C: (多款产品) (STM32H743V, 480 MHz, 1 MB RAM, 2 MB flash) ([标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-018%20Pixhawk%20Autopilot%20v6C%20Standard.pdf))
- FMUv6U: (多款产品) (STM32H753, 400 MHz, 1 MB RAM, 2 MB flash) ([标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-016%20Pixhawk%20Autopilot%20v6U%20Standard.pdf))
- FMUv6X-RT: (多款产品) (NXP i.MX RT1176, 32 Bit Arm® Cortex®-M7, 1GHz 32 Bit Arm® Cortex®-M4, 400MHz 次级核心, 2 MB RAM, 64 MB flash) 及变种6i (i.MX RT1050, 600 MHz, 512 KB RAM, 外部flash) ([标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-020%20Pixhawk%20Autopilot%20v6X-RT%20Standard.pdf))

从FMUv5X开始，所有新标准在GitHub的[Pixhawk/Pixhawk-Standards](https://github.com/pixhawk/Pixhawk-Standards)中发布。详见[Pixhawk.org](https://pixhawk.org)获取更多信息。

## 主板/IO功能分解

下图展示了Pixhawk系列飞行控制器中FMU与IO板之间的总线和功能职责划分（这些板卡集成在单一物理模块中）。

![PX4 主板/IO 功能分解](../../assets/diagrams/px4_fmu_io_functions.png)

<!-- 可在此处找到Draw.io版本文件: https://drive.google.com/file/d/1H0nK7Ufo979BE9EBjJ_ccVX0dqGfRAifI91I2Sr5E0/edit#gid=1585075739 -->

部分Pixhawk型号会省略IO板设计，具体取决于空间需求。例如：

- Pixhawk Mini/2 Mini/3 Pro/4 Pro 省略IO板
- Pixhawk 2/3/4 保留IO板

::: info
制造商可通过缩短IO板长度来节省空间，但需确保FMU与IO板之间的连接器兼容性。
:::

## 注意事项

- 当构建目标仅能在无IO板的飞控上运行时，FMU输出将映射到"MAIN"（例如：Pixhawk Mini）
- 使用带IO板的飞控时，主输出将映射到"MAIN"，辅助输出映射到"IO"
- PWM信号始终通过FMU生成，通过IO板时采用缓冲放大处理
- 不同版本的IO板可能具有不同功能（如支持CAN总线或安全开关）