# 运动捕捉（MoCap）

运动捕捉（MoCap）是一种[计算机视觉](https://en.wikipedia.org/wiki/Computer_vision)技术，通过使用机体外部的定位机制，估计机体的3D姿态（位置和方向）。  
当GPS信号缺失（例如在室内环境）时，该技术常用于机体导航，并提供相对于局部坐标系的位置信息。

_MoCap_ 系统最常见的运动检测方式是红外相机，但也可以使用其他类型的相机、激光雷达或超宽带（Ultra Wideband, UWB）等技术。

::: info
_MoCap_ 在概念上与[视觉惯性里程计（VIO）](../computer_vision/visual_inertial_odometry.md)相似。  
主要区别在于VIO的视觉系统运行在机体上，并结合机体IMU提供速度信息。
:::

## MoCap 资源

关于MoCap的更多信息请参考：

- [使用视觉或运动捕捉系统进行位置估计](../ros/external_position_estimation.md)。 <!-- bring across info into user guide? -->
- [使用运动捕捉系统飞行（VICON, NOKOV, Optitrack）](../tutorials/motion-capture.md)。 <!-- bring across info into user guide? -->
- [使用PX4的导航滤波器（EKF2）> 外部视觉系统](../advanced_config/tuning_the_ecl_ekf.md#external-vision-system)