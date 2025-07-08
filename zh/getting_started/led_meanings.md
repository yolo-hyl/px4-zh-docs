# LED含义（Pixhawk系列）

[Pixhawk系列飞控](../flight_controller/pixhawk_series.md)使用LED指示机体当前状态。
- [UI LED](#ui_led) 提供与*飞行就绪*相关的用户状态信息。
- [状态LED](#status_led) 提供PX4IO和FMU SoC的状态。
  它们指示电源、引导加载模式、活动状态和错误。

<a id="ui_led"></a>

## UI LED

RGB *UI LED* 指示机体当前的*飞行就绪*状态。
这通常是连接在飞控板上的超亮I2C外设（即FMUv4没有板载LED，通常使用GPS上的LED）。

下图显示了LED与机体状态之间的关系。

:::warning
即使GPS锁定（绿色LED），机体仍可能无法解锁，因为PX4尚未通过[飞行前检查](../flying/pre_flight_checks.md)。**起飞需要有效的全局位置估计！**
:::

:::tip
遇到错误（红色闪烁），或无法获取GPS锁定（从蓝色变为绿色）时，请在*QGroundControl*中检查更详细的状态信息，包括校准状态和[Preflight Checks (Internal)](../flying/pre_flight_checks.md)报告的错误信息。
同时检查GPS模块是否正确连接，Pixhawk是否正常读取GPS，以及GPS是否发送了有效位置。
:::

![LED含义](../../assets/flight_controller/pixhawk_led_meanings.gif)


* **[常亮蓝色] 已解锁，无GPS锁定**：指示机体已解锁但未获取GPS定位。
机体解锁后，PX4将解除电机控制，允许你飞行无人机。
如常，解锁时需谨慎，因为高转速螺旋桨可能危险。
此模式下机体无法执行引导任务。

* **[闪烁蓝色] 未解锁，无GPS锁定**：与上述类似，但机体未解锁。
这意味着你无法控制电机，但其他所有子系统正常运行。

* **[常亮绿色] 已解锁，GPS锁定**：指示机体已解锁并获取有效GPS定位。
机体解锁后，PX4将解除电机控制，允许你飞行无人机。
如常，解锁时需谨慎，因为高转速螺旋ie可能危险。
此模式下机体可执行引导任务。

* **[闪烁绿色] 未解锁，GPS锁定**：与上述类似，但机体未解锁。
这意味着你无法控制电机，但包括GPS定位在内的所有其他子系统正常运行。

* **[常亮紫色] 失效保护模式**：当机体飞行中遇到问题时（如失去手动控制、电池电量过低或内部错误），此模式将激活。
失效保护模式下，机体将尝试返回起飞位置，或直接在当前位置降落。

* **[常亮琥珀色] 低电量警告**：指示机体电池电量危险性不足。
达到一定程度后，机体将进入失效保护模式。然而此模式应提示你注意，是时候结束此次飞行了。

* **[红色闪烁] 错误/需配置**：指示自动驾驶仪在飞行前需要配置或校准。
将自动驾驶仪连接到地面控制站以确认具体问题。
若已完成配置过程但自动驾驶仪仍显示红色闪烁，可能存在其他错误。

<a id="status_led"></a>

## 状态LED

三颗*状态LED*提供FMU SoC的状态，另外三颗提供PX4IO（如果存在）的状态。
它们指示电源、引导加载模式、活动状态和错误。

![Pixhawk 4](../../assets/flight_controller/pixhawk4/pixhawk4_status_leds.jpg)

上电后，FMU和PX4IO CPU首先运行引导加载程序（BL），然后运行应用（APP）。
下表显示了引导加载程序和APP如何使用LED指示状态。

颜色 | 标签 | 引导加载程序使用 | 应用使用 
--- | --- | --- | ---
蓝色 | ACT（活动） | 接收数据时闪烁 | 解锁状态指示
红色/琥珀色 | B/E（引导加载/错误） | 处于引导加载程序时闪烁 | 错误指示
绿色 |PWR（电源） | 引导加载程序不使用 | 解锁状态指示

::: info
上述LED标签通常使用，但某些板载可能不同。
:::

LED的详细解释如下（"x"表示"任何状态"）：

红色/琥珀色 | 蓝色 |  绿色 | 含义
--- | --- | --- | ---
10Hz | x | x | CPU负载>80%或内存使用>98%
关闭 | x | x | CPU负载≤80%或内存使用≤98%
无 | 关闭 | 4 Hz| actuator_armed->armed && failsafe 
无 | 开启 | 4 Hz | actuator_armed->armed && !failsafe
无 | 关闭 |1 Hz | !actuator_armed-> armed && actuator_armed->ready_to_arm
无 | 关闭 |10 Hz | !actuator_armed->armed  && !actuator_armed->ready_to_arm