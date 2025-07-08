# 水平地平线校准

你可以使用 _Level Horizon Calibration_ 来补偿控制器方向的小偏差，并在 _QGroundControl_ 飞行视图中校平地平线（上蓝下绿）。

:::tip
仅当自动驾驶仪方向明显与指定方向不一致，或在非位置控制飞行模式下飞行时存在持续漂移时，才建议执行此校准步骤。
:::

## 执行校准

要校平地平线：

1. 启动 _QGroundControl_ 并连接机体。
1. 在顶部工具栏中选择 **Gear** 图标（机体设置），然后在侧边栏中选择 **Sensors**。
1. 点击 **Level Horizon** 按钮。
   ![水平地平线校准](../../assets/qgc/setup/sensor/sensor_level_horizon.png)
   ::: info
   你应该已经设置了 [Autopilot Orientation](../config/flight_controller_orientation.md)。如果没有设置，也可以在此处设置。
   :::
1. 将机体以水平飞行姿态放置在水平表面上：

   - 对于固定翼飞机，这是水平飞行时的姿态（固定翼飞机的机翼通常会略微上仰！）
   - 对于多旋翼，这是悬停姿态。

1. 点击 **OK** 开始校准过程。
1. 等待校准过程完成。

## 验证

当机体放置在水平表面上时，检查飞行视图中显示的人工地平线指示器是否位于中间。

## 更多信息

- [Advanced Orientation Tuning](../advanced_config/advanced_flight_controller_orientation_leveling.md)（仅限高级用户）。
- [QGroundControl User Guide > Sensors](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors_px4.html#level-horizon)
- [PX4 Setup Video "Gyroscope" - @1m14s](https://youtu.be/91VGmdSlbo4?t=1m14s) (Youtube)