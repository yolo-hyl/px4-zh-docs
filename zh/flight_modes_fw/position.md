# 位置模式（固定翼）

<img src="../../assets/site/difficulty_easy.png" title="Easy to fly" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="Manual/Remote control required" width="30px" />&nbsp;<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

位置模式是操作最简单且最安全的手动模式。
该模式适用于具有位置估算（例如GPS）的机体。
它使飞行员更容易控制机体高度，特别是达到并保持固定高度。
该模式将保持机体航向对抗风力。
如果安装了空速传感器，空速将被主动控制。

当滚转杆非零时，机体执行[协调转弯](https://en.wikipedia.org/wiki/Coordinated_flight)，俯仰杆控制上升/下降率。
油门杆决定空速——在50%油门时，飞机将以预设巡航速度保持当前高度。

当所有杆位释放/居中（无滚转、俯仰、偏航，且约50%油门）时，飞机将恢复到直线平飞状态，并保持当前高度和航路，不受风力影响。
这使得在飞行中出现问题时更容易恢复。滚转和俯仰为角度控制（因此不可能翻滚或盘旋机体）。

偏航杆可用于增加/减少机体转弯时的偏航率。
若保持居中，控制器会自行进行转弯协调（即根据当前滚转角自动应用所需的偏航率以实现平滑转弯）。
下图以[模式2发射机](../getting_started/rc_transmitter_receiver.md#transmitter_modes)为例直观展示了该模式行为。

![FW 位置模式](../../assets/flight_modes/position_fw.png)

## 技术描述

位置模式类似于[稳定模式](../flight_modes_fw/altitude.md)，但加入了航向稳定功能。
如果存在空速传感器，空速也会被稳定。

- 居中的滚转/俯仰/偏航输入（在死区范围内）：
  - 自动驾驶仪使机体水平并保持高度、空速和地面航向。
- 非居中输入：
  - 俯仰杆控制高度。
  - 油门杆在连接空速传感器时控制飞机空速。无空速传感器时，机体将保持在平衡油门（[FW_THR_TRIM](../advanced_config/parameter_reference.md#FW_THR_TRIM)）下平飞，根据需要增减油门以爬升或下降。
  - 滚转杆控制滚转角。自动驾驶仪将保持[协调飞行](https://en.wikipedia.org/wiki/Coordinated_flight)。
  - 偏航杆增加额外的偏航率设定值（叠加在自动驾驶仪为保持协调飞行计算的偏航率上）。
    可用于手动调整机体侧滑。
- 需要手动控制输入（如遥控器、操纵杆）。
- 需要高度测量源（通常为气压计或GPS）

## 参数

该模式受以下参数影响：

| 参数                                                                                                   | 描述                                                          |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| <a id="FW_AIRSPD_MIN"></a>[FW_AIRSPD_MIN](../advanced_config/parameter_reference.md#FW_AIRSPD_MIN)          | 最小空速。默认值：10 m/s。                                       |
| <a id="FW_AIRSPD_MAX"></a>[FW_AIRSPD_MAX](../advanced_config/parameter_reference.md#FW_AIRSPD_MAX)          | 最大空速。默认值：20 m/s。                                       |
| <a id="FW_AIRSPD_TRIM"></a>[FW_AIRSPD_TRIM](../advanced_config/parameter_reference.md#FW_AIRSPD_TRIM)       | 巡航速度。默认值：15 m/s。                                       |
| <a id="FW_MAN_P_MAX"></a>[FW_MAN_P_MAX](../advanced_config/parameter_reference.md#FW_MAN_P_MAX)             | 态度稳定模式下的最大俯仰设定值。默认值：45 度。                 |
| <a id="FW_MAN_R_MAX"></a>[FW_MAN_R_MAX](../advanced_config/parameter_reference.md#FW_MAN_R_MAX)             | 态度稳定模式下的最大滚转设定值。默认值：45 度。                 |
| <a id="FW_T_CLMB_R_SP"></a>[FW_T_CLMB_R_SP](../advanced_config/parameter_reference.md#FW_T_CLMB_R_SP)       | 最大爬升率设定值。默认值：3 m/s。                               |
| <a id="FW_T_SINK_R_SP"></a>[FW_T_SINK_R_SP](../advanced_config/parameter_reference.md#FW_T_SINK_R_SP)       | 最大下降率设定值。默认值：2 m/s。                               |
| <a id="FW_PN_R_SLEW_MAX"></a>[FW_PN_R_SLEW_MAX](../advanced_config/parameter_reference.md#FW_PN_R_SLEW_MAX) | 滚转设定值变化率限制。默认值：90 °/s。                          |