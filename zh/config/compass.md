# 罗盘校准

该过程用于校准并配置所有[magnetometers](../gps_compass/index.md)。  
_QGroundControl_ 将引导你将机体置于多个设定方向，并围绕指定轴旋转机体。

::: info
它还会自动检测外部磁强计的朝向([默认启用](../advanced_config/parameter_reference.md#SENS_MAG_AUTOROT))。  
如果你已[安装罗盘](../assembly/mount_gps_compass.md#compass-orientation)在非标准角度，需要在校准前[手动设置罗盘朝向](../config/flight_controller_orientation.md#setting-the-compass-orientation)。
:::

## 概述

在首次设置机体时，您需要校准指南针；如果机体曾暴露于强磁场环境，或在磁特性异常的区域使用，可能需要重新校准。

:::tip
指南针校准不良的表现包括多旋翼在悬停时持续转圈、"马桶效应"（以递增半径向外旋转，通常保持恒定高度，最终飞离航线），或在尝试直线飞行时偏离航线。
_QGroundControl_ 也会提示错误 `mag sensors inconsistent`。
:::

该流程会校准所有指南针并自动检测外部指南针的安装方向。如果有外部磁力计可用，该流程会禁用内置磁力计（这些磁力计主要用于自动检测外部磁力计的旋转方向）。

目前提供以下几种指南针校准类型：

1. [完整校准](#完整校准): 该校准在首次将飞控安装到机体或机体配置发生显著变化后必须执行。
   通过估算每个轴的偏移量和缩放系数来补偿硬铁和软铁效应。
1. [快速校准](#partial-quick-calibration): 该校准可在每次飞行准备时执行，更换载荷后，或当航向罗盘显示不准确时进行。
   该类型校准仅估算偏移量以补偿硬铁效应。
1. [大型机体校准](#大型机体校准): 当机体过大或过重无法执行完整校准时可执行该校准。该类型校准仅估算偏移量以补偿硬铁效应。

## 执行校准

### 预条件

开始校准前：

1. 选择远离大型金属物体或磁场的区域。
   :::tip
   金属并非总是显而易见！避免在办公桌（通常含有金属支架）或车辆旁边校准。
   即使站在钢筋分布不均的混凝土板上也可能影响校准。
   :::
1. 尽可能通过遥测无线电而非USB连接。
   USB可能引起显著磁干扰。
1. 如果使用外部指南针（或组合GPS/指南针模块），确保其[安装](../assembly/mount_gps_compass.md)尽可能远离其他电子设备以减少磁干扰，并采用_支持的朝向_。

### 完整校准

校准步骤如下：

1. 启动 _QGroundControl_ 并连接机体。
1. 选择 **"Q"图标 > 机体设置 > 传感器** (侧边栏) 以打开 _传感器设置_。
1. 点击 **指南针** 传感器按钮。

   ![选择PX4的指南针校准](../../assets/qgc/setup/sensor/sensor_compass_select_px4.png)

   ::: info
   您应已设置[飞控器朝向](../config/flight_controller_orientation.md)。如果没有，也可以在此处设置。
   :::

1. 点击 **OK** 开始校准。
1. 将机体放置在任一红色显示（未完成）的朝向上并保持静止。当提示时（朝向图像变为黄色），按指定轴在任一/双向旋转机体。当前朝向的校准完成后，屏幕上的对应图像会变为绿色。

   ![PX4的指南针校准步骤](../../assets/qgc/setup/sensor/sensor_compass_calibrate_px4.png)

1. 重复所有机体朝向的校准过程。

当机体在所有位置完成校准后，_QGroundControl_ 将显示 _校准完成_ （所有朝向图像变为绿色且进度条完全填充）。然后可以继续校准下一个传感器。

### 部分"快速"校准

此校准类似于智能手机上的8字形指南针校准：

1. 将机体举在身前，并随机在所有轴上进行部分旋转。
   每个方向约30度的2-3次摆动通常足够。
1. 等待航向估计稳定，并验证指南针花是否指向正确方向（这可能需要几秒钟）。

注意事项：

- 此类校准没有开始/停止（算法在机体解除武装时持续运行）。
- 校准立即应用于数据（无需重启），但仅在解除武装后保存至校准参数中（若校准后未执行武装/解除武装序列，校准数据将丢失）。
- 第1步中部分旋转的幅度和速度可能影响校准质量。
  遵循上述建议通常足够。

### 大型机体校准

<Badge type="tip" text="PX4 v1.15" />

此校准流程利用机体朝向和位置的外部知识，以及世界磁模型 (WMM) 来校准硬铁偏差。

1. 确保GNSS定位。这是在WMM表中查找预期地磁场所必需的。
2. 将机体对准真北。
   尽可能精确以获得最佳效果。
3. 打开[QGroundControl MAVLink控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)并发送以下命令：

   ```sh
   commander calibrate mag quick
   ```

注意事项：

- 此方法专为无法或难以进行完整旋转的机体设计。
  如果可以完整旋转，请改用[完整校准](#完整校准)。
- 机体不需要完全水平，因为会使用倾斜估计自动补偿。
- 也可以通过MAVLink命令[MAV_CMD_FIXED_MAG_CAL_YAW](https://mavlink.io/en/messages/common.html#MAV_CMD_FIXED_MAG_CAL_YAW)触发此校准。

## 验证

校准完成后，请检查航向指示器和地图上箭头的航向是否稳定，并与机体转动时的方向一致（例如转向正方向）。

## 额外校准/配置

上述流程将自动检测、[设置默认旋转](../advanced_config/parameter_reference.md#SENS_MAG_AUTOROT)、校准并优先处理所有可用磁力计。
如果有外部磁力计可用，内部磁力计将被禁用。

通常无需进一步配置指南针。

### 启用/禁用指南针

虽然无需进一步配置，但开发者出于测试等任何原因，可以通过指南针参数禁用/启用指南针。
这些参数以 [CAL*MAGx*](../advanced_config/parameter_reference.md#CAL_MAG0_ID)（其中 `x=0-3`）为前缀：

- [CAL_MAGn_ROT](../advanced_config/parameter_reference.md#CAL_MAG0_ROT) 可用于确定哪些指南针为内部设备。
  当 `CAL_MAGn_ROT==1` 时，表示该指南针为内部设备。
- [CAL_MAGx_PRIO](../advanced_config/parameter_reference.md#CAL_MAG0_PRIO) 用于设置指南针的相对优先级，并可用于禁用某个指南针。

## 调试

通过设置 [SDLOG_MODE=1](../advanced_config/parameter_reference.md#SDLOG_MODE) 和 [SDLOG_PROFILE=64](../advanced_config/parameter_reference.md#SDLOG_PROFILE)，可以记录磁强计（实际上包括所有传感器）的原始比较数据。  
更多信息请参见 [日志记录](../dev_log/logging.md)。

## 更多信息

- [外设 > GPS 与指南针](../gps_compass/index.md)
- [基本组装](../assembly/index.md) (各飞控的设置指南)
- [指南针电源补偿](../advanced_config/compass_power_compensation.md) (高级配置)
- [QGroundControl 用户指南 > 传感器](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors_px4.html#compass)
- [PX4 设置视频 - @2m38s](https://youtu.be/91VGmdSlbo4?t=2m38s) (Youtube)