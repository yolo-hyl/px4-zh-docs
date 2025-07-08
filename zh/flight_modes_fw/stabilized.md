# 姿态稳定模式(固定翼)

<img src="../../assets/site/difficulty_medium.png" title="飞行难度中等" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="需要手动/遥控操作" width="30px" />

_姿态稳定模式_ 是一种手动模式，中立位置的摇杆会使机体姿态(滚转和俯仰)保持水平并维持水平姿态。

::: info
_姿态稳定模式_ 与 [高度模式](../flight_modes_fw/altitude.md) 类似，松开摇杆会使机体保持水平，但与高度模式不同的是它不会维持高度或航向。
相比 [手动模式](../flight_modes_fw/manual.md) 更容易飞行，因为无法滚转或翻转机体，且需要时可通过中立摇杆轻松保持水平姿态。
:::

机体的爬升/下降由俯仰和油门输入控制，当滚转摇杆非中立时会执行[协调转弯](https://en.wikipedia.org/wiki/Coordinated_flight)。
滚转和俯仰为角度控制(无法倒飞或做筋斗)。

当油门降至0%时(电机停止)机体将滑翔。
要执行转弯必须在整个机动过程中保持指令，因为松开滚转摇杆飞机将停止转弯并自动保持水平(俯仰指令也是如此)。

偏航摇杆可用来增加/减少转弯时的偏航率。
若保持中立，控制器会自行完成转弯协调，即根据当前滚转角度自动应用所需的偏航率以实现平滑转弯。

下图展示了该模式的飞行特性(适用于 [模式2发射机](../getting_started/rc_transmitter_receiver.md#transmitter_modes))。

![FW Manual Flight](../../assets/flight_modes/stabilized_fw.png)

## 技术描述

一种手动模式，中立位置的滚转/俯仰摇杆使机体保持水平飞行。
机体航向和高度不会被维持，可能会因风力产生漂移。

- 滚转/俯仰/偏航摇杆中立(在死区范围内)时，机体进入直线水平飞行。
  机体航向和高度不会被维持，可能会因风力产生漂移。
- 滚转摇杆控制滚转角度。
  自动驾驶仪将维持<a href="https://en.wikipedia.org/wiki/Coordinated_flight">协调飞行</a>。
- 俯仰摇杆控制围绕[FW_PSP_OFF](../advanced_config/parameter_reference.md#FW_PSP_OFF)定义偏移的俯仰角度
- 油门摇杆直接控制油门
- 偏航摇杆增加额外的偏航率设定值(叠加于自动驾驶仪计算的偏航率以维持协调飞行)
  可用于手动调整机体的侧滑量
- 需要手动控制输入(如遥控器控制、操纵杆)

## 参数

该模式受以下参数影响：

| 参数                                                                                          | 描述                                                                                   |
| -------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| <a id="FW_MAN_P_MAX"></a>[FW_MAN_P_MAX](../advanced_config/parameter_reference.md#FW_MAN_P_MAX)    | 姿态稳定模式下手动控制的最大俯仰角。默认值：45度。                                       |
| <a id="FW_MAN_R_MAX"></a>[FW_MAN_R_MAX](../advanced_config/parameter_reference.md#FW_MAN_R_MAX)    | 姿态稳定模式下手动控制的最大滚转角。默认值：45度。                                       |
| <a id="FW_MAN_YR_MAX"></a>[FW_MAN_YR_MAX](../advanced_config/parameter_reference.md#FW_MAN_YR_MAX) | 手动增加的最大偏航率。默认值：每秒30度。                                               |
| <a id="FW_PSP_OFF"></a>[FW_PSP_OFF](../advanced_config/parameter_reference.md#FW_PSP_OFF)          | 俯仰设定值偏移(水平飞行时的俯仰角)。默认值：0度。                                       |

<!-- this document needs to be extended -->