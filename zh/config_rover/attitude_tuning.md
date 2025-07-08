# 姿态调校

姿态调校是使用 [Stabilized mode](../flight_modes_rover/manual.md#stabilized-mode) 及后续所有模式的必要步骤。

::: warning
此步骤前必须已完成 [rate tuning](rate_tuning.md)！
:::

在 QGroundControl 中配置以下 [参数](../advanced_config/parameters.md)：

1. [RO_YAW_P](#RO_YAW_P) [-]: 偏航闭环控制器的比例增益

   ::: tip
   在稳定模式下，闭环偏航控制仅在直线行驶（无偏航率输入）时激活。  
   调校时建议 [RO_YAW_P](#RO_YAW_P) 初始值设为1。  
   将车辆切换至稳定模式，推动遥控器左摇杆向前行驶。  
   解除车辆武装后，从飞行日志中绘制 [RoverAttitudeStatus](../msg_docs/RoverAttitudeStatus.md) 消息中的 `measured_yaw` 和 `adjusted_yaw_setpoint` 曲线进行对比。  
   调整参数值直至满意轨迹跟踪效果。  
   若观察到偏航设定值存在稳态误差，则需增加角速度控制器的积分项：[RO_YAW_RATE_I](../advanced_config/parameter_reference.md#RO_YAW_RATE_I) 
   :::

车辆现已准备好在 [Stabilized mode](../flight_modes_rover/manual.md#stabilized-mode) 中行驶，可继续进行 [velocity tuning](velocity_tuning.md) 的配置。

## 姿态控制器结构（仅信息用）

本节为开发者及控制理论经验者提供额外信息。

姿态控制器采用如下结构：

![Rover Attitude Controller](../../assets/config/rover/rover_attitude_controller.png)

角速度和姿态控制器为串级结构，因此只需一个积分项即可消除稳态误差。  
我们将积分项设置在角速度控制器中，因为其可独立于姿态控制器运行。

## 参数概览

| 参数名称                                                                           | 描述                          | 单位 |
| ----------------------------------------------------------------------------------- | ------------------------------------ | ---- |
| <a id="RO_YAW_P"></a>[RO_YAW_P](../advanced_config/parameter_reference.md#RO_YAW_P) | 偏航控制器的比例增益 | -    |