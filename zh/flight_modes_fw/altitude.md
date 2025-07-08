# 高度模式（固定翼）

<img src="../../assets/site/difficulty_easy.png" title="Easy to fly" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="Manual/Remote control required" width="30px" />&nbsp;<img src="../../assets/site/altitude_icon.svg" title="Altitude required (e.g. Baro, Rangefinder)" width="30px" />

_高度_飞行模式是除GPS外最安全且最容易手动操作的模式。  
该模式使飞行员更容易控制机体高度，特别是实现并保持固定高度。  
该模式不会尝试对抗风力保持机体航向。  
如果安装了空速传感器，空速将被主动控制。

:::tip
_高度模式_与[位置模式](../flight_modes_fw/position.md)类似，两者在松开操纵杆时都会使机体保持水平并维持高度。  
区别在于位置模式会对抗风力保持实际飞行路径（航向），而高度模式仅维持航向。
:::

当滚转操纵杆不为零时，机体执行[协调转弯](https://en.wikipedia.org/wiki/Coordinated_flight)，俯仰操纵杆控制上升/下降速率。  
油门决定空速——在50%油门时，飞行器将以预设的巡航速度保持当前高度。

当所有操纵杆都回中（无滚转/俯仰/偏航，约50%油门）时，飞行器将恢复直线水平飞行（受风力影响）并保持当前高度。  
这使得飞行时任何问题都容易恢复。  
滚转、俯仰和偏航均为角度控制（因此无法使机体翻滚或做筋斗）。

偏航操纵杆可用于增加/减少转弯时的偏航速率。  
若保持在中心位置，控制器会自行进行转弯协调，即根据当前滚转角自动应用必要的偏航速率以完成平滑转弯。

下图直观展示了该模式的行为（以[模式2发射机](../getting_started/rc_transmitter_receiver.md#transmitter_modes)为例）。

![高度控制固定翼](../../assets/flight_modes/altitude_fw.png)

## 技术概要

高度模式类似于[稳定模式](../flight_modes_fw/stabilized.md)，但增加了高度稳定功能。  
如果存在空速传感器，空速也会被稳定。  
机体航向不会被保持，可能因风力发生漂移。

- 滚转/俯仰/偏航输入回中（在死区内）:  
  - 自动驾驶仪使机体保持水平并维持高度和空速。  
- 输入超出中心位置:  
  - 俯仰操纵杆控制高度。  
  - 油门操纵杆在连接空速传感器时控制空速。没有空速传感器时，飞行器将以配平油门（[FW_THR_TRIM](../advanced_config/parameter_reference.md#FW_THR_TRIM)）水平飞行，根据需要增减油门以爬升或下降。  
  - 滚转操纵杆控制滚转角。自动驾驶仪将保持[协调飞行](https://en.wikipedia.org/wiki/Coordinated_flight)。  
  - 偏航操纵杆添加额外的偏航速率设定值（叠加在自动驾驶仪为保持协调飞行计算的值上）。  
    可用于手动改变机体侧滑。  
- 需要手动控制输入（如遥控器或操纵杆）。  
- 需要高度测量源（通常为气压计或GPS）。

## 参数

该模式受以下参数影响：

| 参数                                                                                             | 描述                                                          |
| ------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| <a id="FW_AIRSPD_MIN"></a>[FW_AIRSPD_MIN](../advanced_config/parameter_reference.md#FW_AIRSPD_MIN)    | 最小空速。默认值: 10 m/s。                                       |
| <a id="FW_AIRSPD_MAX"></a>[FW_AIRSPD_MAX](../advanced_config/parameter_reference.md#FW_AIRSPD_MAX)    | 最大空速。默认值: 20 m/s。                                       |
| <a id="FW_AIRSPD_TRIM"></a>[FW_AIRSPD_TRIM](../advanced_config/parameter_reference.md#FW_AIRSPD_TRIM) | 巡航速度。默认值: 15 m/s。                                       |
| <a id="FW_MAN_P_MAX"></a>[FW_MAN_P_MAX](../advanced_config/parameter_reference.md#FW_MAN_P_MAX)       | 姿态稳定模式下的最大俯仰设定值。默认值: 45 度。 |
| <a id="FW_MAN_R_MAX"></a>[FW_MAN_R_MAX](../advanced_config/parameter_reference.md#FW_MAN_R_MAX)       | 姿态稳定模式下的最大滚转设定值。默认值: 45 度。  |
| <a id="FW_T_CLMB_R_SP"></a>[FW_T_CLMB_R_SP](../advanced_config/parameter_reference.md#FW_T_CLMB_R_SP) | 最大爬升速率设定值。默认值: 3 m/s。                             |
| <a id="FW_T_SINK_R_SP"></a>[FW_T_SINK_R_SP](../advanced_config/parameter_reference.md#FW_T_SINK_R_SP) | 最大下降速率设定值。默认值: 2 m/s。                              |

<!--
FW notes:
FW position controller is basically 2 independent pieces
* L1 is for navigation - determines the roll and yaw needed to achieve the desired waypoint (or loiter)
* TECS is for speed and height control - determines throttle and elevator position needed to achieve the commanded altitude and airspeed
Overall that gives you an attitude setpoint (roll, pitch, yaw) and throttle which is sent off to the attitude controller
-->