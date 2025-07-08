# 硬件在环仿真（HITL）

:::warning
HITL 是 [社区支持和维护的](../simulation/community_supported_simulators.md)。
它可能与当前版本的 PX4 一起工作，也可能不工作。

有关核心开发团队支持的环境和工具的信息，请参见 [工具链安装](../dev_setup/dev_env.md)。
:::

硬件在环（HITL 或 HIL）是一种仿真模式，其中在真实飞行控制器硬件上运行标准的 PX4 固件。这种方法的优势在于可以在真实硬件上测试大部分实际飞行代码。

PX4 支持多旋翼（使用 [jMAVSim](../sim_jmavsim/index.md) 或 [Gazebo Classic](../sim_gazebo_classic/index.md)）和垂直起降飞行器（使用 Gazebo Classic）的 HITL。

<a id="compatible_airframe"></a>## HITL兼容的机体

兼容的机体与模拟器对应关系如下：

| 机体                                                                                                         | `SYS_AUTOSTART` | Gazebo Classic | jMAVSim |
| ---------------------------------------------------------------------------------------------------------------- | --------------- | -------------- | ------- |
| [HIL四旋翼X](../airframes/airframe_reference.md#copter_simulation_hil_quadcopter_x)                        | 1001            | Y              | Y       |
| [HIL标准垂直起降四旋翼](../airframes/airframe_reference.md#vtol_standard_vtol_hil_standard_vtol_quadplane) | 1002            | Y              |         |

<a id="simulation_environment"></a>## HITL 模拟环境

在硬件在环（HITL）模拟中，标准的 PX4 固件会在真实硬件上运行。
JMAVSim 或 Gazebo Classic（运行在开发计算机上）通过 USB/UART 连接到飞控硬件。
模拟器作为网关，在 PX4 和 _QGroundControl_ 之间共享 MAVLink 数据。

::: info
如果飞控硬件支持网络功能且使用稳定的低延迟连接（例如有线以太网连接 - WiFi 通常不够可靠），模拟器也可以通过 UDP 连接。
例如，这种配置已在 PX4 运行于连接电脑以太网的 Raspberry Pi 上进行过测试（包含运行 jMAVSim 命令的启动配置可参考 [px4_hil.config](https://github.com/PX4/PX4-Autopilot/blob/main/posix-configs/rpi/px4_hil.config)）。
:::

下图展示了模拟环境：

- 通过 _QGroundControl_ 选择 HITL 配置，该配置不会启动任何真实传感器。
- _jMAVSim_ 或 _Gazebo Classic_ 通过 USB 连接到飞控。
- 模拟器通过 UDP 连接到 _QGroundControl_，并将其 MAVLink 消息桥接到 PX4。
- _Gazebo Classic_ 和 _jMAVSim_ 还可连接到外部 API 并桥接 MAVLink 消息到 PX4。
- （可选）可通过串口连接使用 _QGroundControl_ 连接摇杆/游戏手柄硬件。

![HITL 设置 - jMAVSim 和 Gazebo Classic](../../assets/simulation/px4_hitl_overview_jmavsim_gazebo.svg)

## HITL与SITL

SITL在开发计算机的模拟环境中运行，并使用为此环境专门生成的固件。除了使用模拟驱动程序提供来自模拟器的虚假环境数据外，系统行为与正常运行时相同。

相比之下，HITL在正常硬件上以"HITL模式"运行标准PX4固件。模拟数据进入系统的入口点与SITL不同。核心模块如commander和sensors在启动时具有HITL模式，会绕过部分正常功能。

总结来说，HITL在实际硬件上使用标准固件运行PX4，但SITL实际上执行了更多的标准系统代码。

## 设置HITL## 检查 HITL 是否在固件中

HITL 所需的模块 ([`pwm_out_sim`](../modules/modules_driver.md#pwm-out-sim)) 并未默认包含在所有 PX4 固件中。

要检查飞控中是否包含该模块：

1. 打开 QGroundControl
2. 打开 **Analyze Tools > Mavlink Console**。
3. 在控制台中输入以下命令：

   ```sh
   pwm_out_sim status
   ```

4. 如果返回值是 `nsh: pwm_out_sim: command not found`，则表示未安装该模块。

如果 `pwm_out_sim` 不存在，需要将其添加到固件中才能使用 HITL 仿真。

### 将 HITL 模块添加到固件

在飞行控制器配置文件中添加以下键值以包含所需模块（示例请参见 [boards/px4/fmu-v6x/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v6x/default.px4board)），然后重新构建固件并刷入板载设备。

```text
CONFIG_MODULES_SIMULATION_PWM_OUT_SIM=y
```

您也可以使用以下命令启动 GUI 配置工具，并在路径 **modules > Simulation > pwm_out_sim** 下交互式启用模块。例如，更新 fmu-v6x 可使用：

```sh
make px4_fmu-v6x boardconfig
```

### PX4 配置

1. 通过 USB 直接将飞控连接到 _QGroundControl_。
2. 选择机体
   1. 打开 **Setup > Airframes**
   2. 选择一个 [兼容的机体](#compatible_airframe) 进行测试，然后在 _Airframe Setup_ 页面右上角点击 **Apply and Restart**。
3. 如果需要，校准遥控器或操纵杆。
4. 设置 UDP
   1. 在设置菜单的 _General_ 选项卡下，取消勾选所有 _AutoConnect_ 选项，仅保留 **UDP**。

      ![QGC HITL 自动连接设置](../../assets/gcs/qgc_hitl_autoconnect.png)

5. (可选) 配置操纵杆和失速保护。
   设置以下 [参数](../advanced_config/parameters.md) 以使用操纵杆代替遥控器：
   - [COM_RC_IN_MODE](../advanced_config/parameter_reference.md#COM_RC_IN_MODE) 设置为 "Joystick/No RC Checks"。这允许操纵杆输入并禁用遥控器输入检查。
   - [NAV_RCL_ACT](../advanced_config/parameter_reference.md#NAV_RCL_ACT) 设置为 "Disabled"。这确保在未运行 HITL 时不会触发遥控器失速保护。

   :::tip
   _QGroundControl 用户指南_ 也包含关于 [操纵杆](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/joystick.html) 和 [虚拟操纵杆](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/virtual_joystick.html) 设置的说明。
   :::

配置完成后，**关闭** _QGroundControl_ 并将飞控硬件从计算机上断开。

### 模拟器特定设置

请根据以下章节中的说明执行特定模拟器的设置步骤。

#### Gazebo Classic

::: info
确保 _QGroundControl_ 未运行！
:::

1. 使用 [Gazebo Classic](../sim_gazebo_classic/index.md) 构建 PX4（以构建 Gazebo Classic 插件）。

   ```sh
   cd <Firmware_clone>
   DONT_RUN=1 make px4_sitl_default gazebo-classic
   ```

1. 打开机体模型的 sdf 文件（例如 **Tools/simulation/gazebo-classic/sitl_gazebo-classic/models/iris_hitl/iris_hitl.sdf**）。
1. 如有必要，替换 `serialDevice` 参数（`/dev/ttyACM0`）。

   ::: info
   串口设备取决于连接机体到计算机所使用的端口（通常是 `/dev/ttyACM0`）。
   在 Ubuntu 上可以通过插入飞控、打开终端并输入 `dmesg | grep "tty"` 来检查。
   显示的最后一个设备即为正确设备。
   :::

1. 设置环境变量：

   ```sh
   source Tools/simulation/gazebo-classic/setup_gazebo.bash $(pwd) $(pwd)/build/px4_sitl_default
   ```

   并以 HITL 模式运行 Gazebo Classic：

   ```sh
   gazebo Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds/hitl_iris.world
   ```

1. 启动 _QGroundControl_。
   它将自动连接到 PX4 和 Gazebo Classic。

#### jMAVSim（仅限四旋翼）

::: info
确保 _QGroundControl_ 未运行！
:::

1. 将飞控连接到计算机并等待其启动。
1. 以 HITL 模式运行 jMAVSim：

   ```sh
   ./Tools/simulation/jmavsim/jmavsim_run.sh -q -s -d /dev/ttyACM0 -b 921600 -r 250
   ```

   ::: info
   根据需要替换串口名称 `/dev/ttyACM0`。
   在 macOS 上该端口为 `/dev/tty.usbmodem1`。
   在 Windows（包括 Cygwin）上则为 COM1 或其他端口 - 请在 Windows 设备管理器中检查连接。
   :::

1. 启动 _QGroundControl_。
   它将自动连接到 PX4 和 jMAVSim。

## 在 HITL 中飞行自主任务

您应该能够使用 _QGroundControl_ [运行任务](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/fly_view/fly_view.html#missions) 并以其他方式控制机体。