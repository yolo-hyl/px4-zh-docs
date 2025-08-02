# 起飞模式（固定翼）

<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_起飞_飞行模式将使机体升至指定高度，然后进入[悬停模式](../flight_modes_fw/takeoff.md)。

机体默认使用[手动或弹射发射](#catapult-hand-launch)，但硬件支持时也可[配置](#RWTO_TKOFF)为使用[跑道起飞](#跑道起飞)。

::: info

- 该模式为自动模式 - 无需用户干预即可控制机体。
- 该模式至少需要有效的本地位置估算（不需要全球位置）。
  - 未配置位置估算的飞行器无法切换至此模式。
  - 飞行器若丢失位置估算将触发安全保护。
  - 未上锁的飞行器可切换至此模式但无法上锁。
- 遥控器模式切换键可用于更改飞行模式。
- 弹射起飞时遥控器摇杆输入被忽略，但跑道起飞时可用于微调机体位置。
- [故障检测器](../config/safety.md#failure-detector)会在起飞阶段出现故障时自动关闭发动机。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/commander/ModeUtil/mode_requirements.cpp -->

:::

## 技术概述

起飞模式（以及[固定翼任务起飞](../flight_modes_fw/mission.md#mission-takeoff)）有两种模式：[弹射/手抛](#catapult-hand-launch)或[跑道起飞](#跑道起飞)（硬件相关）。
该模式默认为弹射/手抛模式，但通过设置[RWTO_TKOFF](#RWTO_TKOFF)为1可切换为跑道起飞。

使用_起飞模式_时，首先切换到该模式，然后激活机体。
手抛/弹射发射的加速度会触发电机启动。
跑道发射时，激活机体后电机将自动加速。

无论哪种模式，都会定义飞行路径（起点和起飞航向）和安全高度：

- 起点为首次进入起飞模式时的机体位置
- 航向设置为激活时的机体航向
- 安全高度设置为[MIS_TAKEOFF_ALT](#MIS_TAKEOFF_ALT)

起飞时，飞机会沿起点和航向定义的航线飞行，以最大爬升率（[FW_T_CLMB_MAX](../advanced_config/parameter_reference.md#FW_T_CLMB_MAX)）爬升至安全高度。达到安全高度后，机体将进入[悬停模式](../flight_modes_fw/takeoff.md)。

### 参数

影响弹射/手抛和跑道起飞的参数：

| 参数                                                                                                   | 描述                                                                                                                      |
| ----------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| <a id="MIS_TAKEOFF_ALT"></a>[MIS_TAKEOFF_ALT](../advanced_config/parameter_reference.md#MIS_TAKEOFF_ALT)    | 机体在起飞过程中爬升到的高于Home的最低高度设定值。                                              |
| <a id="FW_TKO_AIRSPD"></a>[FW_TKO_AIRSPD](../advanced_config/parameter_reference.md#FW_TKO_AIRSPD)          | 起飞空速（若未由操作员定义则设置为[FW_AIRSPD_MIN](../advanced_config/parameter_reference.md#FW_AIRSPD_MIN)） |
| <a id="FW_TKO_PITCH_MIN"></a>[FW_TKO_PITCH_MIN](../advanced_config/parameter_reference.md#FW_TKO_PITCH_MIN) | 爬升阶段的最小俯仰角设定值                                                               |
| <a id="FW_T_CLMB_MAX"></a>[FW_T_CLMB_MAX](../advanced_config/parameter_reference.md#FW_T_CLMB_MAX)          | 最大爬升率。                                                                                                              |
| <a id="FW_FLAPS_TO_SCL"></a>[FW_FLAPS_TO_SCL](../advanced_config/parameter_reference.md#FW_FLAPS_TO_SCL)    | 起飞期间的襟翼设定值                                                                                                    |
| <a id="FW_AIRSPD_FLP_SC"></a>[FW_AIRSPD_FLP_SC](../advanced_config/parameter_reference.md#FW_AIRSPD_FLP_SC)  | 襟翼完全展开时的最小空速系数。当FW_TKO_AIRSPD低于FW_AIRSPD_MIN时需要设置。                                                                                             |

::: info
机体在起飞过程中始终遵循正常的FW最大/最小油门设置（[FW_THR_MIN](../advanced_config/parameter_reference.md#FW_THR_MIN), [FW_THR_MAX](../advanced_config/parameter_reference.md#FW_THR_MAX)）。
:::

<a id="hand_launch"></a>

## 弹射/手投模式

在 _弹射/手投模式_ 下，机体等待通过加速度触发检测发射。  
发射后，启动电机并以最大爬升率 [FW_T_CLMB_MAX](#FW_T_CLMB_MAX) 爬升，同时保持俯仰设定值高于 [FW_TKO_PITCH_MIN](#FW_TKO_PITCH_MIN)。  
当达到 [MIS_TAKEOFF_ALT](#MIS_TAKEOFF_ALT) 时，将自动切换到 [悬停模式](../flight_modes_fw/hold.md) 并悬停。  

整个起飞序列中会忽略所有遥控器摇杆输入。  

要在此模式下发射：  

1. 解锁机体  
1. 将机体切换到 _起飞模式_  
1. 将机体（有力地）直接迎风弹射/投掷。  
   也可以先晃动机体，等待电机加速后再投掷  

### 参数（发射检测器）

_发射检测器_ 受以下参数影响：  

| 参数                                                                                                   | 描述                                                                              |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| <a id="FW_LAUN_DETCN_ON"></a>[FW_LAUN_DETCN_ON](../advanced_config/parameter_reference.md#FW_LAUN_DETCN_ON) | 启用自动发射检测。若禁用，则在解锁时电机即开始加速                                  |
| <a id="FW_LAUN_AC_THLD"></a>[FW_LAUN_AC_THLD](../advanced_config/parameter_reference.md#FW_LAUN_AC_THLD)    | 加速度阈值（机体向前方向的加速度必须高于此值）                                      |
| <a id="FW_LAUN_AC_T"></a>[FW_LAUN_AC_T](../advanced_config/parameter_reference.md#FW_LAUN_AC_T)             | 触发时间（加速度必须高于阈值持续此秒数）                                            |
| <a id="FW_LAUN_MOT_DEL"></a>[FW_LAUN_MOT_DEL](../advanced_config/parameter_reference.md#FW_LAUN_MOT_DEL)    | 从发射检测到电机加速的延迟时间                                                      |

<a id="runway_launch"></a>

## 跑道起飞

跑道起飞功能仅适用于带有起落架和可转向轮的机体。  
首先需要通过参数 [FW_W_EN](#FW_W_EN) 启用轮子控制器。

机体在起飞启动时应保持居中并沿跑道对齐。  
操作员可在跑道上通过"微调"功能协助保持机体居中和对齐（参见 [RWTO_NUDGE](../advanced_config/parameter_reference.md#RWTO_NUDGE)）。

_跑道起飞模式_ 包含以下阶段：

1. **油门上升**：在 [RWTO_RAMP_TIME](../advanced_config/parameter_reference.md#RWTO_RAMP_TIME) 时间内将油门提升至 [RWTO_MAX_THR](../advanced_config/parameter_reference.md#RWTO_MAX_THR)。
2. **固定于跑道**：俯仰固定，无滚转，直到达到旋转空速 ([RWTO_ROT_AIRSPD](../advanced_config/parameter_reference.md#RWTO_ROT_AIRSPD))。操作员可通过偏航杆左右微调机体位置。
3. **爬升阶段**：增加俯仰设定值并爬升至起飞高度。为防止机翼触地，控制器在接近地面时会将滚转设定值锁定为0，随着爬升高度逐渐允许更大滚转。该逻辑基于在 [FW_WING_SPAN](#FW_WING_SPAN) 和 [FW_WING_HEIGHT](#FW_WING_HEIGHT) 中配置的机体几何参数。

::: info
为实现平滑起飞，可能需要调整跑道轮子控制器。
该控制器包含速率控制器（P-I-FF控制器，参数为 [FW_WR_P](../advanced_config/parameter_reference.md#FW_WR_P)、[FW_WR_I](../advanced_config/parameter_reference.md#FW_WR_I)、[FW_WR_FF](../advanced_config/parameter_reference.md#FW_WR_FF)）。
:::

### 参数（跑道起飞）

跑道起飞受以下参数影响：

| 参数                                                                                                   | 描述                                                                                                                  |
| ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| <a id="RWTO_TKOFF"></a>[RWTO_TKOFF](../advanced_config/parameter_reference.md#RWTO_TKOFF)               | 启用跑道起飞                                                                                                          |
| <a id="FW_W_EN"></a>[FW_W_EN](../advanced_config/parameter_reference.md#FW_W_EN)                        | 启用轮子控制器                                                                                                        |
| <a id="RWTO_MAX_THR"></a>[RWTO_MAX_THR](../advanced_config/parameter_reference.md#RWTO_MAX_THR)         | 跑道起飞期间最大油门                                                                                                  |
| <a id="RWTO_RAMP_TIME"></a>[RWTO_RAMP_TIME](../advanced_config/parameter_reference.md#RWTO_RAMP_TIME)   | 油门上升时间                                                                                                          |
| <a id="RWTO_ROT_AIRSPD"></a>[RWTO_ROT_AIRSPD](../advanced_config/parameter_reference.md#RWTO_ROT_AIRSPD) | 开始旋转（俯仰上扬）的空速阈值。若未配置则默认设置为 0.9\*FW_TKO_AIRSPD。                                             |
| <a id="RWTO_ROT_TIME"></a>[RWTO_ROT_TIME](../advanced_config/parameter_reference.md#RWTO_ROT_TIME)     | 起飞旋转期间线性提升俯仰约束所需时间。                                                                                |
| <a id="FW_TKO_AIRSPD"></a>[FW_TKO_AIRSPD](../advanced_config/parameter_reference.md#FW_TKO_AIRSPD)       | 起飞爬升阶段（旋转后）的空速设定值。若未配置则默认设置为 FW_AIRSPD_MIN。                                            |
| <a id="RWTO_NUDGE"></a>[RWTO_NUDGE](../advanced_config/parameter_reference.md#RWTO_NUDGE)               | 在跑道上启用轮子控制器微调功能                                                                                      |
| <a id="FW_WING_SPAN"></a>[FW_WING_SPAN](../advanced_config/parameter_reference.md#FW_WING_SPAN)         | 机体翼展。用于防止机翼触地。                                                                                          |
| <a id="FW_WING_HEIGHT"></a>[FW_WING_HEIGHT](../advanced_config/parameter_reference.md#FW_WING_HEIGHT)   | 机翼离地高度（地面间隙）。用于防止机翼触地。                                                                          |

## 另请参见

- [起飞模式 (MC)](../flight_modes_mc/takeoff.md)
- [规划任务起飞](../flight_modes_fw/mission.md#mission-takeoff)

<!-- this maps to AUTO_TAKEOFF in dev -->