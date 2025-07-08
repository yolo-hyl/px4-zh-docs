# 高度模式（多旋翼）

<img src="../../assets/site/difficulty_easy.png" title="Easy to fly" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="Manual/Remote control required" width="30px" />&nbsp;<img src="../../assets/site/altitude_icon.svg" title="Altitude required (e.g. Baro, Rangefinder)" width="30px" />

_高度模式_ 是一种 _相对_ 易于飞行的遥控模式，横滚和俯仰摇杆控制机体在左右和前后方向（相对于机体"前方"）的移动，偏航摇杆控制水平面的旋转速率，油门控制上升/下降的速度。

当摇杆松开/居中时，机体将保持水平并维持当前 _高度_。
在水平面移动时，机体将持续移动直至动量被风阻消耗。
如果风力吹动机体，机体将随风漂移。

:::tip
_高度模式_ 是新飞行员最安全的非GPS手动模式。它与[稳定模式](../flight_modes_mc/manual_stabilized.md)类似，但额外在摇杆居中时锁定机体高度。
:::

下图展示了该模式的行为（针对[模式2遥控器](../getting_started/rc_transmitter_receiver.md#transmitter_modes)）。

![高度控制 MC - 模式2遥控器](../../assets/flight_modes/altitude_mc.png)

## 技术总结

与[稳定模式](../flight_modes_mc/manual_stabilized.md)类似的遥控/手动模式，但增加了 _高度稳定_（居中摇杆使机体水平并保持固定高度）。由于风力（或原有动量）机体水平位置可能发生移动。

- 居中摇杆（在死区范围内）：
  - 横滚/俯仰摇杆使机体水平。
  - 油门（约50%）在风阻下保持当前高度稳定。
- 非居中状态：
  - 横滚/俯仰摇杆控制相应方向的倾斜角，产生对应的左右和前后移动。
  - 油门摇杆控制上下速度（预设最大速率），并影响其他轴的移动速度。
  - 偏航摇杆控制水平面以上的角速度旋转速率。
- 起飞：
  - 当机体着陆时，若油门摇杆超过62.5%（从底部满量程计算），机体将起飞。
- 高度通常通过气压计测量，在极端天气条件下可能不准确。配备激光雷达/测距传感器的机体可实现更可靠和精确的高度假。
- 需要手动控制输入（如遥控器控制、操纵杆）：
  - 横滚、俯仰：飞控协助稳定姿态。遥控杆位置映射到机体姿态。
  - 油门：飞控协助抵御风力保持位置。
  - 偏航：飞控协助稳定姿态速率。遥控杆位置映射到该方向的旋转速率。

## 参数

该模式受以下参数影响：

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                                                                         |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="MPC_Z_VEL_MAX_UP"></a>[MPC_Z_VEL_MAX_UP](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_UP) | 最大垂直上升速度。默认值：3 m/s。                                                                                                                                                                                                                                                    |
| <a id="MPC_Z_VEL_MAX_DN"></a>[MPC_Z_VEL_MAX_DN](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_DN) | 最大垂直下降速度。默认值：1 m/s。                                                                                                                                                                                                                                                    |
| <a id="RCX_DZ"></a>`RCX_DZ`                                                                             | 通道X的遥控器死区。油门通道的X值取决于[RC_MAP_THROTTLE](../advanced_config/parameter_reference.md#RC_MAP_THROTTLE)的设置。例如，若油门是通道4，则[RC4_DZ](../advanced_config/parameter_reference.md#RC4_DZ)指定死区。 |
| <a id="MPC_xxx"></a>`MPC_XXXX`                                                                          | 大多数MPC_xxx参数会影响此模式的飞行行为（至少到某种程度）。例如，[MPC_THR_HOVER](../advanced_config/parameter_reference.md#MPC_THR_HOVER)定义了机体悬停所需推力。                                                                                                                       |