# 包裹投递任务

包裹投递任务允许用户通过[gripper](../peripherals/gripper.md)规划并执行货物投递。

::: info
此功能在PX4 v1.14中添加，仅支持Gripper。
未来将扩展对其他货物释放硬件的支持，包括绞盘。
:::

## 投递机制配置

包裹投递任务需要预先配置，才能进行任务规划和执行。

配置主要与硬件相关，因此每种投递硬件的配置信息请参考对应的设置页面：

- [Gripper > 包裹投递配置](../peripherals/gripper.md#package-delivery-configuration)

## 任务规划

包裹投递任务的规划方式与其他[航点任务](../flying/missions.md)类似，包含任务开始、起飞航点、路径航点，以及可能的返回航点。
唯一区别是，包裹投递任务必须包含一个航点指示包裹应在地面释放（`Land`）还是空中释放（`Waypoint`），随后需要另一个任务项来释放包裹（`Gripper Mechanism`）。

是否选择`Land`取决于包裹能否在飞行中安全释放，以及机体是否能在投放位置着陆。
由于夹爪无法安全降低包裹，多旋翼和垂直起降（VTOL）机体在使用夹爪时通常会选择着陆投放。

在投递设备[反馈完成](#package-release-feedback)后，机体将前往下一个航点。
注意，如果已着陆，投放后的下一个任务项应为另一个`Waypoint`或`Takeoff`任务项（[不能是`RETURN`](#rtl-waypoint-for-package-delivery-with-landing)）。

## 创建包裹投递任务

要创建使用Gripper的包裹投递任务：

1. 创建一个包含`Takeoff`任务项的常规任务，并添加必要的飞行路径航点。
1. 在地图上添加一个释放包裹的航点。

   - 若要在飞行中投放包裹，请为航点设置适当高度（并确保航点位置适合安全投放）。

   - 如果需要着陆投放，则需将`Waypoint`改为`Land`任务项。
     通过选择任务项标题，然后在弹出窗口中选择`Land`完成修改。

     ![Waypoint to Land mission item](../../assets/flying/package_delivery_land_waypoint.png)

1. 在地图上任意位置添加一个夹爪释放航点。
   要将其改为`Gripper Mechanism`，选择"Waypoint"标题后，在弹出窗口中将组更改为"Advanced"，然后选择`Gripper Mechanism`。

   ![Action waypoint](../../assets/flying/qgc_mission_gripper_mechanism_item_example.png)

1. 在编辑器中配置夹爪操作。

   ![Gripper action setting](../../assets/flying/qgc_mission_plan_gripper_action_setting.png)

   - 设置为"Release"以释放包裹。
   - 当前无需设置夹爪ID。

1. 为剩余路径添加额外航点。
   如果已着陆，请记住在`Gripper Mechanism`后添加航点，再添加`Return`任务项。

### 示例规划

#### 包裹空投任务

显示一个机体在飞行中释放包裹的任务规划。
初始任务项是航点，操作为`Gripper Release`（如任务项列表所示）

![Package drop mission example](../../assets/flying/package_drop_mission_example.png)

注意高度图显示预设航点为飞行中航点，右侧面板也显示相同。

#### 着陆并释放任务

显示一个机体通过着陆交付包裹的任务规划。

![Land and Release example](../../assets/flying/land_and_release_package_delivery_mission_example.png)

注意高度图显示了`Land`任务项。

### 注意事项

#### 带着陆的包裹投递任务的RTL航点

不要规划`LAND` > `GRIPPER` > `RETURN TO LAUNCH`这样的任务。

出于安全原因，当机体着陆时"Return To Launch"功能被禁用（[相关问题](https://github.com/PX4/PX4-Autopilot/pull/20044)）。
因此如果着陆后释放货物再设置RTL航点，机体将在着陆点保持静止。

#### 任务中夹爪的手动控制

夹爪可以在任何模式下通过[摇杆按钮手动控制](../peripherals/gripper.md#qgc-joystick-configuration)（如果已配置），包括任务执行期间。

但请注意，如果在包裹投递任务中夹爪正在打开时手动命令其关闭，夹爪将无法完成打开操作。
任务将在任务指令超时后（[MIS_COMMAND_TOUT](../advanced_config/parameter_reference.md#MIS_COMMAND_TOUT)）恢复，即使包裹尚未释放。

#### 任务中自动解除武装功能禁用

任务中默认禁用自动解除武装功能。
这意味着在包裹投递时着陆后，机体仍会保持武装状态（可能具有危险性！）

#### 包裹释放反馈

"包裹释放"任务项（如`GRIPPER`）完成后，任务将继续执行。

夹爪和其他投递设备通过传感器反馈或可配置的超时来指示完成。