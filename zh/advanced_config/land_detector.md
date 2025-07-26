# 陆地检测器配置

陆地检测器是一个动态机体模型，表示从地面接触到着陆的关键机体状态。  
本主题介绍了为改善着陆行为需要调整的主要参数。

## 自动解除武装

陆地检测器会在着陆时自动解除机体武装。

你可以通过设置[COM_DISARM_LAND](../advanced_config/parameter_reference.md#COM_DISARM_LAND)指定着陆后系统解除武装的秒数（或通过将参数设置为-1关闭自动解除武装功能）。

## 多旋翼配置

所有与着陆检测器相关的参数在参数参考中以[LNDMC](../advanced_config/parameter_reference.md#land-detector)为前缀列出（这些参数可通过QGroundControl的[参数编辑器](../advanced_config/parameters.md)进行编辑）。

:::tip
关于参数如何影响着陆的详细信息，请参见下方的[陆地检测器状态](#多旋翼陆地检测器状态)。
:::

为改善特定机体的着陆行为，可能需要调整的其他关键参数包括：

- [MPC_THR_HOVER](../advanced_config/parameter_reference.md#MPC_THR_HOVER) - 系统的悬停油门（默认为50%）。  
  正确设置此参数对提高高度控制精度和确保正确检测着陆至关重要。  
  赛车或未装载载荷的大摄像头无人机可能需要更低的设置值（例如35%）。

  ::: info
  错误设置`MPC_THR_HOVER`可能导致在空中（特别是在[Position mode](../flight_modes_mc/position.md)或[Altitude mode](../flight_modes_mc/altitude.md)下降时）误判为地面接触或已着陆。  
  这会导致机体出现"抽搐"现象（电机突然停转，随后立即重启）。
  :::

- [MPC_THR_MIN](../advanced_config/parameter_reference.md#MPC_THR_MIN) - 系统的最低油门。  
  需设置为允许受控下降的值。
- [MPC_LAND_CRWL](../advanced_config/parameter_reference.md#MPC_LAND_CRWL) - 如果系统配备距离传感器且正常工作，自主着陆最后阶段的垂直速度。需设置为大于LNDMC_Z_VEL_MAX的值。

### 多旋翼陆地检测器状态

为检测着陆，多旋翼机体需要依次经过三个不同状态，每个状态包含前序状态的条件并增加更严格的约束。  
如果因缺少传感器无法满足某个条件，则默认该条件为真。  
例如，在[Acro mode](../flight_modes_mc/acro.md)下，除陀螺仪传感器外无其他传感器时，检测完全依赖推力输出和时间。

要进入下一个状态，每个条件需在配置的总陆地检测器触发时间[LNDMC_TRIG_TIME](../advanced_config/parameter_reference.md#LNDMC_TRIG_TIME)的三分之一内持续为真。  
如果机体配备距离传感器但当前无法测距（通常因距离过远），触发时间将增加3倍。

如果任一条件失败，陆地检测器将立即退出当前状态。

#### 地面接触

该状态的条件：

- 无垂直运动（[LNDMC_Z_VEL_MAX](../advanced_config/parameter_reference.md#LNDMC_Z_VEL_MAX)）
- 无水平运动（[LNDMC_XY_VEL_MAX](../advanced_config/parameter_reference.md#LNDMC_XY_VEL_MAX)）
- 推力低于[MPC_THR_MIN](../advanced_config/parameter_reference.md#MPC_THR_MIN) + (悬停油门 - [MPC_THR_MIN](../advanced_config/parameter_reference.md#MPC_THR_MIN)) \* (0.3，除非有悬停推力估计值，则为0.6)，
- 额外检查：机体当前是否处于高度率控制飞行模式：机体必须有意图下降（垂直速度设定值高于LNDMC_Z_VEL_MAX）。
- 额外检查：配备距离传感器的机体：当前地面距离低于1m。

如果机体处于位置控制或速度控制且检测到地面接触，  
位置控制器将把推力矢量在机体x-y轴方向设为零。

#### 可能着陆

该状态的条件：

- [地面接触](#地面接触)状态的所有条件为真
- 无旋转（[LNDMC_ROT_MAX](../advanced_config/parameter_reference.md#LNDMC_ROT_MAX)）
- 推力为低值`MPC_THR_MIN + (MPC_THR_HOVER - MPC_THR_MIN) * 0.1`
- 未检测到自由下落

如果机体仅了解推力和角速度，  
要进入下一状态，机体需在8.0秒内保持低推力且无旋转。

如果机体处于位置或速度控制且检测到可能着陆，  
位置控制器将把推力矢量设为零。

#### 已着陆

该状态的条件：

- [可能着陆](#可能着陆)状态的所有条件为真

## 固定翼配置

固定翼着陆检测的调整参数：

- [LNDFW_AIRSPD_MAX](../advanced_config/parameter_reference.md#LNDFW_AIRSPD_MAX) - 系统仍被视为着陆时的最大空速。  
  需在空速感知精度和触发速度之间取得平衡。  
  更好的空速传感器允许该参数采用更低值。
- [LNDFW_VEL_XY_MAX ](../advanced_config/parameter_reference.md#LNDFW_VEL_XY_MAX) - 系统仍被视为着陆时的最大水平速度。
- [LNDFW_VEL_Z_MAX](../advanced_config/parameter_reference.md#LNDFW_VEL_XY_MAX) - 系统仍被视为着陆时的最大垂直速度。
- [LNDFW_XYACC_MAX](../advanced_config/parameter_reference.md#LNDFW_XYACC_MAX) - 系统仍被视为着陆时的最大水平加速度。
- [LNDFW_TRIG_TIME](../advanced_config/parameter_reference.md#LNDFW_TRIG_TIME) - 上述条件需在此触发时间内满足以判定着陆。

::: info
当启用固定翼发射检测（[FW_LAUN_DETCN_ON](../advanced_config/parameter_reference.md#FW_LAUN_DETCN_ON)）时，机体将保持"着陆"状态直至检测到起飞（该检测完全基于加速度而非速度）。
:::

## 垂直起降（VTOL）陆地检测器

当系统处于悬停模式时，VTOL陆地检测器与多旋翼检测器完全相同。在固定翼模式下，陆地检测功能被禁用。