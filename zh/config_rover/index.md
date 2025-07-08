# 越野车配置/调参

本主题提供设置越野车的逐步指南。

后续步骤将解锁具有更多自动驾驶仪支持和功能的[驱动模式](../flight_modes_rover/index.md)：

| 步骤 | 配置                                 | 解锁的PX4驱动模式                                                       |
| ---- | ------------------------------------ | ------------------------------------------------------------------------ |
| 1    | [基础设置](basic_setup.md)           | [全手动模式](../flight_modes_rover/manual.md#manual-mode)                |
| 2    | [角速度调参](rate_tuning.md)         | [手动特技模式](../flight_modes_rover/manual.md#acro-mode)                |
| 3    | [姿态调参](attitude_tuning.md)       | [手动稳定模式](../flight_modes_rover/manual.md#stabilized-mode)          |
| 4    | [速度调参](velocity_tuning.md)       | [手动定位模式](../flight_modes_rover/manual.md#manual-mode)              |
| 5    | [位置调参](position_tuning.md)       | [自动模式](../flight_modes_rover/auto.md)                                |

::: warning
只有在完成所有前置模式的配置后，驱动模式才能正常工作。
:::

## 烧录越野车固件

越野车需要使用自定义固件，需烧录到飞控中而非默认的PX4固件：

1. 首先从`main`分支为你的飞控构建越野车固件（没有发布版本，因此不能直接在QGroundControl中选择该固件）。

   使用`make`命令构建越野车固件时，将后缀`_default`替换为`_rover`。
   例如，为px4_fmu-v6x板构建越野车固件时，使用以下命令：

   ```sh
   make px4_fmu-v6x_rover
   ```

   ::: info
   你也可以通过在[板配置](../hardware/porting_guide_config.md)中添加以下行来启用默认固件中的模块（例如，对于fmu-v6x，可以将它们添加到 [`main/boards/px4/fmu-v6x/default.px4board`](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v6x/default.px4board)）：

   ```sh
   CONFIG_MODULES_ROVER_ACKERMANN=y
   CONFIG_MODULES_ROVER_DIFFERENTIAL=y
   CONFIG_MODULES_ROVER_MECANUM=y
   ```

   注意添加越野车模块可能导致FLASH溢出，此时需要禁用不打算使用的模块（如多旋翼或固定翼相关模块）。
   :::

2. 将刚刚构建的**自定义固件**加载到你的飞控中（参见[加载固件 > 安装PX4 Main、Beta或自定义固件](../config/firmware.md#installing-px4-main-beta-or-custom-firmware)）。