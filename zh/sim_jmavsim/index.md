# jMAVSim with SITL

:::warning
此模拟器由[社区支持和维护](../simulation/community_supported_simulators.md)。
它可能与当前版本的PX4兼容，也可能不兼容，并可能在未来的版本中被移除。

有关核心开发团队支持的环境和工具的信息，请参见[工具链安装](../dev_setup/dev_env.md)。
:::

jMAVSim 是一个简单的多旋翼/四旋翼模拟器，允许您在模拟世界中飞行运行 PX4 的_多旋翼_类型机体。  
它易于设置，可用于测试您的机体是否能够起飞、飞行、着陆，并对各种故障条件（例如 GPS 故障）作出适当响应。

<strong>支持的机体:</strong>

- Quad

本主题展示如何设置 jMAVSim 以连接 PX4 的 SITL 版本。

:::tip
jMAVSim 也可用于 HITL 模拟（[如此处所示](../simulation/hitl.md#jmavsim-quadrotor-only)）。
:::

## 安装

我们为Ubuntu Linux和Windows系统提供了[jMAVSim](../sim_jmavsim/index.md)的安装配置，请参阅[标准构建指南](../dev_setup/dev_env.md)。  
按照以下步骤在macOS上安装jMAVSim：

### macOS

要配置[jMAVSim](../sim_jmavsim/index.md)模拟环境：

1. 安装最新版本的Java（例如Java 15）。  
   可从[Oracle下载Java 15或更高版本](https://www.oracle.com/java/technologies/javase-downloads.html)，或使用[Eclipse Temurin](https://adoptium.net)：

   ```sh
   brew install --cask temurin
   ```

1. 安装jMAVSim：

   ```sh
   brew install px4-sim-jmavsim
   ```

   :::warning
   PX4 v1.11及更高版本需要至少JDK 15来运行jMAVSim模拟。

   旧版本中，macOS用户可能会遇到错误 `Exception in thread "main" java.lang.UnsupportedClassVersionError:`。  
   解决方案请参见[jMAVSim with SITL > Troubleshooting](../sim_jmavsim/index.md#troubleshooting))。
   :::

## 仿真环境

软件在环仿真（Software in the Loop Simulation）在主机上运行整个系统并模拟自动驾驶仪。它通过本地网络连接到仿真器。系统结构如下：

[![Mermaid graph: SITL Simulator](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggTFI7XG4gIFNpbXVsYXRvci0tPk1BVkxpbms7XG4gIE1BVkxpbmstLT5TSVRMOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggTFI7XG4gIFNpbXVsYXRvci0tPk1BVkxpbms7XG4gIE1BVkxpbmstLT5TSVRMOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)

## 运行SITL

在确保系统已安装[simulation prerequisites](../dev_setup/dev_env.md)后，只需启动：该便捷的make目标将编译POSIX主机构建并运行模拟。

```sh
make px4_sitl_default jmavsim
```

这将启动PX4 shell：

```sh
[init] shell id: 140735313310464
[init] task name: px4

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

Ready to fly.


pxh>
```

同时会弹出一个窗口显示[jMAVSim](https://github.com/PX4/jMAVSim)模拟器的3D视图：

![jMAVSim 3d View](../../assets/simulation/jmavsim/jmavsim.jpg)

## 送上天空

系统将开始打印状态信息。当您获得位置锁定后（控制台显示消息 _EKF commencing GPS fusion_ 后不久）即可开始飞行。

要起飞，请在控制台输入以下命令：

```sh
pxh> commander takeoff
```

您可以使用 _QGroundControl_ 执行任务，或通过 [joystick](#using-a-joystick) 连接飞行。

## 使用/配置选项

适用于所有模拟器的选项在顶层[模拟](../simulation/index.md#sitl-simulation-environment)主题中介绍（其中一些可能在下方重复）。

### 模拟传感器/硬件故障

[模拟故障保护](../simulation/failsafes.md)说明如何触发GPS故障和电池耗尽等安全保护机制。

### 设置自定义起降位置

默认起降位置可通过环境变量覆盖：`PX4_HOME_LAT`、`PX4_HOME_LON`和`PX4_HOME_ALT`。

例如设置纬度、经度和海拔：

```sh
export PX4_HOME_LAT=28.452386
export PX4_HOME_LON=-13.867138
export PX4_HOME_ALT=28.5
make px4_sitl_default jmavsim
```

### 调整仿真速度

可通过环境变量`PX4_SIM_SPEED_FACTOR`调整仿真速度相对于实时的速度。

以双倍实时运行：

```sh
PX4_SIM_SPEED_FACTOR=2 make px4_sitl_default jmavsim
```

以半倍实时运行：

```sh
PX4_SIM_SPEED_FACTOR=0.5  make px4_sitl_default jmavsim
```

要对当前会话的所有SITL运行应用因子，使用`EXPORT`：

```sh
export PX4_SIM_SPEED_FACTOR=2
make px4_sitl_default jmavsim
```

### 使用游戏手柄

通过_QGroundControl_支持游戏手柄和拇指摇杆（[设置说明在此](../simulation/index.md#joystick-gamepad-integration)）。

### 模拟Wi-Fi无人机

有一个特殊目标可模拟通过本地网络Wi-Fi连接的无人机：

```sh
make broadcast jmavsim
```

模拟器会像真实无人机一样在本地网络上广播其地址。

### 分开启动JMAVSim和PX4

可以分别启动JMAVSim和PX4：

```sh
./Tools/simulation/jmavsim/jmavsim_run.sh -l
make px4_sitl none
```

这允许更快的测试循环（重新启动jMAVSim需要更长时间）。

### 无头模式

要以无GUI方式启动jMAVSim，请设置环境变量`HEADLESS=1`：

```sh
HEADLESS=1 make px4_sitl jmavsim
```

## 多机体仿真

JMAVSim 可用于多机体仿真：[Multi-Vehicle Sim with JMAVSim](../sim_jmavsim/multi_vehicle.md)。

## Lockstep

PX4 SITL 和 jMAVSim 被配置为以 _lockstep_ 模式运行。这意味着 PX4 和模拟器以相同速度运行，因此可以对传感器和执行器消息做出适当响应。Lockstep 机制使得[更改模拟速度](#change-simulation-speed)成为可能，并且可以暂停以逐步执行代码。

#### Lockstep 序列

Lockstep 的步骤顺序为：

1. 模拟器发送包含时间戳 `time_usec` 的传感器消息 [HIL_SENSOR](https://mavlink.io/en/messages/common.html#HIL_SENSOR) 以更新 PX4 的传感器状态和时间。
1. PX4 接收后执行一次状态估计、控制等操作，最终发送执行器消息 [HIL_ACTUATOR_CONTROLS](https://mavlink.io/en/messages/common.html#HIL_ACTUATOR_CONTROLS)。
1. 模拟器等待接收执行器/电机消息后，模拟物理过程并计算下一传感器消息再次发送给 PX4。

系统启动时有一个 "freewheeling" 阶段，此时模拟器发送包含时间的传感器消息并运行 PX4，直到其初始化完成并响应执行器消息。

#### 禁用 Lockstep

如果要禁用 lockstep 模拟（例如 SITL 需要与不支持该功能的模拟器配合使用），则模拟器和 PX4 将使用主机系统时间且不再相互等待。

在以下位置禁用 lockstep：

- PX4 中，运行 `make px4_sitl_default boardconfig` 并设置工具链下的 `BOARD_NOLOCKSTEP` "强制禁用 lockstep" 符号。
- jMAVSim 中，移除 [sitl_run.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/simulation/jsbsim/sitl_run.sh#L40) 中的 `-l` 参数，或确保以不带 `-lockstep` 标志的方式启动 Java 二进制文件。

<!-- sitl_run.sh 中的相关行是： -->
<!-- # 启动 Java 模拟器 -->
<!-- "$src_path"/Tools/simulation/jmavsim/jmavsim_run.sh -r 250 -l & SIM_PID=$!  -->## 扩展和自定义

要扩展或自定义模拟接口，请编辑 **Tools/jMAVSim** 文件夹中的文件。代码可以通过GitHub上的[jMAVSim仓库](https://github.com/px4/jMAVSim)获取。

::: info
构建系统会强制为所有依赖项（包括模拟器）检出正确的子模块。
它不会覆盖目录中文件的修改，但当这些修改被提交时，需要将子模块在固件仓库中注册为新的提交哈希值。为此，请执行 `git add Tools/jMAVSim` 并提交修改。
这将更新模拟器的GIT哈希值。
:::

## 与ROS的接口

模拟可以通过[与ROS接口](../simulation/ros_interface.md)的方式，与真实机体上的接口方式相同。

## 重要文件

- 启动脚本相关内容请参见 [System Startup](../concept/system_startup.md)。
- 模拟根文件系统("`/`"目录)创建在构建目录内，路径为：`build/px4_sitl_default/rootfs`。

## 故障排查

### java.lang.NoClassDefFoundError

```sh
Exception in thread "main" java.lang.NoClassDefFoundError: javax/vecmath/Tuple3d
at java.base/java.lang.Class.forName0(Native Method)
at java.base/java.lang.Class.forName(Class.java:374)
at org.eclipse.jdt.internal.jarinjarloader.JarRsrcLoader.main(JarRsrcLoader.java:56)
Caused by: java.lang.ClassNotFoundException: javax.vecmath.Tuple3d
at java.base/java.net.URLClassLoader.findClass(URLClassLoader.java:466)
at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:566)
at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:499)
... 3 more
Exception in thread "main" java.lang.NoClassDefFoundError: javax/vecmath/Tuple3d
at java.base/java.lang.Class.forName0(Native Method)
at java.base/java.lang.Class.forName(Class.java:374)
at org.eclipse.jdt.internal.jarinjarloader.JarRsrcLoader.main(JarRsrcLoader.java:56)
Caused by: java.lang.ClassNotFoundException: javax.vecmath.Tuple3d
at java.base/java.net.URLClassLoader.findClass(URLClassLoader.java:466)
at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:566)
at java.base/java.lang.ClassLoader.loadClass(ClassLoader.java:499)
```

在jMAVSim子模块更新至[新版jar库](https://github.com/PX4/jMAVSim/pull/119)后，此错误将不再出现。Java 11或Java 14均可正常工作。

### An illegal reflective access operation has occurred

此警告可忽略（虽然会显示但仿真仍能正常运行）。

```sh
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by javax.media.j3d.JoglPipeline (rsrc:j3dcore.jar) to method sun.awt.AppContext.getAppContext()
WARNING: Please consider reporting this to the maintainers of javax.media.j3d.JoglPipeline
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
Inconsistency detected by ld.so: dl-lookup.c: 112: check_match: Assertion version->filename == NULL || ! _dl_name_match_p (version->filename, map)' failed!
```

### java.awt.AWTError: Assistive Technology not found: org.GNOME.Accessibility.AtkWrapper

```sh
Exception in thread "main" java.lang.reflect.InvocationTargetException
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(De43)
at java.lang.reflect.Method.invoke(Method.java:498)
at org.eclipse.jdt.internal.jarinjarloader.JarRsrcLoader.main(JarRsrcLoader.java:58)
Caused by: java.awt.AWTError: Assistive Technology not found: org.GNOME.Accessibility.AtkWrapper
at java.awt.Toolkit.loadAssistiveTechnologies(Toolkit.java:807)
at java.awt.Toolkit.getDefaultToolkit(Toolkit.java:886)
at java.awt.Window.getToolkit(Window.java:1358)
at java.awt.Window.init(Window.java:506)
at java.awt.Window.(Window.java:537)
at java.awt.Frame.(Frame.java:420)
at java.awt.Frame.(Frame.java:385)
at javax.swing.JFrame.(JFrame.java:189)
at me.drton.jmavsim.Visualizer3D.(Visualizer3D.java:104)
at me.drton.jmavsim.Simulator.(Simulator.java:157)
at me.drton.jmavsim.Simulator.main(Simulator.java:678)
```

若出现此错误，请尝试以下解决方案：

编辑 **accessibility.properties** 文件：

```sh
sudo gedit /etc/java-8-openjdk/accessibility.properties
```

并注释掉下述行：

```sh
#assistive_technologies=org.GNOME.Acessibility.AtkWrapper
```

更多信息请查看 [GitHub issue](https://github.com/PX4/PX4-Autopilot/issues/9557)。有贡献者在 [askubuntu.com](https://askubuntu.com/questions/695560) 找到修复方案。

### Exception in thread "main" java.lang.UnsupportedClassVersionError

编译jMAVsim时可能会遇到以下错误：

```sh
Exception in thread "main" java.lang.UnsupportedClassVersionError: me/drton/jmavsim/Simulator has been compiled by a more recent version of the Java Runtime (class file version 59.0), this version of the Java Runtime only recognizes class file versions up to 58.0
```

该错误表明您需要安装更高版本的Java。类文件版本58对应jdk14，版本59对应jdk15，版本60对应jdk16等。

在macOS下，建议通过homebrew安装OpenJDK：

```sh
brew install --cask adoptopenjdk16
```