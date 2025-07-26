# 电调与电机

许多 PX4 无人机使用通过电子调速器（电调）驱动飞控的无刷电机。电调接收飞控的信号，并利用该信号控制输出到电机的功率水平。

PX4 支持多种常见协议向电调发送信号：[PWM 电调](../peripherals/pwm_escs_and_servo.md)、[OneShot 电调](../peripherals/oneshot.md)、[DShot 电调](../peripherals/dshot.md)、[DroneCAN 电调](../dronecan/escs.md)、PCA9685 电调（通过 I2C）以及一些 UART 电调（来自 Yuneec）。

欲了解更多信息，请参见：

- [PWM 电调和舵机](../peripherals/pwm_escs_and_servo.md)
- [OneShot 电调和舵机](../peripherals/oneshot.md)
- [DShot](../peripherals/dshot.md)
- [DroneCAN 电调](../dronecan/escs.md)
- [电调校准](../advanced_config/esc_calibration.md)
- [电调固件与协议概览](https://oscarliang.com/esc-firmware-protocols/) (oscarliang.com)

下文简要介绍 PX4 支持的主要电调/舵机协议。

## 电调协议

### PWM

[PWM 电调](../peripherals/pwm_escs_and_servo.md) 常用于固定翼机体和地面车辆（需要低延迟的多旋翼机体通常使用 OneShot 或 DShot 电调）。

PWM 电调通过周期性脉冲进行通信，其中脉冲的 _宽度_ 表示目标功率水平。脉冲宽度通常在 1000uS（零功率）到 2000uS（全功率）之间。信号的周期帧率取决于电调的性能，通常在 50Hz 到 490Hz 之间（理论最大值为 500Hz，前提是“关闭”周期极短）。较高频率对电调更优，尤其在需要对设定值变化快速响应时。PWM 舵机通常 50Hz 即可满足需求，许多不支持更高频率。

![PWM 占空比](../../assets/peripherals/esc_pwm_duty_cycle.png)

除了相对较低的协议速度外，PWM 电调还需要 [校准](../advanced_config/esc_calibration.md)，因为表示低高值的范围可能差异较大。与 [DShot](#DShot) 和 [DroneCAN 电调](#DroneCAN) 不同，它们无法提供电调（或舵机）状态的遥测和反馈。

配置：

- [电调接线](../peripherals/pwm_escs_and_servo.md)
- [PX4 配置](../peripherals/pwm_escs_and_servo.md#px4-configuration)
- [电调校准](../advanced_config/esc_calibration.md)

### OneShot 125

[OneShot 125 电调](../peripherals/oneshot.md) 通常比 PWM 电调快得多，因此响应更灵敏且更易于调参。它们比 PWM 更适合多旋翼机体（但不如 [DShot 电调](#DShot) 优越，后者无需校准且可能提供遥测反馈）。OneShot 协议有多种变体，支持不同的频率。PX4 仅支持 OneShot 125。

OneShot 125 与 PWM 相同，但脉冲宽度缩短为原来的 1/8（从 125us 到 250us 表示零功率到全功率）。这使得 OneShot 125 电调具有更短的占空比/更高频率。PWM 理论最大值接近 500Hz，而 OneShot 可达 4kHz。实际支持频率取决于电调型号。

配置：

- [电调接线](../peripherals/pwm_escs_and_servo.md)（与 PWM 电调相同）
- [PX4 配置](../peripherals/oneshot.md#px4-configuration)
- [电调校准](../advanced_config/esc_calibration.md)

### DShot

[DShot](../peripherals/dshot.md) 是一种数字电调协议，特别推荐用于需要降低延迟的机体，例如竞速多旋翼、垂直起降（VTOL）等。

其延迟低于 [PWM](#PWM) 和 [OneShot](#OneShot 125)，且更稳定。此外，它无需电调校准，部分电调可提供遥测反馈，还可反转电机旋转方向。

PX4 配置在 [执行器配置](../config/actuators.md) 中完成。通过 UI 选择更高频率的 DShot 电调可降低延迟，但低频率更稳定（更适合长线缆的大机体）；部分电调仅支持低频率（详见数据手册）。

配置：

- [电调接线](../peripherals/pwm_escs_and_servo.md)（与 PWM 电调相同）
- [DShot](../peripherals/dshot.md) 还包含发送指令等信息。

### DroneCAN

[DroneCAN 电调](../dronecan/escs.md) 推荐用于以 DroneCAN 为主总线的机体。PX4 实现目前限制更新频率为 200Hz。

DroneCAN 与 [DShot](#DShot) 具有相似优势，包括高数据率、长线缆稳定连接、遥测反馈以及无需电调校准。

[DroneCAN 电调](../dronecan/escs.md) 通过 DroneCAN 总线连接（设置和配置详见该链接）。