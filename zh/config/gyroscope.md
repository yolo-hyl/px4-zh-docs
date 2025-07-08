# 陀螺仪校准

_QGroundControl_ 将引导你将机体放置在平坦的表面上并保持静止。

## 执行校准

校准步骤如下：

1. 启动 _QGroundControl_ 并连接机体。
1. 选择 **"Q"图标 > 机体设置 > 传感器** （侧边栏）打开 _传感器设置_。
1. 点击 **陀螺仪** 传感器按钮。

   ![选择陀螺仪校准 PX4](../../assets/qgc/setup/sensor/gyroscope_calibrate_px4.png)

1. 将机体放置在平面上并保持静止。
1. 点击 **确定** 开始校准。

   顶部的进度条显示进度：

   ![PX4 上的陀螺仪校准进度](../../assets/qgc/setup/sensor/gyroscope_calibrate_progress_px4.png)

1. 完成后，_QGroundControl_ 将显示进度条 _校准完成_
   ![PX4 上的陀螺仪校准完成](../../assets/qgc/setup/sensor/gyroscope_calibrate_complete_px4.png)

::: info
如果移动机体，_QGroundControl_ 将自动重新开始陀螺仪校准。
:::

## 更多信息

- [QGroundControl 用户指南 > 陀螺仪](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors_px4.html#gyroscope)