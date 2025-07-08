# 标准配置

本节介绍大多数PX4机体所需的通用软件配置和校准。

你必须首先[加载固件并选择机体框架/类型](#firmware-vehicle-selection)。
其他大多数步骤可以按任意顺序执行，但[调参](#tuning)必须放在最后进行。

## 前置条件

开始前你应该[下载QGroundControl](http://qgroundcontrol.com/downloads/)并在你的**桌面**电脑上安装。
然后打开QGC应用程序菜单（左上角的"Q"图标），在_Select Tool_弹窗中选择**机体设置**：

![QGC主菜单弹窗: 高亮显示机体设置](../../assets/qgc/setup/menu_setup.png)

## 配置步骤

### 固件/机体选择

- [加载固件](../config/firmware.md)
- [机体(框架)选择](../config/airframe.md)

### 电机/执行器设置

- [电调校准](../advanced_config/esc_calibration.md)
- [执行器配置和测试](../config/actuators.md)

### 传感器校准

- [传感器方向](../config/flight_controller_orientation.md)
- [磁力计(指南针)](../config/compass.md)
- [陀螺仪](../config/gyroscope.md)
- [加速度计](../config/accelerometer.md)
- [水平面校准](../config/level_horizon_calibration.md)
- [空速](../config/airspeed.md) (仅固定翼/垂直起降)

::: info
这些及其他传感器的设置位于[传感器硬件与配置](../sensor/index.md)。
:::

### 手动控制设置

遥控器控制:

- [遥控器设置](../config/radio.md)
- [飞行模式配置](../config/flight_mode.md)

手柄/游戏手柄:

- [手柄设置](../config/joystick.md)

### 安全配置

- [电池估算调参](../config/battery.md) (需要[电源模块](../power_module/index.md))
- [安全配置(故障保护)](../config/safety.md)

### 调参

以下框架支持并推荐自动调参：

- [自动调参(多旋翼)](../config/autotune_mc.md) 
- [自动调参(固定翼)](../config/autotune_fw.md)
- [自动调参(垂直起降)](../config/autotune_vtol.md)

## 视频指南

以下视频展示了大部分校准过程（使用的是较旧版本的_QGroundControl_，但大部分流程仍然适用）。

<lite-youtube videoid="91VGmdSlbo4" title="PX4自动驾驶仪设置教程预览"/>

## 支持

如果需要配置帮助，可以在[QGroundControl支持论坛](https://discuss.px4.io//c/qgroundcontrol/qgroundcontrol-usage)提问。

## 参见

- [QGroundControl > 设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/setup_view.html)
- [飞控外设](../peripherals/index.md) - 设置特定传感器、可选传感器、执行器等。
- [高级配置](../advanced_config/index.md) - 工厂/OEM校准、高级功能配置、不常见配置。
- 机体中心配置/调参：

  - [多旋翼配置/调参](../config_mc/index.md)
  - [直升机配置/调参](../config_heli/index.md)
  - [固定翼配置/调参](../config_fw/index.md)
  - [垂直起降配置/调参](../config_vtol/index.md)