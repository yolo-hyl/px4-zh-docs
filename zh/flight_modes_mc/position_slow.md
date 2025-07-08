# 位置慢速模式（多旋翼）

<Badge type="tip" text="PX4 v1.15" />

<img src="../../assets/site/difficulty_easy.png" title="Easy to fly" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="Manual/Remote control required" width="30px" />&nbsp;<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

_位置慢速_模式是标准[位置模式](../flight_modes_mc/position.md)的速度和偏航率受限版本。

该模式的工作原理与_位置模式_完全相同，但通过重新缩放控制器杆位移以降低最大速度（以及相应降低加速度）。
您可以通过此模式快速将机体速度降低到安全范围（当其移动速度超过限定轴的最大速度时）。
您还可以使用它来提高杆位输入的精度，特别是在靠近障碍物飞行时，或遵循如[EASA的低速模式/功能](https://www.easa.europa.eu/en/light/topics/flying-drones-close-people)等法规。

速度限制可以通过参数、[遥控器](../getting_started/rc_transmitter_receiver.md)的旋转旋钮、滑块或开关，或者使用MAVLink进行设置。
遥控器设置的限制优先于MAVLink设置的限制，而MAVLink又优先于参数设置的限制。
这些限制只能低于正常_位置_模式的限制。

## 通过参数设置限制

可以通过参数分别设置慢速模式的水平速度、垂直速度和偏航率的最大值。
当慢速模式的最大期望速度固定时，这种方法很有用，您只需能够快速降低到安全速度范围（可能通过控制器上的开关实现）。

下表展示了用于设置_位置慢速_模式和_位置_模式最大值的参数及其默认值：

| 轴                | 位置慢速模式                            | 位置模式                                                                                 |
| ------------------- | --------------------------------------------- | --------------------------------------------------------------------------------------------- |
| 水平速度          | [MC_SLOW_DEF_HVEL][mc_slow_def_hvel] (3 m/s)  | [MPC_VEL_MANUAL][mpc_vel_manual] (10 m/s)                                                     |
| 垂直速度          | [MC_SLOW_DEF_VVEL][mc_slow_def_vvel] (1 m/s)  | [MPC_Z_VEL_MAX_UP][mpc_z_vel_max_up] (3 m/s) / [MPC_Z_VEL_MAX_DN][mpc_z_vel_max_dn] (1.5 m/s) |
| 偏航率            | [MC_SLOW_DEF_YAWR][mc_slow_def_yawr] (45 °/s) | [MPC_MAN_Y_MAX][mpc_man_y_max] (150 °/s)                                                      |

例如，当从位置模式切换到位置慢速模式时，水平速度的最大默认值会从10 m/s降至3 m/s。
如果水平速度超过3 m/s，将会被限制到3 m/s。

请注意，只有在未通过遥控器或MAVLink提供限制时，参数才会生效。

<!-- links used in table above -->

[mpc_vel_manual]: ../advanced_config/parameter_reference.md#MPC_VEL_MANUAL
[mc_slow_def_hvel]: ../advanced_config/parameter_reference.md#MC_SLOW_DEF_HVEL
[mpc_z_vel_max_up]: ../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_UP
[mpc_z_vel_max_dn]: ../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_DN
[mc_slow_def_vvel]: ../advanced_config/parameter_reference.md#MC_SLOW_DEF_VVEL
[mpc_man_y_max]: ../advanced_config/parameter_reference.md#MPC_MAN_Y_MAX
[mc_slow_def_yawr]: ../advanced_config/parameter_reference.md#MC_SLOW_DEF_YAWR

## 通过遥控器控制设置限制

您可以通过[遥控器](../getting_started/rc_transmitter_receiver.md)上的旋转旋钮、滑块或开关，将最大速度映射到某个轴（水平/垂直/偏航）。
当飞行中适当的慢速模式最大值需要变化时，这种方法很有用。

如果输入控制设置为其最高值，机体速度将与_位置_模式相同。
如果输入设置为最低值，机体最大速度将设置为对应`MC_SLOW_MIN_`参数的值（如下表所示）。
如果某个轴的遥控器输入被映射，它将优先于所有其他输入。

下表列出每个轴及其用于选择对应遥控器辅助通道的参数，以及该轴最大速度最小值的参数：

| 轴                | 辅助输入映射参数     | 最大速度最小值参数 |
| ------------------- | ------------------------------------ | ----------------------------------------------- |
| 水平速度          | [MC_SLOW_MAP_HVEL][mc_slow_map_hvel] | [MC_SLOW_MIN_HVEL][mc_slow_min_hvel]            |
| 垂直速度          | [MC_SLOW_MAP_VVEL][mc_slow_map_vvel] | [MC_SLOW_MIN_VVEL][mc_slow_min_vvel]            |
| 偏航率            | [MC_SLOW_MAP_YAWR][mc_slow_map_yawr] | [MC_SLOW_MIN_YAWR][mc_slow_min_yawr]            |

<!-- links used in table above -->

[mc_slow_map_hvel]: ../advanced_config/parameter_reference.md#MC_SLOW_MAP_HVEL
[mc_slow_min_hvel]: ../advanced_config/parameter_reference.md#MC_SLOW_MIN_HVEL
[mc_slow_map_vvel]: ../advanced_config/parameter_reference.md#MC_SLOW_MAP_VVEL
[mc_slow_min_vvel]: ../advanced_config/parameter_reference.md#MC_SLOW_MIN_VVEL
[mc_slow_map_yawr]: ../advanced_config/parameter_reference.md#MC_SLOW_MAP_YAWR
[mc_slow_min_yawr]: ../advanced_config/parameter_reference.md#MC_SLOW_MIN_YAWR

要使用此方法：

1. 确保您的遥控器有额外的输入和额外的RC通道来传输其位置。
2. 通过设置[RC_MAP_AUXn](../advanced_config/parameter_reference.md#RC_MAP_AUX1)将包含旋钮位置的通道映射为6个辅助直通输入之一。
3. 使用适当的`MC_SLOW_MAP_`参数为要控制的轴进行映射（见上表）。

例如，如果要将RC通道`8`映射为限制水平速度，可以将[RC_MAP_AUX1](../advanced_config/parameter_reference.md#RC_MAP_AUX1)设置为`8`，并将[MC_SLOW_MAP_HVEL][mc_slow_map_hvel]设置为`1`。
通道8的RC输入将设置水平速度限制在[MC_SLOW_MIN_HVEL][mc_slow_min_hvel]和[MPC_VEL_MANUAL][mpc_vel_manual]之间。

## 通过MAVLink设置限制

您可以通过MAVLink协议动态调整速度限制，适用于需要实时调整的场景。

## 相关链接

- [位置模式文档](../flight_modes_mc/position.md)
- [参数配置参考](../advanced_config/parameter_reference.md)