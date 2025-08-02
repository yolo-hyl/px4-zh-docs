# 添加机体配置

PX4 [机体配置文件](#配置文件概述) 是 shell 脚本，用于设置特定机体（如四旋翼无人机、地面车辆或船只）所需的参数、控制器和应用程序（部分或全部）。
这些脚本在 _QGroundControl_ 中选择并应用对应 [机体](../config/airframe.md) 时执行。

编译到 NuttX 目标固件中的配置文件位于 [ROMFS/px4fmu_common/init.d](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d) 文件夹（POSIX 模拟器的配置文件存储在 [ROMFS/px4fmu_common/init.d-posix](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d-posix/airframes)）。
该文件夹包含特定机体的完整配置，以及不同机体类型的部分"通用配置"。
通用配置通常用作创建新配置文件的起点。

此外，机体配置文件也可以从 SD 卡加载。

::: info
你还可以通过 SD 卡上的文本文件对当前机体配置进行"调整"。
详情请参见 [系统启动 > 定制系统启动](../concept/system_startup.md#customizing-the-system-startup) 页面。
:::

::: info
确定配置文件中需要设置哪些参数/值时，可以先分配一个通用机体并调整机体，然后使用 [`param show-for-airframe`](../modules/modules_command.md#param) 列出已更改的参数。
:::

## 开发框架配置

开发新框架配置的推荐流程如下：

1. 首先在QGC中为目标机体类型选择一个合适的"通用配置"，例如_Generic Quadcopter_。
1. 配置[几何结构和执行器输出](../config/actuators.md)。
1. 执行其他[基本配置](../config/index.md)。
1. 调整机体参数。
1. 运行[`param show-for-airframe`](../modules/modules_command.md#param)控制台命令，列出与原始通用机型相比的参数差异。

获取参数后，可以通过复制通用配置的配置文件并附加新参数来创建新的框架配置文件。

或者，可以将修改后的参数附加到[系统启动 > 自定义系统启动](../concept/system_startup.md#customizing-the-system-startup)中描述的启动配置文件中（即"调整通用配置"）。

## 如何向固件中添加配置

要向固件中添加机型配置:

1. 在 [init.d/airframes](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d/airframes) 文件夹中创建新的配置文件。
   - 为其指定一个简短的描述性文件名，并以未使用的自动启动ID作为前缀（例如，`1033092_superfast_vtol`）。
   - 使用配置参数和应用程序更新文件（见上文章节）。
1. 将新机型配置文件的名称添加到 [CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/airframes/CMakeLists.txt) 中对应机体类型的相应章节
1. [构建并上传](../dev_setup/building_px4.md) 软件。

## 如何将配置添加到SD卡

从SD卡启动的机体配置文件与存储在固件中的配置文件相同。

要让PX4使用机体配置启动，请将配置文件重命名为`rc.autostart`并复制到SD卡的`/ext_autostart/rc.autostart`路径下。
PX4会自动识别固件中的所有链接文件。

## 配置文件概述

配置文件包含以下几个主要模块：

- 文档（用于[Airframes Reference](../airframes/airframe_reference.md)和_QGroundControl_）
  机体系专用参数设置
  - 使用[控制分配](../concept/control_allocation.md)参数的配置和几何定义
  - [调参增益](#增益调整)
- 应该启动的控制器和应用程序，例如多旋翼或固定翼控制器、着陆检测器等

这些模块在很大程度上是相互独立的，这意味着许多配置文件会共享相同的机体物理布局，启动相同的应用程序，差异主要体现在调参增益上。

::: info
新框架配置文件只有在执行过干净编译（运行 `make clean`）后才会被自动添加到构建系统中。
:::

### 示例 - 通用四旋翼框架配置

一个通用Quad X框架的配置文件如下所示（[原始文件地址](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/airframes/4001_quad_x)）。该配置非常简单，因为它仅定义了所有四旋翼共有的最小配置。

第一行为shebang行，用于告知PX4运行的NuttX操作系统该配置文件是一个可执行的shell脚本。

```c
#!/bin/sh
```

接下来是框架文档部分。
`@name`、`@type`和`@class`用于在[API Reference](../airframes/airframe_reference.md#copter_quadrotor_x_generic_quadcopter)和QGroundControl机架选择中识别和分组该框架。

```plain
# @name Generic Quadcopter
#
# @type Quadrotor x
# @class Copter
#
# @maintainer Lorenz Meier <lorenz@px4.io>
#
```

下一行导入适用于指定类型所有机体的通用参数（参见 [init.d/rc.mc_defaults](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/rc.mc_defaults)）。

```plain
. ${R}etc/init.d/rc.mc_defaults
```

最后该文件列出了控制分配参数（以 `CA_` 开头，定义了框架的默认几何参数）。
这些参数可根据您的框架几何结构在 [执行器配置](../config/actuators.md) 中修改，也可以添加输出映射。

```sh
param set-default CA_ROTOR_COUNT 4
param set-default CA_ROTOR0_PX 0.15
param set-default CA_ROTOR0_PY 0.15
param set-default CA_ROTOR1_PX -0.15
param set-default CA_ROTOR1_PY -0.15
param set-default CA_ROTOR2_PX 0.15
param set-default CA_ROTOR2_PY -0.15
param set-default CA_ROTOR2_KM -0.05
param set-default CA_ROTOR3_PX -0.15
param set-default CA_ROTOR3_PY 0.15
param set-default CA_ROTOR3_KM -0.05
```

### 示例 - Babyshark VTOL 完整机体

以下是完整机体更复杂配置文件的示例。
这是 Baby Shark [标准垂直起降](../frames_vtol/standardvtol.md) 的配置文件（[原始文件在此](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/airframes/13014_vtol_babyshark)）。

shebang 和文档部分与通用框架类似，但此处我们还会记录每个电机和执行器的 `outputs` 映射关系。
请注意这些输出仅用于文档说明；实际映射是通过参数完成的。

```sh
#!/bin/sh
#
# @name BabyShark VTOL
#
# @type Standard VTOL
# @class VTOL
#
# @maintainer Silvan Fuhrer <silvan@auterion.com>
#
# @output Motor1 motor 1
# @output Motor2 motor 2
# @output Motor3 motor 3
# @output Motor4 motor 4
# @output Motor5 Pusher motor
# @output Servo1 Ailerons
# @output Servo2 A-tail left
# @output Servo3 A-tail right
#
# @board px4_fmu-v2 exclude
# @board bitcraze_crazyflie exclude
# @board holybro_kakutef7 exclude
#
```

与通用机架相同，我们接着包含通用垂直起降(VTOL)默认值。

```sh
. ${R}etc/init.d/rc.vtol_defaults
```

然后我们定义配置参数和[调节增益](#增益调整)：

```sh
param set-default MAV_TYPE 22

param set-default BAT1_N_CELLS 6

param set-default FW_AIRSPD_MAX 30
param set-default FW_AIRSPD_MIN 19
param set-default FW_AIRSPD_TRIM 23
param set-default FW_PN_R_SLEW_MAX 40
param set-default FW_PSP_OFF 3
param set-default FW_P_LIM_MAX 18
param set-default FW_P_LIM_MIN -25
param set-default FW_RLL_TO_YAW_FF 0.1
param set-default FW_RR_P 0.08
param set-default FW_R_LIM 45
param set-default FW_R_RMAX 50
param set-default FW_THR_TRIM 0.65
param set-default FW_THR_MIN 0.3
param set-default FW_THR_SLEW_MAX 0.6
param set-default FW_T_HRATE_FF 0
param set-default FW_T_SINK_MAX 15
param set-default FW_T_SINK_MIN 3
param set-default FW_YR_P 0.15

param set-default IMU_DGYRO_CUTOFF 15
param set-default MC_PITCHRATE_MAX 60
param set-default MC_ROLLRATE_MAX 60
param set-default MC_YAWRATE_I 0.15
param set-default MC_YAWRATE_MAX 40
param set-default MC_YAWRATE_P 0.3

param set-default MPC_ACC_DOWN_MAX 2
param set-default MPC_ACC_HOR_MAX 2
param set-default MPC_ACC_UP_MAX 3
param set-default MC_AIRMODE 1
param set-default MPC_JERK_AUTO 4
param set-default MPC_LAND_SPEED 1
param set-default MPC_MAN_TILT_MAX 25
param set-default MPC_MAN_Y_MAX 40
param set-default COM_SPOOLUP_TIME 1.5
param set-default MPC_THR_HOVER 0.45
param set-default MPC_TILTMAX_AIR 25
param set-default MPC_TKO_RAMP_T 1.8
param set-default MPC_TKO_SPEED 1
param set-default MPC_VEL_MANUAL 3
param set-default MPC_XY_CRUISE 3
param set-default MPC_XY_VEL_MAX 3.5
param set-default MPC_YAWRAUTO_MAX 40
param set-default MPC_Z_VEL_MAX_UP 2

param set-default NAV_ACC_RAD 3

param set-default SENS_BOARD_ROT 4

param set-default VT_ARSP_BLEND 10
param set-default VT_ARSP_TRANS 21
param set-default VT_B_DEC_MSS 1.5
param set-default VT_B_TRANS_DUR 12
param set-default VT_ELEV_MC_LOCK 0
param set-default VT_FWD_THRUST_SC 1.2
param set-default VT_F_TR_OL_TM 8
param set-default VT_PSHER_SLEW 0.5
param set-default VT_TRANS_MIN_TM 4
param set-default VT_TYPE 2
```

最后，该文件定义了几何形状的控制分配参数以及设置哪些输出对应不同电机和舵机的参数。

```sh
param set-default CA_AIRFRAME 2
param set-default CA_ROTOR_COUNT 5
param set-default CA_ROTOR0_PX 1
param set-default CA_ROTOR0_PY 1
param set-default CA_ROTOR1_PX -1
param set-default CA_ROTOR1_PY -1
param set-default CA_ROTOR2_PX 1
param set-default CA_ROTOR2_PY -1
param set-default CA_ROTOR2_KM -0.05
param set-default CA_ROTOR3_PX -1
param set-default CA_ROTOR3_PY 1
param set-default CA_ROTOR3_KM -0.05
param set-default CA_ROTOR4_AX 1.0
param set-default CA_ROTOR4_AZ 0.0

param set-default CA_SV_CS_COUNT 3
param set-default CA_SV_CS0_TYPE 15
param set-default CA_SV_CS0_TRQ_R 1.0
param set-default CA_SV_CS1_TRQ_P 0.5000
param set-default CA_SV_CS1_TRQ_R 0.0000
param set-default CA_SV_CS1_TRQ_Y -0.5000
param set-default CA_SV_CS1_TYPE 13
param set-default CA_SV_CS2_TRQ_P 0.5000
param set-default CA_SV_CS2_TRQ_Y 0.5000
param set-default CA_SV_CS2_TYPE 14

param set-default PWM_MAIN_FUNC1 201
param set-default PWM_MAIN_FUNC2 202
param set-default PWM_MAIN_FUNC3 105
param set-default PWM_MAIN_FUNC4 203
param set-default PWM_MAIN_FUNC5 101
param set-default PWM_MAIN_FUNC6 102
param set-default PWM_MAIN_FUNC7 103
param set-default PWM_MAIN_FUNC8 104

param set-default PWM_MAIN_TIM0 50
param set-default PWM_MAIN_DIS1 1500
param set-default PWM_MAIN_DIS2 1500
param set-default PWM_MAIN_DIS3 1000
param set-default PWM_MAIN_DIS4 1500
```

## 添加新的机体组

"机体组"用于在[QGroundControl](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/airframe.html)和[机体参考文档](../airframes/airframe_reference.md)中对相似的机体进行分类选择。每个组都有一个名称，以及与之关联的svg图像，该图像显示了组内机体的通用几何结构、电机数量和电机旋转方向。

QGroundControl和文档源代码使用的机体元数据文件是通过构建命令 `make airframe_metadata` 从机体描述中生成的，该命令通过脚本执行。

对于属于现有组的新机体，只需在以下路径提供文档即可：
[ROMFS/px4fmu_common/init.d](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d) 中的机体描述。

如果机体属于**新组**，还需执行以下操作：

1. 将组的svg图像添加到用户指南文档中（若未提供图像则显示占位图像）：[assets/airframes/types](https://github.com/PX4/PX4-user_guide/tree/master/assets/airframes/types)
1. 在 [srcparser.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/px4airframes/srcparser.py) 的 `GetImageName()` 方法中添加新组名称与图像文件名的映射（遵循以下模式）：

   ```python
   def GetImageName(self):
       """
       获取参数组图像基础名称（无扩展名）
       """
       if (self.name == "Standard Plane"):
           return "Plane"
       elif (self.name == "Flying Wing"):
           return "FlyingWing"
        ...
    ...
       return "AirframeUnknown"
   ```

1. 更新 _QGroundControl_:

   - 将组的svg图像添加到：[src/AutopilotPlugins/Common/images](https://github.com/mavlink/qgroundcontrol/tree/master/src/AutoPilotPlugins/Common/Images)
   - 按照以下模式在 [qgcimages.qrc](https://github.com/mavlink/qgroundcontrol/blob/master/qgcimages.qrc) 中添加对svg图像的引用：

     ```xml
     <qresource prefix="/qmlimages">
        ...
        <file alias="Airframe/AirframeSimulation">src/AutoPilotPlugins/Common/Images/AirframeSimulation.svg</file>
        <file alias="Airframe/AirframeUnknown">src/AutoPilotPlugins/Common/Images/AirframeUnknown.svg</file>
        <file alias="Airframe/Boat">src/AutoPilotPlugins/Common/Images/Boat.svg</file>
        <file alias="Airframe/FlyingWing">src/AutoPilotPlugins/Common/Images/FlyingWing.svg</file>
        ...
     ```

     ::: info
     其余的机体元数据应会在更新 **srcparser.py** 后自动包含在固件中。
     :::

## 增益调整

以下主题解释了如何调整将写入配置文件的参数：

- [自动调参 (多旋翼)](../config/autotune_mc.md)（或 [多旋翼PID调参指南](../config_mc/pid_tuning_guide_multicopter.md)）
- [自动调参 (固定翼)](../config/autotune_fw.md)（或 [固定翼PID调参指南](../config_fw/pid_tuning_guide_fixedwing.md)）
- [自动调参 (VTOL)](../config/autotune_vtol.md) ([VTOL配置](../config_vtol/index.md))

## 添加机体到 QGroundControl

要使新的机体在 _QGroundControl_ 中的 [机体配置](../config/airframe.md) 页面可用：

1. 进行干净的编译（例如运行 `make clean` 然后运行 `make px4_fmu-v5_default`）
1. 打开 QGC 并选择 **Custom firmware file...**，如下图所示：

![QGC flash custom firmware](../../assets/gcs/qgc_flash_custom_firmware.png)

系统会提示您选择要烧录的 **.px4** 固件文件（该文件是压缩的 JSON 文件，包含机体元数据）。

1. 进入构建文件夹并选择固件文件（例如 **PX4-Autopilot/build/px4_fmu-v5_default/px4_fmu-v5_default.px4**）。
1. 点击 **OK** 开始烧录固件。
1. 重新启动 _QGroundControl_。

新机体随后将在 _QGroundControl_ 中可用。