# 安装飞控

飞控应尽可能安装在机架的重心（CoG）附近，正面朝上，且方向应使 _航向标记箭头_ 指向机体前方。  
通常需要[减振](#vibration-isolation)，请遵循制造商建议。  
若以这种方式安装，则无需进一步的PX4配置。

## 方向

几乎所有飞控都带有 _航向标记箭头_（如下图所示）。  
飞控应正面朝上安装在机架上，方向应使箭头指向机体前方（适用于所有机型 - 固定翼、多旋翼、垂直起降、地面车辆等）。

![FC 航向标记](../../assets/qgc/setup/sensor/fc_heading_mark_1.png)

![FC 安装方向](../../assets/qgc/setup/sensor/fc_orientation_1.png)

::: info
如果由于物理限制无法以推荐/默认方向安装，则需要通过软件配置实际使用的方向：[飞控方向配置](../config/flight_controller_orientation.md)。
:::

## 位置

飞控应尽可能靠近机架的重心安装。

如果无法在此位置安装，则需要[配置](../advanced_config/parameters.md)以下参数以设置相对于重心的偏移量：[EKF2_IMU_POS_X](../advanced_config/parameter_reference.md#EKF2_IMU_POS_X)，[EKF2_IMU_POS_Y](../advanced_config/parameter_reference.md#EKF2_IMU_POS_Y)，[EKF2_IMU_POS_Z](../advanced_config/parameter_reference.md#EKF2_IMU_POS_Z)（针对默认的EKF2估计算法）。

请注意，如果未设置这些偏移量，则EKF2的位置/速度估计将基于IMU位置而非重心位置。  
这可能导致不必要的振荡，具体取决于IMU距离重心的距离。

::: details 解释
要理解未设置这些偏移量的影响，可以考虑以下情况：当飞控（IMU）位于重心前方，以位置模式飞行时，如果产生围绕重心的前倾运动，高度估计值会下降，因为IMU实际上已经向下移动。作为响应，高度控制器会增加推力进行补偿。振幅取决于IMU距离重心的距离。这种影响可能是微小的，但会持续产生不必要的控制输出。如果指定了偏移量，纯俯仰运动不会引起高度估计值的任何变化，因此寄生修正会减少。
:::

## 减振

带有内置加速度计或陀螺仪的飞控板对振动敏感。  
某些板载有内置减振装置，而其他板则提供 _安装泡沫_ 用于将控制器与机体隔离。

![Pixhawk 安装泡沫](../../assets/hardware/mounting/3dr_anti_vibration_mounting_foam.png)  
_减振泡沫_

应使用飞控文档中推荐的安装策略。

:::tip
[通过飞行评审分析日志 > 振动](../log/flight_review.md#vibration) 说明如何测试振动水平是否可接受，[减振](../assembly/vibration_isolation.md) 建议了若干可能的解决方案（如果存在振动问题）。
:::