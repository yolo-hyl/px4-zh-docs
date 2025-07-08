# 加速度计校准

加速度计必须在首次使用时或飞控方向改变时进行校准。否则通常无需重新校准（除非在冬季，而飞控出厂时未进行[热校准](../advanced_config/sensor_thermal_calibration.md)）。

::: info
加速度计校准不良通常会被飞行前检查和禁止武装消息捕获（QGC警告通常会提示"加速度计偏置过高"和"一致性检查失败"）。
:::

:::tip
这与[指南针校准](../config/compass.md)类似，区别在于你需保持机体静止（而非旋转）于每个姿态中。
:::

## 执行校准

_QGroundControl_ 会引导你将机体放置并保持在多种姿态中（屏幕提示时再移动位置）。

校准步骤如下：

1. 启动 _QGroundControl_ 并连接机体。
1. 选择 **"Q"图标 > 机体设置 > 传感器**（侧边栏）打开 _传感器设置_。
1. 点击 **加速度计** 传感器按钮。

   ![加速度计校准](../../assets/qgc/setup/sensor/accelerometer.png)

   ::: info
   你应已设置[飞控方向](../config/flight_controller_orientation.md)。
   如果未设置，也可以在此处设置。
   :::

1. 点击 **OK** 开始校准。
1. 按照屏幕上的_图像_提示放置机体。当提示出现（姿态图像变为黄色）时保持机体静止。
   当当前姿态校准完成后，屏幕上对应图像将变为绿色。

   ::: info
   校准使用最小二乘法拟合算法，不需要你进行"完美"的90度姿态校准。
   只要每个轴在某个姿态中大致朝上和朝下，并且机体保持静止，具体姿态并不重要。
   :::

   ![加速度计校准](../../assets/qgc/setup/sensor/accelerometer_positions_px4.png)

1. 为所有机体姿态重复校准过程。

当机体在所有位置校准完成后，_QGroundControl_ 会显示 _校准完成_（所有姿态图像变为绿色且进度条完全填充）。
之后可以继续校准下一个传感器。

## 更多信息

- [QGroundControl 用户指南 > 传感器](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors_px4.html#accelerometer)
- [PX4 设置视频 - @1m46s](https://youtu.be/91VGmdSlbo4?t=1m46s) (Youtube)