# 操纵杆设置

通过 _QGroundControl_ 连接的[计算机操纵杆](https://en.wikipedia.org/wiki/Joystick)或游戏手柄可以用于手动控制机体（作为 [RC发射机](../config/radio.md) 的替代方案）。

此方法可被集成地面控制站的手动控制单元使用（如下方展示的 _UAVComponents_ [MicroNav](https://uxvtechnologies.com/ground-control-stations/micronav/) ）。
操纵杆也常被开发者用于在仿真环境中飞行机体。

![Joystick MicroNav](../../assets/peripherals/joystick/micronav.jpg)

:::tip
如果仅使用操纵杆，[Radio Setup](../config/radio.md) 是不需要的（因为操纵杆不是RC控制器）！
:::

::: info
_QGroundControl_ 使用跨平台 [SDL2](http://www.libsdl.org/index.php) 库将操纵杆运动转换为 MAVLink [MANUAL_CONTROL](https://mavlink.io/en/messages/common.html#MANUAL_CONTROL) 消息，然后通过遥测通道发送到 PX4。
因此，基于操纵杆的控制系统需要可靠的高带宽遥测通道，以确保机体对操纵杆运动的响应性。
:::

## 启用 PX4 操纵杆支持

关于如何设置操纵杆的信息详见：[QGroundControl > 操纵杆设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/joystick.html)。

总结步骤如下：

- 打开 _QGroundControl_
- 设置参数 [COM_RC_IN_MODE=1](../advanced_config/parameter_reference.md#COM_RC_IN_MODE) - `操纵杆`
  - 设置参数的更多信息请见 [Parameters](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/parameters.html)
  - 将参数设置为 `2` 或 `3` 在某些情况下也会启用操纵杆
- 连接操纵杆
- 在 **Vehicle Setup > Joystick** 中配置连接的操纵杆
  -