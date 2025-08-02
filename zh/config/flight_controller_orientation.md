# 飞行控制器/传感器方向

默认情况下，飞行控制器和外部指南针（如有）应安装在机体顶部朝上，且箭头指向机体前方。  
如果电路板或任何外部指南针安装在其他方向，则需要在固件中进行配置。

## 计算方向

飞行控制器的ROLL、PITCH和/或YAW偏移量是围绕机体前方（x轴）、右侧（y轴）、下方（z轴）进行计算的。

![Frame Heading](../../assets/concepts/frame_heading.png)

旋转过程中各旋转轴保持不变。  
因此旋转的坐标系始终固定。  
这被称为**外在旋转**。

![Vehicle orientation](../../assets/qgc/setup/sensor/fc_orientation_1.png)

例如，下图所示的机体仅围绕z轴（即仅偏航）进行旋转，对应的旋转方向为：`ROTATION_NONE`、`ROTATION_YAW_90`、`ROTATION_YAW_180`、`ROTATION_YAW_270`。

![Yaw Rotation](../../assets/qgc/setup/sensor/yaw_rotation.png)

::: info
对于VTOL尾座式机体，所有传感器校准应根据其多旋翼配置（即起飞、悬停、着陆时的机体方向）设置方向。  
通常坐标轴相对于机体在稳定前飞时的方向。  
更多信息请参见[基础概念](../getting_started/px4_basic_concepts.md#heading-and-directions)。
:::

## 设置飞行控制器方向

设置方向的步骤如下：

1. 启动_QGroundControl_ 并连接机体。  
1. 选择 **"Q"图标 > 机体设置 > 传感器**（侧边栏）打开_传感器设置_。  
1. 选择 **方向设置** 按钮。  

   ![Set sensor orientations](../../assets/qgc/setup/sensor/sensor_orientation_set_orientations.jpg)

1. 选择 **飞行控制器方向**（按照[上述方法](#计算方向)计算的方向）。  

   ![Orientation options](../../assets/qgc/setup/sensor/sensor_orientation_selector_values.jpg)

1. 点击 **确定**。  

::: info
可以通过[水平地平线校准](../config/level_horizon_calibration.md)补偿控制器方向的小偏差，并在飞行视图中校准地平线。
:::

## 设置指南针方向

PX4会自动检测指南针方向作为[指南针校准](../config/compass.md)的一部分（[默认启用](../advanced_config/parameter_reference.md#SENS_MAG_AUTOROT)），适用于任何[标准MAVLink方向](https://mavlink.io/en/messages/common.html#MAV_SENSOR_ORIENTATION)（顶部朝上且前方对准，或任意轴的45°倍数偏移）。

::: info
可以通过查看[CAL_MAGn_ROT](../advanced_config/parameter_reference.md#CAL_MAG0_ROT)参数确认自动检测是否成功。
:::

如果使用了非标准方向，则需要为每个指南针设置[CAL_MAGx_ROLL](../advanced_config/parameter_reference.md#CAL_MAG0_ROLL)、[CAL_MAGx_PITCH](../advanced_config/parameter_reference.md#CAL_MAG0_PITCH)和[CAL_MAGx_YAW](../advanced_config/parameter_reference.md#CAL_MAG0_YAW)参数，填入实际使用的角度。

这将自动将[CAL_MAGn_ROT](../advanced_config/parameter_reference.md#CAL_MAG0_ROT)设置为"自定义欧拉角"，并禁用所选指南针的自动校准（即使[SENS_MAG_AUTOROT](../advanced_config/parameter_reference.md#SENS_MAG_AUTOROT)已启用）。

## 进一步信息

- [高级方向调节](../advanced_config/advanced_flight_controller_orientation_leveling.md)（仅限高级用户）。
- [QGroundControl用户指南 > 传感器](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors_px4.html#flight_controller_orientation)