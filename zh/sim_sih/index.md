# 硬件仿真 (SIH)

<Badge type="tip" text="PX4 v1.9 (MC)" /><Badge type="tip" text="PX4 v1.13 (MC, VTOL, FW)" />

:::warning
此仿真器为[社区支持和维护](../simulation/community_supported_simulators.md)。
其可能与当前PX4版本兼容也可能不兼容（已知在PX4 v1.14中可用）。

有关核心开发团队支持的环境和工具的信息，请参阅[工具链安装](../dev_setup/dev_env.md)。
:::

硬件仿真 (SIH) 是[硬件在环仿真 (HITL)](../simulation/hitl.md)的替代方案，适用于四旋翼飞行器、固定翼飞行器（飞机）和垂直起降尾座飞行器。

SIH 可供新的PX4用户熟悉PX4系统及其不同模式和功能，并可用于学习如何在仿真中使用遥控器飞行机体，这是SITL无法实现的。

## 概述

通过SIH（硬件在环仿真），整个仿真过程完全运行在嵌入式硬件上：包括控制器、状态估计算法和仿真器。
桌面电脑仅用于显示虚拟机体。

![Simulator MAVLink API](../../assets/diagrams/SIH_diagram.png)

### 兼容性

- SIH兼容所有PX4支持的开发板，除了基于FMUv2的。
- SIH对多旋翼四轴飞行器的支持从PX4 v1.9开始。
- SIH对固定翼（飞机）和尾座式垂直起降（VTOL）的支持从PX4 v1.13开始。
- SIH作为纯软件在环（SITL）仿真从PX4 v1.14开始支持。
- SIH对标准垂直起降（VTOL）的支持从PX4 v1.16开始。
- SIH对X型六旋翼飞行器的支持从`main`分支开始（预计在PX4 v1.17中加入）。

### 优势

SIH相比HITL（硬件在环测试）具有多项优势：

- 通过避免与计算机的双向连接，确保了同步时序。
  因此用户不需要性能强大的桌面电脑。
- 整个仿真过程保留在PX4环境中。
  熟悉PX4的开发者可以更轻松地将自定义数学模型集成到仿真器中。
  例如，他们可以修改气动模型、传感器噪声等级，甚至添加需要仿真的传感器。
- 代表机体的物理参数（如质量、惯性矩和最大推力）可以通过[SIH参数](../advanced_config/parameter_reference.md#simulation-in-hardware)轻松修改。

## 要求

要运行 SIH，您需要以下设备：

- [飞控](../flight_controller/index.md)，例如 Pixhawk 系列主板。

  ::: info
  从 PX4 v1.14 开始，您可以运行 [SIH "as SITL"](#sih-as-sitl-no-fc)，这种情况下无需飞控。
  :::

- [手动控制器](../getting_started/px4_basic_concepts.md#manual-control)：可以是 [遥控系统](../getting_started/rc_transmitter_receiver.md) 或 [操纵杆](../config/joystick.md)。
- 通过地面站软件进行飞行的 QGroundControl。
- 用于可视化虚拟机体的开发用计算机（可选）。

## 检查固件中是否包含SIH

SIH所需模块默认已集成在大多数PX4固件中。这些模块包括：  
[`pwm_out_sim`](../modules/modules_driver.md#pwm-out-sim), [`sensor_baro_sim`](../modules/modules_system.md#sensor-baro-sim), [`sensor_gps_sim`](../modules/modules_system.md#sensor-gps-sim) 和 [`sensor_mag_sim`](../modules/modules_system.md#sensor-mag-sim)。

要检查这些模块是否存在于飞控中：

1. 启动QGroundControl。
2. 打开 **Analyze Tools > Mavlink Console**。
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
   注意在真实硬件上使用SIH时，无需通过对应的参数([SENS_EN_GPSSIM](../advanced_config/parameter_reference.md#SENS_EN_GPSSIM), [SENS_EN_BAROSIM](../advanced_config/parameter_reference.md#SENS_EN_BAROSIM), [SENS_EN_MAGSIM](../advanced_config/parameter_reference.md#SENS_EN_MAGSIM))额外启用模块。
   :::

4. 如果返回有效状态信息，则可以开始使用SIH。

如果上述任一返回值为 `nsh: MODULENAME: command not found`，则说明模块未安装。此时需要将模块添加到板级配置中，然后重新构建并安装固件。

### 将SIH添加到固件中

向飞控配置文件添加以下配置项以包含所有所需模块（示例可参考 [boards/px4/fmu-v6x/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v6x/default.px4board)）。  
然后重新构建固件并烧录到开发板。

```text
CONFIG_MODULES_SIMULATION_SIMULATOR_SIH=y
```

::: details 这会执行什么操作？

这将安装 [simulator_sih/Kconfig](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/simulator_sih/Kconfig) 中的依赖。等效于：

```text
CONFIG_MODULES_SIMULATION_PWM_OUT_SIM=y
CONFIG_MODULES_SIMULATION_SENSOR_BARO_SIM=y
CONFIG_MODULES_SIMULATION_SENSOR_GPS_SIM=y
CONFIG_MODULES_SIMulation_SENSOR_MAG_SIM=y
```

:::

作为手动修改配置文件的替代方案，可以使用以下命令启动GUI配置工具，并在路径 **modules > Simulation > simulator_sih** 下交互式启用所需模块。例如，更新fmu-v6x配置时可使用：

```sh
make px4_fmu-v6x boardconfig
```

上传后，检查所需模块是否已存在。

## 启动 SIH

要设置/启动 SIH：

1. 用 USB 线将飞行控制器连接到桌面电脑。
2. 打开 QGroundControl 并等待飞行控制器启动并连接。
3. 打开 [机型设置 > 机架](../config/airframe.md) 然后选择目标机架：
   - [SIH 四旋翼飞行器 X](../airframes/airframe_reference.md#copter_simulation_sih_quadcopter_x)
   - **SIH 六旋翼飞行器 X**（目前仅提供 SITL 机架，为节省闪存空间，需在飞控硬件上手动等效配置）。
   - [SIH 平面飞行器 AERT](../airframes/airframe_reference.md#plane_simulation_sih_plane_aert)
   - [SIH 尾座双旋翼飞行器](../airframes/airframe_reference.md#vtol_simulation_sih_tailsitter_duo)
   - [SIH 标准 VTOL 复合翼](../airframes/airframe_reference.md#vtol_simulation_sih_standard_vtol_quadplane)

自动驾驶仪随后将重启。
重启后会启动 `sih` 模块，机体应在地面控制站地图上显示。

:::warning
飞机需要在手动模式下全油门起飞。
另外，如果飞机坠毁，状态估计器可能丢失定位。
:::

## 显示/可视化(可选)

SIH模拟的机体可通过 [jMAVSim](../sim_jmavsim/index.md) 进行可视化显示。

::: tip
SIH 并不 _需要_ 可视化工具 —— 您可以通过 QGroundControl 连接并操控机体，无需可视化工具。
:::

要显示模拟的机体：

1. 关闭 _QGroundControl_（如果已打开）。
1. 拔出并重新插上飞控（等待几秒使其启动）。
1. 通过终端调用脚本 **jmavsim_run.sh** 启动 jMAVSim：

   ```sh
   ./Tools/simulation/jmavsim/jmavsim_run.sh -q -d /dev/ttyACM0 -b 2000000 -o
   ```

   其中标志位含义为：
   - `-q` 允许与 _QGroundControl_ 通信（可选）。
   - `-d` 在 Linux 上启动串口设备 `/dev/ttyACM0`。
     在 macOS 上应为 `/dev/tty.usbmodem1`。
   - `-b` 设置串口波特率为 `2000000`。
   - `-o` 以 _仅显示_ 模式启动 jMAVSim（即物理引擎关闭，jMAVSim 仅实时显示 SIH 提供的轨迹）。
   - 添加标志位 `-a` 以显示固定翼或 `-t` 以显示尾座机。
     如果未添加该标志位，默认显示四旋翼。

1. 几秒后，可重新打开 _QGroundControl_。

此时，系统可以解锁并飞行。
机体的移动可在 jMAVSim 和 QGC 的 _Fly_ 界面中观察到。

## SIH 作为 SITL（无飞控）

从 v1.14 开始，SIH 可以作为 SITL（Software-In-The-Loop）运行。  
这意味着模拟代码在笔记本电脑/计算机上执行，而非飞行控制器，类似 Gazebo 或 jMAVSim。  
在这种情况下，您不需要飞行控制器硬件。

运行 SIH 作为 SITL 的步骤：  

1. 安装 [PX4 开发工具链](../dev_setup/dev_env.md)。  
2. 为每种机体类型运行相应的 make 命令（在 PX4-Autopilot 仓库根目录）：  
   - 四旋翼  

     ```sh
     make px4_sitl sihsim_quadx
     ```

   - 六旋翼  

     ```sh
     make px4_sitl sihsim_hex
     ```

   - 固定翼（plane）  

     ```sh
     make px4_sitl sihsim_airplane
     ```

   - XVert 垂直起降尾座  

     ```sh
     make px4_sitl sihsim_xvert
     ```

   - 标准 VTOL  

     ```sh
     make px4_sitl sihsim_standard_vtol
     ```

### 调整模拟速度  

SITL 允许以高于实时的速度运行模拟。  
要以 10 倍实时速度运行飞机模拟，运行命令：  

```sh
PX4_SIM_SPEED_FACTOR=10 make px4_sitl sihsim_airplane
```

在 SITL 模式下通过 jMAVSim 显示机体时，在另一个终端输入以下命令：  

```sh
./Tools/simulation/jmavsim/jmavsim_run.sh -p 19410 -u -q -o
```

- 添加 `-a` 标志显示固定翼，或 `-t` 显示尾座。  
  若未提供此标志，默认显示四旋翼。  

### 设置自定义起飞位置  

在 SITL 中的 SIH 可通过环境变量设置起飞位置，这将覆盖默认位置。  
需设置的变量为：`PX4_HOME_LAT`、`PX4_HOME_LON` 和 `PX4_HOME_ALT`。  

例如：  

```sh
export PX4_HOME_LAT=28.452386
export PX4_HOME_LON=-13.867138
export PX4_HOME_ALT=28.5
make px4_sitl sihsim_quadx
```

## 添加新机体

[为SIH仿真添加新机体](../dev_airframes/adding_a_new_frame.md)与其它使用场景的流程基本一致。您仍需要配置机体类型和[几何参数](../config/actuators.md)（`CA_`参数），并设置该特定机体的其他默认参数。

::: warning
并非所有机体都支持SIH仿真——目前仅支持[四种机体类型](../advanced_config/parameter_reference.md#SIH_VEHICLE_TYPE)，每种类型在 [`sih.cpp`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/simulator_sih/sih.cpp) 中都有相对固定的实现。
:::

SIH仿真机体的具体差异如下所述：

对于所有SIH仿真变体：

- 需设置所有[硬件在环仿真](../advanced_config/parameter_reference.md#simulation-in-hardware)参数（以 `SIH_` 开头），以配置机体的物理模型。

  ::: tip
  确保 `SIH_*` 参数和 `CA_*` 参数描述的是同一机体。注意某些参数存在冗余！
  :::

- 执行 `param set-default SYS_HITL 2` 以在下次启动时启用SIH。

  ::: info
  此操作还会禁用真实传感器的输入。对于仅在飞控（FC）上启用SIH的情况，还会启用仿真GPS、气压计、磁力计和空速传感器。

  对于作为SITL的SIH，需要显式启用这些传感器，如以下所示。
  :::

- 执行 `param set-default EKF2_GPS_DELAY 0` 以提升状态估计器性能（GPS测量立即可用的假设在常规情况下不现实，但在SIH中是精确的）。

对于飞控（FC）上的SIH：

- 机体文件需放置在 `ROMFS/px4fmu_common/init.d/airframes` 目录，命名格式为 `${ID}_${model_name}.hil`，其中 `ID` 是选择机体的 `SYS_AUTOSTART_ID`，`model_name` 是机体型号名称。
- 在 `ROMFS/px4fmu_common/init.d/airframes/CMakeLists.txt` 中添加型号名称以生成对应的构建目标。
- 执行器通过 `HIL_ACT_FUNC*` 参数配置（而非通常的 `PWM_MAIN_FUNC*` 参数）。这是为了在SIH中避免使用真实执行器输出。同样，反转单个执行器输出范围的位域使用 `HIL_ACT_REV` 而非 `PWM_MAIN_REV`。

对于作为SITL的SIH（无飞控）：

- 机体文件需放置在 `ROMFS/px4fmu_common/init.d-posix/airframes` 目录，命名格式为 `${ID}_sihsim_${model_name}`，其中 `ID` 是选择机体的 `SYS_AUTOSTART_ID`，`model_name` 是机体型号名称。
- 在 `src/modules/simulation/simulator_sih/CMakeLists.txt` 中添加型号名称以生成对应的构建目标。
- 执行器通过常规的 `PWM_MAIN_FUNC*` 和 `PWM_MAIN_REV` 参数配置。
- 此外需设置：
  - `PX4_SIMULATOR=${PX4_SIMULATOR:=sihsim}`
  - `PX4_SIM_MODEL=${PX4_SIM_MODEL:=svtol}`（将 `svtol` 替换为机体型号名称）
- 使用以下参数启用仿真传感器（仅SIH作为SITL时需要）：
  - `param set-default SENS_EN_GPSSIM 1`
  - `param set-default SENS_EN_BAROSIM 1`
  - `param set-default SENS_EN_MAGSIM 1`
  - `param set-default SENS_EN_ARSPDSIM 1`（若为固定翼或VTOL机体且配备空速传感器）

具体示例可参考 [ROMFS/px4fmu_common/init.d-posix/airframes](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/airframes/) 中的 `_sihsim_` 机体（SIH作为SITL）和 [ROMFS/px4fmu_common/init.d/airframes](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d/airframes)（FC上的SIH）。

## 动态模型

各类型机体的动态模型如下：

- 四旋翼：[PDF报告](https://github.com/PX4/PX4-user_guide/raw/main/assets/simulation/SIH_dynamic_model.pdf)。
- 六旋翼：与四旋翼模型相同，但采用对称六旋翼X型执行器布局。
- 固定翼：灵感来源于博士论文：""敏捷固定翼无人机动力学建模"。Khan, Waqas，导师：Nahon, Meyer, McGill University, 博士论文, 2016。"
- 尾座式：灵感来源于硕士论文：""飞翼尾座式无人机建模与控制"。Chiappinelli, Romain，导师：Nahon, Meyer, McGill University, 硕士论文, 2018。"## 视频

<lite-youtube videoid="PzIpSCRD8Jo" title="SIH FW demo"/>## 致谢

SIH 最初由 Coriolis g 公司开发。  
飞机模型和尾坐式模型由 Altitude R&D 公司添加。  
均为加拿大公司：

- Coriolis g 开发了一种基于被动耦合系统的新型垂直起降飞行器（VTOL）；
- [Altitude R&D](https://www.altitude-rd.com/) 专注于动力学、控制和实时仿真（现总部位于苏黎世）。

该模拟器在 BSD 许可下免费发布。

<!-- original author: @romain-chiap -->