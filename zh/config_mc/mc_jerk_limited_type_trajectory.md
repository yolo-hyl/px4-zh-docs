# 多旋翼的加加速度限制轨迹类型

加加速度限制轨迹类型通过用户摇杆输入或任务变化（如拍摄、测绘、货运）提供平滑运动。  
它生成对称的平滑S曲线，始终保证加加速度和加速度限制。

该轨迹类型在[任务模式](../flight_modes_mc/mission.md)中始终启用。  
要在[位置模式](../flight_modes_mc/position.md)中启用它，将参数[MPC_POS_MODE](../advanced_config/parameter_reference.md#MPC_POS_MODE)设置为`Smoothed velocity`。

::: info
加加速度限制类型在位置模式中**默认不启用**。  
它可能不适合需要快速响应的机体/应用场景——例如竞速四旋翼。
:::

## 轨迹生成器

下图显示了一个典型的加加速度限制曲线，约束条件如下：

- `jMax`：最大加加速度  
- `a0`：初始加速度  
- `aMax`：最大加速度  
- `a3`：最终加速度（始终为0）  
- `v0`：初始速度  
- `vRef`：期望速度  

约束条件`jMax`和`aMax`可通过参数由用户配置，并在手动位置控制和自动模式中可能不同。  
生成的速度曲线通常称为"S-Curve"。

![加加速度限制轨迹](../../assets/config/mc/jerk_limited_trajectory_1d.png)

## 手动模式

在手动位置模式下，摇杆映射到速度：全XY摇杆偏移对应[MPC_VEL_MANUAL](../advanced_config/parameter_reference.md#MPC_VEL_MANUAL)，全Z摇杆偏移对应[MPC_Z_VEL_MAX_UP](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_UP)（上升运动）或[MPC_Z_VEL_MAX_DN](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_DN)（下降运动）。

### 约束条件

XY平面：  
- `jMax`：[MPC_JERK_MAX](../advanced_config/parameter_reference.md#MPC_JERK_MAX)  
- `aMax`：[MPC_ACC_HOR_MAX](../advanced_config/parameter_reference.md#MPC_ACC_HOR_MAX)  

Z轴：  
- `jMax`：[MPC_JERK_MAX](../advanced_config/parameter_reference.md#MPC_JERK_MAX)  
- `aMax`（上升运动）：[MPC_ACC_UP_MAX](../advanced_config/parameter_reference.md#MPC_ACC_UP_MAX)  
- `aMax`（下降运动）：[MPC_ACC_DOWN_MAX](../advanced_config/parameter_reference.md#MPC_ACC_DOWN_MAX)  

## 自动模式

在自动模式下，期望速度为[MPC_XY_CRUISE](../advanced_config/parameter_reference.md#MPC_XY_CRUISE)，但该值会根据到下一个航点的距离、航点处的最大可能速度以及最大期望加速度和加加速度自动调整。  
垂直速度由[MPC_Z_V_AUTO_UP](../advanced_config/parameter_reference.md#MPC_Z_V_AUTO_UP)（上升运动）和[MPC_Z_V_AUTO_DN](../advanced_config/parameter_reference.md#MPC_Z_V_AUTO_DN)（下降运动）定义。

### 约束条件

XY平面：  
- `jMax`：[MPC_JERK_AUTO](../advanced_config/parameter_reference.md#MPC_JERK_AUTO)  
- `aMax`：[MPC_ACC_HOR](../advanced_config/parameter_reference.md#MPC_ACC_HOR)  

Z轴：  
- `jMax`：[MPC_JERK_AUTO](../advanced_config/parameter_reference.md#MPC_JERK_AUTO)  
- `aMax`（上升运动）：[MPC_ACC_UP_MAX](../advanced_config/parameter_reference.md#MPC_ACC_UP_MAX)  
- `aMax`（下降运动）：[MPC_ACC_DOWN_MAX](../advanced_config/parameter_reference.md#MPC_ACC_DOWN_MAX)  

靠近航点时的距离到速度增益：  
- [MPC_XY_TRAJ_P](../advanced_config/parameter_reference.md#MPC_XY_TRAJ_P)  

### 相关参数

- [MPC_XY_VEL_MAX](../advanced_config/parameter_reference.md#MPC_XY_VEL_MAX)  
- [MPC_Z_VEL_MAX_UP](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_UP)  
- [MPC_Z_VEL_MAX_DN](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_DN)  
- [MPC_TKO_SPEED](../advanced_config/parameter_reference.md#MPC_TKO_SPEED)  
- [MPC_LAND_SPEED](../advanced_config/parameter_reference.md#MPC_LAND_SPEED)  
- [MPC_LAND_ALT1](../advanced_config/parameter_reference.md#MPC_LAND_ALT1)  
- [MPC_LAND_ALT2](../advanced_config/parameter_reference.md#MPC_LAND_ALT2)