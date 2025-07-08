# FlightGear 仿真

:::warning  
此仿真器为[社区支持和维护](../simulation/community_supported_simulators.md)。  
是否与当前版本的PX4兼容尚未确定。

有关核心开发团队支持的环境和工具信息，请参阅[工具链安装](../dev_setup/dev_env.md)。  
:::

[FlightGear](https://www.flightgear.org/) 是一款具有强大[FDM引擎](http://wiki.flightgear.org/Flight_Dynamics_Model)的飞行模拟器。  
这使得 FlightGear 能够在各种气象条件下模拟旋翼飞行器（这也是该桥梁最初由[ThunderFly s.r.o.](https://www.thunderfly.cz/)开发的原因）。

本文档描述了 FlightGear 在 SITL 中的单机体使用。  
关于多机体使用的更多信息请参阅：[使用 FlightGear 的多机体仿真](../sim_flightgear/multi_vehicle.md)。

**支持的机体**：自转旋翼机、固定翼飞机、地面车。

<lite-youtube videoid="iqdcN5Gj4wI" title="[ThunderFly] PX4 SITL with Flightgear, Rascal110 - electric version"/>

[![Mermaid Graph ](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggTFI7XG4gIEZsaWdodEdlYXIgLS0-IEZsaWdodEdlYXItQnJpZGdlO1xuICBGbGlnaHRHZWFyLUJyaWRnZSAtLT4gTUFWTGluaztcbiAgTUFWTGluayAtLT4gUFg0X1NJVEw7XG5cdCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggTFI7XG4gIEZsaWdodEdlYXIgLS0-IEZsaWdodEdlYXItQnJpZGdlO1xuICBGbGlnaHRHZWFyLUJyaWRnZSAtLT4gTUFWTGluaztcbiAgTUFWTGluayAtLT4gUFg0X1NJVEw7XG5cdCIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)

<!-- Original mermaid graph  
graph LR;  
  FlightGear-- >FlightGear-Bridge;  
  FlightGear-Bridge-- >MAVLink;  
  MAVLink-- >PX4_SITL;  
-->

::: info  
有关模拟器、模拟环境和模拟配置的通用信息，请参阅[仿真](../simulation/index.md)（例如支持的机体）。  
:::

## 安装 (Ubuntu Linux)

::: info
这些说明已针对 Ubuntu 18.04 进行测试
:::

1. 安装标准的 [Ubuntu LTS / Debian Linux 开发环境](../dev_setup/dev_env_linux_ubuntu.md)。
1. 安装 FlightGear：

   ```sh
   sudo add-apt-repository ppa:saiarcot895/flightgear
   sudo apt update
   sudo apt install flightgear
   ```

   此命令将从 PAA 仓库安装最新稳定版 FlightGear 及其 FGdata 包。

   :::tip
   对于部分机型（例如配备电动引擎的机型），可能需要安装包含最新功能的每日构建版本。
   可通过 [每日构建 PPA](https://launchpad.net/~saiarcot895/+archive/ubuntu/flightgear-edge) 安装。
   :::

1. 验证是否能够运行 FlightGear：

   ```sh
   fgfs --launcher
   ```

1. 为 FlightGear 安装目录中的 **Protocols** 文件夹设置写入权限：

   ```sh
   sudo chmod a+w /usr/share/games/flightgear/Protocol
   ```

   设置权限是必要的，因为 PX4-FlightGear-Bridge 会将通信定义文件放置在此处。

更多安装说明请参见 [FlightGear wiki](http://wiki.flightgear.org/Howto:Install_Flightgear_from_a_PPA)。

## 运行仿真

通过启动 PX4 SITL 并指定所需机体配置来运行仿真。

最简单的方法是在 PX4 _PX4-Autopilot_ 仓库的根目录打开终端并调用 `make` 指定目标。
例如，启动固定翼飞机仿真：

```sh
cd /path/to/PX4-Autopilot
make px4_sitl_nolockstep flightgear_rascal
```

支持的机体和 `make` 命令如下（点击链接查看机体图像）。

| 机体                                                                                   | 命令                                      |
| ----------------------------------------------------------------------------------------- | -------------------------------------------- |
| [Standard Plane](../sim_flightgear/vehicles.md#standard-plane)                            | `make px4_sitl_nolockstep flightgear_rascal` |
| [Ackermann vehicle (UGV/Rover)](../sim_flightgear/vehicles.md#ackerman-vehicle-ugv-rover) | `make px4_sitl_nolockstep flightgear_tf-r1`  |
| [Autogyro](../sim_flightgear/vehicles.md#autogyro)                                        | `make px4_sitl_nolockstep flightgear_tf-g1`  |

上述命令将启动带有完整界面的单机体仿真。
_QGroundControl_ 应能自动连接到仿真机体。

::: info
要查看完整的 FlightGear 构建目标（高亮显示），运行：

```sh
make px4_sitl_nolockstep list_vmd_make_targets | grep flightgear_
```

更多详情请参阅：[FlightGear Vehicles](../sim_flightgear/vehicles.md)（包含"unsupported"机体和添加新机体的信息）。
:::

::: info
如果遇到构建错误，[Installing Files and Code](../dev_setup/dev_env.md) 指南是很有用的参考资料。
:::

## 上天飞行

上述 `make` 命令首先构建 PX4 并随后与 FlightGear 模拟器一起运行。

一旦 PX4 启动，它将按照如下所示启动 PX4 壳层。
您必须选择回车以获取命令提示符。

```sh
______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

px4 starting.

INFO  [px4] Calling startup script: /bin/sh etc/init.d-posix/rcS 0
INFO  [param] selected parameter default file eeprom/parameters_1034
I'm Mavlink to FlightGear Bridge
Target Bridge Freq: 200, send data every step: 1
4
  5   -1
  7   -1
  2   1
  4   1
[param] Loaded: eeprom/parameters_1034
INFO  [dataman] Unknown restart, data manager file './dataman' size is 11798680 bytes
INFO  [simulator] Waiting for simulator to accept connection on TCP port 4560
INFO  [simulator] Simulator connected on TCP port 4560.
INFO  [commander] LED: open /dev/led0 failed (22)
INFO  [commander] Mission #3 loaded, 9 WPs, curr: 8
INFO  [mavlink] mode: Normal, data rate: 4000000 B/s on udp port 18570 remote port 14550
INFO  [airspeed_selector] No airspeed sensor detected. Switch to non-airspeed mode.
INFO  [mavlink] mode: Onboard, data rate: 4000000 B/s on udp port 14580 remote port 14540
INFO  [mavlink] mode: Onboard, data rate: 4000 B/s on udp port 14280 remote port 14030
INFO  [logger] logger started (mode=all)
INFO  [logger] Start file log (type: full)
INFO  [logger] Opened full log file: ./log/2020-04-28/22_03_36.ulg
INFO  [mavlink] MAVLink only on localhost (set param MAV_{i}_BROADCAST = 1 to enable network)
INFO  [px4] Startup script returned successfully
pxh> StatsHandler::StatsHandler() Setting up GL2 compatible shaders
Now checking for plug-in osgPlugins-3.4.1/osgdb_nvtt.so
PX4 Communicator: PX4 Connected.

pxh>
```

控制台将输出 PX4 加载机体特定初始化和参数文件的状态，并等待（并连接到）模拟器。
一旦出现 [ecl/EKF] `commencing GPS fusion` 的 INFO 输出，机体即可解锁。
此时，您应该会看到 FlightGear 窗口显示飞机的视图。

::: info
您可以通过按下 **Ctrl+V** 更改视图。
:::

![FlightGear UI](../../assets/simulation/flightgear/flightgearUI.jpg)

您可以通过输入以下命令使其起飞：

```sh
pxh> commander takeoff
```

## 使用/配置选项

你可以通过以下环境变量来调整FG安装/设置：

- `FG_BINARY` - 运行的FG二进制文件的绝对路径。（可以是AppImage）
- `FG_MODELS_DIR` - 包含手动下载的飞机模型文件夹的绝对路径，这些模型应被用于模拟。
- `FG_ARGS_EX` - 任何额外的FG参数。

<a id="frame_rate"></a>

### 显示帧率

在FlightGear中，你可以通过启用以下路径显示帧率：**View > View Options > Show frame rate**。

### 设置自定义起飞位置

SITL FlightGear中的起飞位置可以通过额外变量进行设置。设置该变量将覆盖默认的起飞位置。

可设置的变量包括：`--airport`，`--runway`，和`--offset-distance`。其他选项请参考[FlightGear wiki](http://wiki.flightgear.org/Command_line_options#Initial_Position_and_Orientation)

例如：

```sh
FG_ARGS_EX="--airport=PHNL"  make px4_sitl_nolockstep flightgear_rascal
```

上述示例在[Honolulu international airport](http://wiki.flightgear.org/Suggested_airports)启动模拟。

### 使用摇杆

通过_QGroundControl_支持摇杆和拇指摇杆（[设置说明在此](../simulation/index.md#joystick-gamepad-integration)）。

FlightGear中的摇杆输入应被禁用，否则会导致FG摇杆输入和PX4命令之间的"race condition"。

## 扩展和定制

要扩展或定制模拟接口，请编辑 **Tools/simulation/flightgear/flightgear_bridge** 文件夹中的文件。
代码可在Github上的 [PX4-FlightGear-Bridge 仓库](https://github.com/ThunderFly-aerospace/PX4-FlightGear-Bridge) 中获取。

## 更多信息

- [PX4-FlightGear-Bridge readme](https://github.com/ThunderFly-aerospace/PX4-FlightGear-Bridge)