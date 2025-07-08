# IMU/磁力计出厂校准

PX4 OEM制造商可执行IMU和磁力计的出厂校准，以将加速度计、陀螺仪和磁力计的校准值存储到持久化存储(通常为EEPROM)。
这确保了终端用户始终可以将机体配置和调参重置为安全飞行状态。

此过程将以下参数写入`/fs/mtd_caldata`: [CAL_ACC\*](../advanced_config/parameter_reference.md#CAL_ACC0_ID), [CAL_GYRO\*](../advanced_config/parameter_reference.md#CAL_GYRO0_ID), [CAL_MAG\*](../advanced_config/parameter_reference.md#CAL_MAG0_ID)。
这些数据将在参数被设置(或重置)为默认值时使用。

:::warning
此功能依赖于FMU具有专用EEPROM芯片或配套的IMU PCBA具有足够的存储空间。
PX4将数据存储到`/fs/mtd_caldata`，必要时会创建该文件。
:::

::: info
这些值无法存储在[框架配置](../dev_airframes/adding_a_new_frame.md)中，因为它们因设备而异(框架配置定义了适用于同类型所有机体的参数集，例如启用的传感器、[飞控旋转](../config/flight_controller_orientation.md)和PID调参)。
:::

## 执行出厂校准

1. 将参数[SYS_FAC_CAL_MODE](../advanced_config/parameter_reference.md#SYS_FAC_CAL_MODE)设置为1。
1. 执行所有IMU校准: [加速度计](../config/accelerometer.md#performing-the-calibration), [陀螺仪](../config/gyroscope.md#performing-the-calibration)和[磁力计](../config/compass.md#performing-the-calibration)。
1. 重启机体。
   这会将所有`CAL_ACC*`、`CAL_GYRO*`和`CAL_MAG*`参数写入`/fs/mtd_caldata`。
1. 将参数`SYS_FAC_CAL_MODE`重置为0(默认值)。

::: info
如果仅需对加速度计和陀螺仪进行出厂校准，可将[SYS_FAC_CAL_MODE](../advanced_config/parameter_reference.md#SYS_FAC_CAL_MODE)设置为2，此时将省略磁力计。
:::

后续用户校准将如常生效(出厂校准数据仅用于参数默认值)。

## 更多信息

- [QGroundControl用户指南 > 传感器](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors_px4.html)