# 位置模式（多旋翼）

<img src="../../assets/site/difficulty_easy.png" title="Easy to fly" width="30px" />&nbsp;<img src="../../assets/site/remote_control.svg" title="Manual/Remote control required" width="30px" />&nbsp;<img src="../../assets/site/position_fixed.svg" title="Position fix required (e.g. GPS)" width="30px" />

位置模式是一种易于飞行的遥控模式，横滚和俯仰摇杆控制机体在左右和前后方向上的地面加速度（类似汽车油门踏板），油门控制升降速度。
当摇杆释放/居中时，机体将主动制动、调平并锁定在三维空间中的位置——补偿风力和其他外力。
在全速偏转时，机体将初始以[MPC_ACC_HOR_MAX](#MPC_ACC_HOR_MAX)加速，随后逐渐降低直到达到最终速度[MPC_VEL_MANUAL](#MPC_VEL_MANUAL)。

:::tip
位置模式是新手最安全的手动模式。
与[高度模式](../flight_modes_mc/altitude.md)和[稳定模式](../flight_modes_mc/manual_stabilized.md)不同，当摇杆居中时，机体将停止而非继续运动直到被风阻力减缓。
:::

下图展示了该模式的行为（以模式2发射机为例）。

![MC Position Mode](../../assets/flight_modes/position_mc.png)

### 降落

此模式下降落非常简单：

1. 使用横滚和俯仰摇杆将无人机水平定位在降落点上方。
1. 松开横滚和俯仰摇杆并等待其完全停止。
1. 轻推油门杆向下直到机体接触地面。
1. 将油门杆完全下压以促进和加速降落检测。
1. 机体将降低螺旋桨推力，检测地面并[自动解除武装](../advanced_config/prearm_arm_disarm.md#auto-disarming)（默认行为）。

:::warning
虽然校准良好的机体很少出现问题，但有时降落可能会遇到问题。

- 如果机体不水平停止：
  - 仍可控制降落至[高度模式](../flight_modes_mc/altitude.md)。
    方法与上述相同，区别在于必须使用横滚和俯仰摇杆手动确保机体保持在降落点上方。
  - 降落检查GPS和磁力计方向、校准。
- 如果机体未检测到地面/降落并解除武装：
  - 在机体着陆后切换到[稳定模式](../flight_modes_mc/manual_stabilized.md)并保持油门杆低位，通过手势或其他命令手动解除武装。
    或者当机体已着陆时也可以使用kill switch。

:::

## 技术摘要

RC模式下，横滚（Roll）、俯仰（Pitch）、油门（Throttle）杆位控制机体在对应轴/方向的运动。中立杆位使机体水平并保持固定高度和位置抵御风力干扰。

- 横滚、俯仰、油门杆位在中立位置（处于RC死区 [MPC_HOLD_DZ](../advanced_config/parameter_reference.md#MPC_HOLD_DZ) 内）时，机体可抵御风力等干扰保持x、y、z轴位置稳定。
- 超出中立范围时：
  - 横滚/俯仰杆位控制机体在左-右、前-后方向的水平加速度。
  - 油门杆位控制升降速度。
  - 偏航（Yaw）杆位控制水平面以上的角旋转速率。
- 起飞：
  - 飞行器着陆时，若油门杆位提升至62.5%（满量程范围的下限）以上，将触发起飞。
- 需要全局位置估计。
- 需要手动控制输入（如遥控器控制、手柄）。
  - 横滚、俯仰、油门：自动驾驶仪辅助抵御风力保持位置。
  - 偏航：自动驾驶仪辅助稳定姿态角速率。
    遥控杆位位置映射为对应方向的机体旋转速率。

### 参数

[多旋翼位置控制](../advanced_config/parameter_reference.md#multicopter-position-control)组中所有参数均相关。部分关键参数如下：

| 参数                                                                                                   | 描述                                                                                                                                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="MPC_HOLD_DZ"></a>[MPC_HOLD_DZ](../advanced_config/parameter_reference.md#MPC_HOLD_DZ)           | 启用位置保持的杆位死区。默认值：0.1（占满杆范围的10%）。                                                                                                                                                                                                                                           |
| <a id="MPC_Z_VEL_MAX_UP"></a>[MPC_Z_VEL_MAX_UP](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_UP) | 最大垂直上升速度。默认值：3 m/s。                                                                                                                                                                                                                                                            |
| <a id="MPC_Z_VEL_MAX_DN"></a>[MPC_Z_VEL_MAX_DN](../advanced_config/parameter_reference.md#MPC_Z_VEL_MAX_DN) | 最大垂直下降速度。默认值：1 m/s。                                                                                                                                                                                                                                                           |
| <a id="MPC_LAND_ALT1"></a>[MPC_LAND_ALT1](../advanced_config/parameter_reference.md#MPC_LAND_ALT1)     | 慢速降落第一阶段的触发高度。低于此高度时，下降速度将被限制在 [MPC_Z_VEL_MAX_DN](#MPC_Z_VEL_MAX_DN)（或 `MPC_Z_V_AUTO_DN`）与 [MPC_LAND_SPEED](#MPC_LAND_SPEED) 之间。该值需高于 [MPC_LAND_ALT2](#MPC_LAND_ALT2)。默认值10米。 |
| <a id="MPC_LAND_ALT2"></a>[MPC_LAND_ALT2](../advanced_config/parameter_reference.md#MPC_LAND_ALT2)     | 慢速降落第二阶段的触发高度。低于此高度时，下降速度将被限制在 [`MPC_LAND_SPEED`](#MPC_LAND_SPEED)。该值需低于 "MPC_LAND_ALT1"。默认值5米。                                                                                                            |
| <a id="RCX_DZ"></a>`RCX_DZ`                                                                            | 通道X的遥控死区。油门通道的X值取决于 [RC_MAP_THROTTLE](../advanced_config/parameter_reference.md#RC_MAP_THROTTLE) 的设置。例如，若油门为通道4，则 [RC4_DZ](../advanced_config/parameter_reference.md#RC4_DZ) 定义死区。          |
| <a id="MPC_xxx"></a>`MPC_XXXX`                                                                         | 大部分MPC_xxx参数会影响此模式下的飞行行为（至少在某种程度上）。例如，[MPC_THR_HOVER](../advanced_config/parameter_reference.md#MPC_THR_HOVER) 定义了机体悬停所需的推力。                                                                       |
| <a id="MPC_POS_MODE"></a>[MPC_POS_MODE](../advanced_config/parameter_reference.md#MPC_POS_MODE)        | 杆位输入到运动的转换策略。从PX4 v1.12开始，默认值（`基于加速度`）为杆位位置控制加速度（类似于汽车油门踏板）。其他选项允许杆位偏移直接控制地面速度，且可带或不带平滑和加速度限制。                                                                 |
| <a id="MPC_ACC_HOR_MAX"></a>[MPC_ACC_HOR_MAX](../advanced_config/parameter_reference.md#MPC_ACC_HOR_MAX) | 最大水平加速度。                                                                                                                                                                                                                                                                             |
| <a id="MPC_VEL_MANUAL"></a>[MPC_VEL_MANUAL](../advanced_config/parameter_reference.md#MPC_VEL_MANUAL)   | 最大水平速度。                                                                                                                                                                                                                                                                                 |
| <a id="MPC_LAND_SPEED"></a>[MPC_LAND_SPEED](../advanced_config/parameter_reference.md#MPC_LAND_SPEED)   | 降落下降率。默认0.7 m/s。                                                                                                                                                                                                                                                                       |## 补充信息

### 位置丢失/安全

位置模式的运行依赖于获得可接受的位置估计。  
如果位置估计低于可接受水平（例如由于GPS丢失），可能会触发[位置丢失（GPS）失效保护](../config/safety.md#position-gnss-loss-failsafe)。  
根据配置、遥控器是否存在以及高度估计是否充足，PX4可能会切换至高度模式、手动模式、降落模式或终止任务。

## 另请参见

- [Position Slow Mode](../flight_modes_mc/position_slow.md)