# 安全点（Rally Points）

安全点是[Return Mode](../flight_modes/return.md)的替代返航目的地/着陆点。  
启用后，机体将选择以下最近的返航目的地：起始点、任务着陆模式或*安全点*。

![安全点](../../assets/qgc/plan/rally_point/rally_points.jpg)

## 创建/定义安全点

安全点在*QGroundControl*中创建（QGroundControl将其称为“Rally Points”）。

操作步骤概览：
1. 打开 **QGroundControl > Plan View**
1. 在*Plan Editor*（屏幕右侧）中选择 **Rally** 标签/按钮。
1. 在工具栏（屏幕左侧）中选择 **Rally Point** 按钮。
1. 在地图任意位置点击以添加rally/safety点。
   - *Plan Editor*会显示并允许编辑当前rally点（地图上显示为绿色 **R** ）。
   - 可以选择另一个rally点（地图上显示为更橙色/黄色 **R** ）以进行编辑。
1. 点击 **Upload Required** 按钮，将rally点（以及任何[任务](../flying/missions.md)和[地理围栏](../flying/geofence.md)）上传到机体。

:::tip
更完整的文档请参考*QGroundControl用户指南*：[Plan View - Rally Points](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/plan_view/plan_rally_points.html)。
:::

## 使用安全点

安全点默认未启用（存在多种不同的[Return Mode Types](../flight_modes/return.md#return_types)）。

启用安全点的步骤：
1. [使用QGroundControl参数编辑器](../advanced_config/parameters.md)设置参数：[RTL_TYPE=3](../advanced_config/parameter_reference.md#RTL_TYPE)。