# Snapdragon Flight Autopilot (已停产)

<Badge type="info" text="已停产" />

:::warning
Snapdragon Flight Autopilot 已被[停产](../flight_controller/autopilot_experimental.md)且不再商业销售。
关于其使用方法的说明请参见 [PX4 User Guide v1.11](https://docs.px4.io/v1.11/en/flight_controller/snapdragon_flight.html)

PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题请联系[制造商](https://www.intrinsyc.com/)。
:::

Qualcomm Snapdragon Flight 平台是一款高端自动驾驶仪/机载计算机，通过 [DSPAL API](https://github.com/ATLFlight/dspal) 在 QuRT 实时操作系统上运行 PX4 飞行栈，以实现 POSIX 兼容性。
与 [Pixhawk](../flight_controller/pixhawk.md) 相比，它增加了摄像头和 WiFi 功能，具备更强大的处理能力，并采用不同的 I/O 接口。

![Snapdragon Hero Doc](../../assets/hardware/snapdragon/hardware-snapdragon.jpg)