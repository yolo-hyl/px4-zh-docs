# Pixhawk 自主飞行器总线与载板

[Pixhawk 自主飞行器总线 (PAB) 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf) 提供了标准化的接口设计，允许任何符合标准的 Pixhawk 飞控可以与任何符合标准的底板实现"即插即用"功能。

这种模块化设计使飞控更容易集成到不同的系统模块设计方案中。
例如，PAB 意味着您可以使用相同的飞控硬件在输出更少的紧凑型载板上，或者在集成伴飞计算机的载板上使用，等等。

以下载板和飞控符合 PAB 标准，因此可以互换使用。

::: info
标准中的"机械设计"部分提供了不同厂商间机械兼容性的具体建议。
此处列出的飞控和底座预计符合所有建议。
:::

## PAB 兼容载板

- [ARK Electronics Pixhawk 自主飞行器总线载板](../flight_controller/ark_pab.md)
- [Holybro Pixhawk 标准底板](https://holybro.com/products/pixhawk-baseboards)
- [Holybro Pixhawk Mini 底板](https://holybro.com/products/pixhawk-baseboards)
- [Holybro Pixhawk RPi CM4 底板](../companion_computer/holybro_pixhawk_rpi_cm4_baseboard.md) (集成伴飞/飞控主板)

## PAB 兼容飞控

- [ARK Electronics ARKV6X](../flight_controller/ark_v6x.md)
- [Holybro Pixhawk 5X (FMUv5X)](../flight_controller/pixhawk5x.md)
- [Holybro Pixhawk 6X (FMUv6X)](../flight_controller/pixhawk6x.md)
- [CUAV Pixhawk V6X (FMUv6X)](../flight_controller/cuav_pixhawk_v6x.md)