# 着陆模式（Fixed-Wing）

<img src="../../assets/site/position_fixed.svg" title="Position estimate required (e.g. GPS)" width="30px" />

_Land_ 飞行模式会使机体在模式激活位置下降，沿圆形路径下降直至触地。  
着陆后，机体将在短暂停留后自动解除武装（默认行为）。

:::warning
固定翼 _land mode_ 仅应在 **紧急情况** 下使用！  
机体将无视地形适航性围绕当前位置下降，并沿圆形飞行路径触地。

建议优先使用 [Return mode](../flight_modes_fw/return.md) 配合预定义的 [Fixed-wing mission landing](../flight_modes_fw/mission.md#mission-landing)。
:::

::: info

- 自动模式 - 无需用户干预即可控制机体。
- 需要至少有效的局部位置估算（不依赖全局位置）。
  - 无有效局部位置时飞行器无法切换至该模式。
  - 位置估算丢失时飞行器将触发紧急保护。
- 模式会阻止武装（切换至该模式时机体必须已武装）。
- 任何机体均可通过遥控器模式开关切换飞行模式。
- 遥控器摇杆输入将被忽略。
- 可通过 [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND) MAVLink 命令或显式切换至 Land mode 触发该模式。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->
:::

## 技术摘要

Land mode 使机体沿螺旋状下降路径（corkscrew）下降，直至触地。

模式激活时，机体以 [NAV_LOITER_RAD](#NAV_LOITER_RAD) 设置的盘旋半径围绕当前位置盘旋，并以恒定下降速度开始下降。  
下降速度由 [FW_LND_ANG](#FW_LND_ANG) 和设定的着陆空速 [FW_LND_AIRSPD](#FW_LND_AIRSPD) 计算得出。若配置了 flare（拉平）功能，机体将执行拉平动作（见 [Flaring](../flight_modes_fw/mission.md#flaring-roll-out)），否则会以恒定下降率沿圆形路径下降直至检测到着陆。

[手动微调](../flight_modes_fw/mission.md#automatic-abort) 和 [自动着陆中止](../flight_modes_fw/mission.md#nudging) 在 Land mode 中不可用。

### 参数

可通过以下参数配置 Land mode 行为。

| 参数                                                                                             | 描述                                                                  |
| ----------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| <a id="NAV_LOITER_RAD"></a>[NAV_LOITER_RAD](../advanced_config/parameter_reference.md#NAV_LOITER_RAD) | 控制器在整个着陆序列中跟踪的盘旋半径。 |
| <a id="FW_LND_ANG"></a>[FW_LND_ANG](../advanced_config/parameter_reference.md#FW_LND_ANG)             | 飞行路径角度设定值。                                              |
| <a id="FW_LND_AIRSPD"></a>[FW_LND_AIRSPD](../advanced_config/parameter_reference.md#FW_LND_AIRSPD)    | 空速设定值。                                                       |
| <a id="FW_AIRSPD_FLP_SC"></a>[FW_AIRSPD_FLP_SC](../advanced_config/parameter_reference.md#FW_AIRSPD_FLP_SC)    | 襟翼完全展开时的最小空速缩放因子。当 FW_LND_AIRSPD 低于 FW_AIRSPD_MIN 时需设置。 |

## 参见

- [Land Mode (MC)](../flight_modes_mc/land.md)
- [Land Mode (VTOL)](../flight_modes_vtol/land.md)