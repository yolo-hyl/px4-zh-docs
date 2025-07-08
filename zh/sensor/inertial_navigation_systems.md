# IMU、AHRS和INS

PX4通常运行在包含IMU的飞控器上（如Pixhawk系列），并在EKF2估计器中融合传感器数据和GNSS信息，以确定机体姿态、航向、位置和速度。

然而，PX4也可以使用某些INS设备作为原始数据源或外部估计器，替代EKF。

以下系统可以以这种方式使用：

- [VectorNav](../sensor/vectornav.md)：IMU/AHRS、GNSS/INS、双GNSS/INS系统，可用作外部INS或原始传感器数据源。

## 术语表

### 惯性测量单元（IMU）

IMU是一种包含三轴加速度计（运动传感器）、三轴陀螺仪（旋转传感器）以及有时包含磁力计（指南针）的设备。陀螺仪和加速度计提供原始传感器数据，用于测量系统的角速度和加速度。若存在磁力计，其提供的传感器数据可用于确定机体的航向。

### 姿态航向参考系统（AHRS）

包含IMU和处理系统的AHRS系统，能够从IMU的原始数据中提供姿态和航向信息。

### 惯性导航系统（INS）

INS是一种导航设备，通过加速度计、陀螺仪、可能的磁力计和计算机，无需外部参考即可计算运动物体的姿态、位置和速度。本质上，它是一个包含位置/速度估计的AHRS。

## 更多信息

- [什么是惯性导航系统？](https://www.vectornav.com/resources/inertial-navigation-articles/what-is-an-ins) (VectorNav)
- [惯性导航入门](https://www.vectornav.com/resources/inertial-navigation-primer) (VectorNav)