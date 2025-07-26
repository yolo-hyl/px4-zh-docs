

# 硬件在环仿真（SIH）

<Badge type="tip" text="PX4 v1.9 (多旋翼)" /><Badge type="tip" text="PX4 v1.13 (多旋翼、垂直起降、固定翼)" />

:::warning
此模拟器为[社区支持和维护](../simulation/community_supported_simulators.md)。
可能与当前PX4版本兼容（已知在PX4 v1.14中可用）。

有关核心开发团队支持的环境和工具的信息，请参见 [工具链安装](../dev_setup/dev_env.md)。
:::

硬件在环仿真（SIH）是四旋翼、固定翼机体（飞机）和垂直起降尾座机体的[硬件在环仿真（HITL）](../simulation/hitl.md)的替代方案。

新PX4用户可以使用SIH来熟悉PX4及其不同的模式和功能，当然也可以通过模拟使用遥控器学习飞行机体，这是软件在环仿真（SITL）无法实现的。

## 概述

在SIH中，整个模拟运行在嵌入式硬件上：控制器、状态估计算法和模拟器。
桌面计算机仅用于显示虚拟机体。

![模拟器 MAVLink API](../../assets/diagrams/SIH_diagram.png)

### 兼容性

- SIH 兼容所有 PX4 支持的开发板，但基于 FMUv2 的开发板除外。
- 多旋翼四旋翼飞行器的 SIH 从 PX4 v1.9 开始支持。
- 固定翼飞机（FW）和垂直起降尾座式飞行器（VTOL）的 SIH 从 PX4 v1.13 开始支持。
- 作为 SITL（无硬件环境）的 SIH 从 PX4 v1.14 开始支持。
- 标准垂直起降（Standard VTOL）的 SIH 从 PX4 v1.16 开始支持。
- 多旋翼六旋翼 X 的 SIH 从 `main`（预计为 PX4 v1.17）开始支持。

### 优势

SIH相比HITL提供了以下优势：

- 通过避免与计算机的双向连接，它确保了同步时序。因此，用户无需使用高性能的台式电脑。
- 整个仿真过程保留在PX4环境中。熟悉PX4的开发者可以更轻松地将其自定义的数学模型整合到仿真器中。例如，他们可以修改气动模型，或调整传感器的噪声水平，甚至添加需要仿真的传感器。
- 代表机体的物理参数（如质量、惯性和最大推力）可轻松通过[SIH参数](../advanced_config/parameter_reference.md#simulation-in-hardware)进行修改。

## 要求

要运行SIH，你需要以下设备：

- [飞控](../flight_controller/index.md)，例如Pixhawk系列板。

  ::: info
  从PX4 v1.14开始，你可以[以SITL模式运行SIH](#sih-as-sitl-no-fc)，这种情况下不需要飞控。
  :::

- [手动控制器](../getting_started/px4_basic_concepts.md#manual-control)：可以是[无线电控制系统](../getting_started/rc_transmitter_receiver.md)或[操纵杆](../config/joystick.md)。
- 通过GCS操作机体的QGroundControl软件。
- 用于可视化虚拟机体的开发计算机（可选）。

## 检查SIH是否在固件中

SIH所需的模块默认被包含在大多数PX4固件中。
这些模块包括：[`pwm_out_sim`](../modules/modules_driver.md#pwm-out-sim)、[`sensor_baro_sim`](../modules/modules_system.md#sensor-baro-sim)、[`sensor_gps_sim`](../modules/modules_system.md#sensor-gps-sim) 和 [`sensor_mag_sim`](../modules/modules_system.md#sensor-mag-sim)。

要检查这些模块是否存在于你的飞控器中：

1. 启动QGroundControl。
2. 打开 **分析工具 > Mavlink控制台**。
3. 在控制台中输入以下命令：

   ```sh
   pwm_out_sim status
   ```

   ```sh
   sensor_baro_sim status
   ```

   ```sh
   sensor_gps_sim status
   ```

   ```sh
   sensor_mag_sim status
   ```

   ::: tip
   注意当在真实硬件上使用SIH时，你不需要通过对应参数([SENS_EN_GPSSIM](../advanced_config/parameter_reference.md#SENS_EN_GPSSIM)、[SENS_EN_BAROSIM](../advanced_config/parameter_reference.md#SENS_EN_BAROSIM)、[SENS_EN_MAGSIM](../advanced_config/parameter_reference.md#SENS_EN_MAGSIM))额外启用这些模块。
   :::

4. 如果返回了有效状态，则可以开始使用SIH。

如果上述任何返回值为 `nsh: MODULENAME: command not found`，则说明模块未安装。
在这种情况下，你需要将这些模块添加到你的板级配置中，然后重新构建并安装固件。

### 将 SIH 添加到固件中

将以下键添加到您的飞行控制器配置文件中，以包含所有必需的模块（例如 [boards/px4/fmu-v6x/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v6x/default.px4board)）。  
然后重新构建固件并将其烧录到开发板。

```text
CONFIG_MODULES_SIMULATION_SIMULATOR_SIH=y
```

::: details 这会做什么？

这会安装 [simulator_sih/Kconfig](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/simulator_sih/Kconfig) 中的依赖项。  
它等效于：

```text
CONFIG_MODULES_SIMULATION_PWM_OUT_SIM=y
CONFIG_MODULES_SIMULATION_SENSOR_BARO_SIM=y
CONFIG_MODULES_SIMULATION_SENSOR_GPS_SIM=y
CONFIG_MODULES_SIMULATION_SENSOR_MAG_SIM=y
```

:::

作为手动更新配置文件的替代方案，您可以使用以下命令启动图形配置工具，并在路径 **模块 > 模拟 > simulator_sih** 下交互式启用所需模块。  
例如，要更新 fmu-v6x 配置，可以使用：

```sh
make px4_fmu-v6x boardconfig
```

上传后，请检查所需模块是否已存在。

## 启动SIH

要设置/启动SIH：

1. 用USB线缆将飞控连接到桌面电脑。
2. 打开QGroundControl并等待飞控启动并连接。
3. 打开[机体设置 > 机身](../config/airframe.md)然后选择所需机身：
   - [SIH 四旋翼 X](../airframes/airframe_reference.md#copter_simulation_sih_quadcopter_x)
   - **SIH 六旋翼 X** (目前仅在SITL中提供机身，因此在飞控硬件上需要手动等效配置)。
   - [SIH 平面 AERT](../airframes/airframe_reference.md#plane_simulation_sih_plane_aert)
   - [SIH 尾座双旋翼](../airframes/airframe_reference.md#vtol_simulation_sih_tailsitter_duo)
   - [SIH 标准 VTOL 四旋翼](../airframes/airframe_reference.md#vtol_simulation_sih_standard_vtol_quadplane)

飞控将重新启动。
重新启动后会启动`sih`模块，并在地面控制站地图上显示机体。

:::warning
飞机需要在手动模式下全推力起飞。
此外，如果飞机坠毁，状态估计器可能会丢失定位。
:::

## 显示/可视化（可选）

SIH模拟的机体可以通过 [jMAVSim](../sim_jmavsim/index.md) 作为可视化工具进行显示。

::: tip
SIH不需要可视化工具——你可以通过QGroundControl连接并飞行机体，无需使用可视化工具。
:::

要显示模拟的机体：

1. 关闭 _QGroundControl_（如果已打开）。
1. 拔出并重新插入飞控（等待几秒让其启动）。
1. 通过终端调用脚本 **jmavsim_run.sh** 启动jMAVSim：

   ```sh
   ./Tools/simulation/jmavsim/jmavsim_run.sh -q -d /dev/ttyACM0 -b 2000000 -o
   ```

   其中标志位含义为：
   - `-q` 允许与 _QGroundControl_ 通信（可选）。
   - `-d` 在Linux上启动串口设备 `/dev/ttyACM0`。
     在macOS上这将是 `/dev/tty.usbmodem1`。
   - `-b` 设置串口波特率为 `2000000`。
   - `-o` 以 _仅显示_ 模式启动jMAVSim（即物理引擎关闭，jMAVSim仅实时显示SIH提供的轨迹）。
   - 添加标志位 `-a` 显示飞机或 `-t` 显示尾座式飞行器。
     如果未指定此标志位，默认将显示四旋翼飞行器。

1. 几秒后，可以重新打开 _QGroundControl_。

此时，系统可以解锁并飞行。
机体的运动可以在jMAVSim中观察到，也可在QGC的 _Fly_ 界面查看。

## SIH 作为 SITL（无飞控）

从 v1.14 开始，SIH 可以作为 SITL（软件在环）运行。  
这意味着模拟代码在笔记本电脑/计算机上执行，而非在飞控上，效果类似于 Gazebo 或 jMAVSim。在这种情况下，您无需飞控硬件。

要运行 SIH 作为 SITL：

1. 安装 [PX4 开发工具链](../dev_setup/dev_env.md)。
2. 针对每种机体类型运行相应的 make 命令（在 PX4-Autopilot 仓库根目录下）：
   - 四旋翼飞行器

     ```sh
     make px4_sitl sihsim_quadx
     ```

   - 六旋翼飞行器

     ```sh
     make px4_sitl sihsim_hex
     ```

   - 固定翼（飞机）

     ```sh
     make px4_sitl sihsim_airplane
     ```

   - XVert VTOL 尾座飞行器

     ```sh
     make px4_sitl sihsim_xvert
     ```

   - 标准 VTOL

     ```sh
     make px4_sitl sihsim_standard_vtol
     ```

### 修改仿真速度

SITL 允许以快于实时的速度运行仿真。
要以 10 倍于实时的速度运行飞机仿真，执行以下命令：

```sh
PX4_SIM_SPEED_FACTOR=10 make px4_sitl sihsim_airplane
```

在 SITL 模式下显示机体，可在另一个终端中输入以下命令：

```sh
./Tools/simulation/jmavsim/jmavsim_run.sh -p 19410 -u -q -o
```

- 添加 `-a` 标志可显示固定翼飞机或 `-t` 显示尾座飞机。
  如果不使用此标志，将默认显示四旋翼飞行器。

### 设置自定义起飞位置

在 SITL 中的 SIH 可以通过环境变量设置起飞位置。  
这将覆盖默认的起飞位置。

需要设置的变量为：`PX4_HOME_LAT`、`PX4_HOME_LON` 和 `PX4_HOME_ALT`。

例如：

```sh
export PX4_HOME_LAT=28.452386
export PX4_HOME_LON=-13.867138
export PX4_HOME_ALT=28.5
make px4_sitl sihsim_quadx
```

## 添加新机架

[添加新机架](../dev_airframes/adding_a_new_frame.md)以在SIH仿真中使用，与在其他使用场景中的操作基本相同。
您仍然需要配置机体类型和[几何参数](../config/actuators.md) (`CA_` 参数) 并为特定机体设置其他默认值。

::: 警告
并非所有机体都能通过SIH进行仿真——目前[支持的机体类型](../advanced_config/parameter_reference.md#SIH_VEHICLE_TYPE)只有四种，每种类型在 [`sih.cpp`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/simulator_sih/sih.cpp) 中都有相对固定的实现。
:::

SIH仿真机架的具体差异列在下方各小节中。

对于所有SIH变体：

- 需要设置所有[硬件在环仿真](../advanced_config/parameter_reference.md#simulation-in-hardware)参数（前缀为`SIH_`），以配置机体的物理模型。

  ::: 提示
  确保`SIH_*`参数和`CA_*`参数描述的是同一机体。
  注意其中某些参数是冗余的！
  :::

- `param set-default SYS_HITL 2` 用于在下次启动时启用SIH。

  ::: 信息
  这还会禁用来自真实传感器的输入。
  对于飞行控制器（FC）上的SIH（仅），它还会启用仿真GPS、气压计、磁力计和空速传感器。

  对于作为SITL的SIH，需要显式启用这些传感器，如下所示。
  :::

- `param set-default EKF2_GPS_DELAY 0` 以提高状态估计器性能（通常GPS测量存在延迟是不现实的，但在SIH中假设是准确的）。

对于FC上的SIH：

- 机架文件应放在 `ROMFS/px4fmu_common/init.d/airframes` 中，并遵循命名模板 `${ID}_${model_name}.hil`，其中 `ID` 是用于选择机架的 `SYS_AUTOSTART_ID`，`model_name` 是机架型号名称。
- 在 `ROMFS/px4fmu_common/init.d/airframes/CMakeLists.txt` 中添加型号名称以生成对应的构建目标。
- 执行器通过 `HIL_ACT_FUNC*` 参数配置（而非通常的 `PWM_MAIN_FUNC*` 参数）。
  这是为了避免在SIH中使用真实执行器输出。
  同样，用于反转单个执行器输出范围的位域是 `HIL_ACT_REV`，而非 `PWM_MAIN_REV`。

对于作为SITL的SIH（无FC）：

- 机架文件应放在 `ROMFS/px4fmu_common/init.d-posix/airframes` 中，并遵循命名模板 `${ID}_sihsim_${model_name}`，其中 `ID` 是用于选择机架的 `SYS_AUTOSTART_ID`，`model_name` 是机架型号名称。
- 在 `src/modules/simulation/simulator_sih/CMakeLists.txt` 中添加型号名称以生成对应的构建目标。
- 执行器通过 `PWM_MAIN_FUNC*` 和 `PWM_MAIN_REV` 按常规方式配置。
- 此外，设置：
  - `PX4_SIMULATOR=${PX4_SIMULATOR:=sihsim}`
  - `PX4_SIM_MODEL=${PX4_SIM_MODEL:=svtol}`（将 `svtol` 替换为机架型号名称）
- 使用以下参数启用仿真传感器（这仅在SIH作为SITL时需要）：
  - `param set-default SENS_EN_GPSSIM 1`
  - `param set-default SENS_EN_BAROSIM 1`
  - `param set-default SENS_EN_MAGSIM 1`
  - `param set-default SENS_EN_ARSPDSIM 1`（如果它是固定翼或VTOL机架且配备空速传感器）

具体示例可参考 [ROMFS/px4fmu_common/init.d-posix/airframes](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/airframes/) 中的 `_sihsim_` 机架（SIH作为SITL）和 [ROMFS/px4fmu_common/init.d/airframes](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d/airframes)（FC上的SIH）。

## 动态模型

不同机体的动态模型如下：

- 四旋翼飞行器：[PDF报告](https://github.com/PX4/PX4-user_guide/raw/main/assets/simulation/SIH_dynamic_model.pdf)。
- 六旋翼飞行器：与四旋翼飞行器类似，但采用对称的六旋翼X型执行器配置。
- 固定翼：基于博士学位论文《敏捷固定翼无人机的动力学建模》。Khan, Waqas，导师为Nahon, Meyer，麦吉尔大学，博士学位论文，2016年。
- 尾坐式飞行器：基于硕士学位论文《飞翼尾坐式无人机的建模与控制》。Chiappinelli, Romain，导师为Nahon, Meyer，麦吉尔大学，硕士学位论文，2018年。

## 视频

<lite-youtube videoid="PzIpSCRD8Jo" title="SIH FW demo"/>

## 致谢

SIH最初由Coriolis g Corporation开发。
飞机模型和尾座模型由Altitude R&D inc添加。
这两家公司均为加拿大公司：

- Coriolis g开发了基于被动耦合系统的新型垂直起降(VTOL)机体；
- [Altitude R&D](https://www.altitude-rd.com/) 专注于动力学、控制与实时仿真（现位于苏黎世）。

该模拟器在BSD许可证下免费发布。

<!-- original author: @romain-chiap -->