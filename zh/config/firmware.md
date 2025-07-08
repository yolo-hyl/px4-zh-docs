# 加载固件

_QGroundControl_ **桌面**版本可用于将PX4固件安装到[Pixhawk系列](../getting_started/flight_controller_selection.md)飞控板上。

:::warning
**在开始安装固件前**，必须将机体的所有USB连接**断开**（无论是直接连接还是通过遥测电台连接）。
机体**不得**由电池供电。
:::

## 安装稳定版PX4

通常建议使用最新_发布版_的PX4，以便获得错误修复和最新功能。

:::tip
这是默认安装的版本。
:::

安装PX4的步骤如下：

1. 启动_QGroundControl_ 并连接机体。
1. 选择 **"Q"图标 > 机体设置 > 固件**（侧边栏）打开_固件设置_。

   ![固件未连接](../../assets/qgc/setup/firmware/firmware_disconnected.png)

1. 通过USB直接将飞控连接到计算机。

   ::: info
   直接连接到计算机的带电USB端口（不要通过USB集线器连接）。
   :::

1. 选择 **PX4 Pro Stable Release vX.x.x** 选项，安装最新稳定版PX4 _（针对您的飞控自动检测）_。

   ![安装默认PX4](../../assets/qgc/setup/firmware/firmware_connected_default_px4.png)

1. 点击 **OK** 按钮开始更新。

   固件将依次执行多个升级步骤（下载新固件、擦除旧固件等）。
   每个步骤会显示在屏幕上，整体进度通过进度条显示。

   ![固件升级完成](../../assets/qgc/setup/firmware/firmware_upgrade_complete.png)

   固件加载完成后，设备/机体将重启并重新连接。

   :::tip
   如果_QGroundControl_ 安装了FMUv2目标（查看安装时的控制台信息），而您使用的是更新的板卡，可能需要[更新引导加载程序](#bootloader)以访问飞控上的全部内存。
   :::

接下来需要指定[机体构型](../config/airframe.md)（然后是传感器、遥控器等）。

<a id="custom"></a>

## 安装PX4 Main、Beta或自定义固件

要安装其他版本的PX4：

1. 按上述方式连接机体，并选择 **PX4 Pro Stable Release vX.x.x**。
   ![安装PX4版本](../../assets/qgc/setup/firmware/qgc_choose_firmware.png)
1. 勾选 **高级设置**，从下拉列表中选择版本：
   - **标准版本（稳定版）:** 默认版本（即无需使用高级设置即可安装！）
   - **测试版（beta）:** 测试/候选版本。
     仅在新版本准备发布时提供。
   - **开发版（master）:** PX4/PX4-Autopilot _main_分支的最新构建版本。
   - **自定义固件文件...:** 自定义固件文件（例如[本地构建的版本](../dev_setup/building_px4.md)）。
     如果选择此选项，下一步需要从文件系统中选择自定义固件。

固件更新将继续按照之前的流程进行。

<a id="bootloader"></a>

## 引导加载程序更新

Pixhawk硬件通常预装了合适的引导加载程序版本。

需要更新的情况包括安装FMUv2固件的新型Pixhawk板卡。
如果_QGroundControl_ 安装了FMUv2目标（查看安装时的控制台信息），而您使用的是更新的板卡，可能需要更新引导加载程序以访问飞控上的全部内存。

![FMUv2更新](../../assets/qgc/setup/firmware/bootloader_update.jpg)

可通过[引导加载程序更新 > FMUv2引导加载程序更新](../advanced_config/bootloader_update.md#fmuv2-bootloader-update)中的说明进行更新。

## 补充信息

- [QGroundControl用户指南 > 固件](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/firmware.html)。
- [PX4设置视频](https://youtu.be/91VGmdSlbo4) (YouTube)