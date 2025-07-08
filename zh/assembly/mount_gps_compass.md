# 安装指南针（或GNSS/指南针）

指南针和GNSS/指南针模块应尽可能远离电机/电调电源线及其他电磁干扰源安装，并[朝向](#compass-orientation)竖直方向，方向标记指向机体的前方。  
您还应配置PX4以[设置接收器的位置](#position)相对于重心（CoG）的相对位置。

在多旋翼飞行器中，通常将指南针安装在支架上，而固定翼和VTOL机体则通常将其安装在机翼上。

## 指南针方向

理想情况下，指南针应竖直朝向，方向标记指向机体前方（默认方向），但如需调整，可按45°的倍数旋转（任一轴向），具体参考[标准MAVLink方向](https://mavlink.io/en/messages/common.html#MAV_SENSOR_ORIENTATION)（该标准与[飞控方向设置](../config/flight_controller_orientation.md#calculating-orientation)的框架约定一致）。

下图展示了Pixhawk 4飞控和指南针的航向标记。

![连接指南针/GPS到Pixhawk 4](../../assets/flight_controller/pixhawk4/pixhawk4_compass_gps.jpg)

PX4会在[指南针校准](../config/compass.md)过程中自动检测这些标准方向（[默认启用](../advanced_config/parameter_reference.md#SENS_MAG_AUTOROT)）。

指南针也可安装在任意"自定义欧拉角"方向，但此时需手动配置方向。  
更多信息请参见[Flight Controller/Sensor Orientation](../config/flight_controller_orientation.md#setting-the-compass-orientation)中的[Setting the Compass Orientation](../config/flight_controller_orientation.md#setting-the-compass-orientation)。

## 位置

为补偿接收器与重心之间的相对运动，您应[配置](../advanced_config/parameters.md)以下参数设置偏移量：[EKF2_GPS_POS_X](../advanced_config/parameter_reference.md#EKF2_GPS_POS_X)、[EKF2_GPS_POS_Y](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Y)和[EKF2_GPS_POS_Z](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Z)。

这是因为EKF估计的机体框架会收敛到GNSS模块的位置，并假设其位于重心处。如果GNSS模块显著偏离重心，机体绕重心的旋转会被误认为是高度变化，这在某些飞行模式（如位置模式）中会导致不必要的修正。

这一点在使用[RTK GNSS](../advanced/rtk_gps.md)时尤为重要，因为其具有厘米级精度。如果未设置偏移量，GNSS测量值通常会被EKF认为与当前估计值不一致而被拒绝。

::: details 解释
例如，若GNSS模块位于重心上方10cm处，而IMU位于重心处，1 rad/s的俯仰运动将产生10cm/s的GNSS速度测量值_即使重心并未移动_。  
如果GNSS接收器的速度精度为1cm/s，EKF可能会停止信任这些测量值，因为它们看似不一致（超出精度10倍）。  
如果定义了偏移量，EKF将使用陀螺仪数据修正测量值。
:::