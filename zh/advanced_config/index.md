# 高级配置

本主题列出了一些与特定机体无关的配置主题，适用于较少修改的功能或仅针对制造商/OEM的特定配置。

## 参数

- [查找/更新参数](../advanced_config/parameters.md)
- [完整参数参考](../advanced_config/parameter_reference.md)

## 功能配置

- [使用PX4导航滤波器(EKF2)](../advanced_config/tuning_the_ecl_ekf.md)
- [飞行终止配置](../advanced_config/flight_termination.md)
- [着陆检测器配置](../advanced_config/land_detector.md)
- [预上锁/上锁/解锁配置](../advanced_config/prearm_arm_disarm.md)

## OEM/出厂校准

- [IMU出厂校准](../advanced_config/imu_factory_calibration.md)
- [传感器热补偿](../advanced_config/sensor_thermal_calibration.md)
- [指南针供电补偿](../advanced_config/compass_power_compensation.md)
- [高级控制器姿态调整](../advanced_config/advanced_flight_controller_orientation_leveling.md)
- [静压积聚](../advanced_config/static_pressure_buildup.md)

## 串口/以太网配置

- [串口配置](../peripherals/serial_configuration.md)
- [MAVLink遥测(OSD/GCS)](../peripherals/mavlink_peripherals.md)
- [PX4以太网配置](../advanced_config/ethernet_setup.md)

## 其他

- [引导程序更新](../advanced_config/bootloader_update.md)
  - [通过USB更新FMUv6X-RT引导程序](../advanced_config/bootloader_update_v6xrt.md)

## 参见

- [标准配置](../config/index.md) - 设置大多数PX4机体所需的基本传感器/功能。
- [飞控外设](../peripherals/index.md) - 配置专用传感器、可选传感器、执行器等。
- 机体配置/调参：
  - [多旋翼机体配置/调参](../config_mc/index.md)
  - [直升机配置/调参](../config_heli/index.md)
  - [固定翼配置/调参](../config_fw/index.md)
  - [垂直起降(VTOL)配置/调参](../config_vtol/index.md)