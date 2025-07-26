# 构建 PX4 软件

PX4 固件可以从源代码在控制台或 IDE 中进行编译，适用于模拟和硬件目标。

您需要构建 PX4 来使用 [模拟器](../simulation/index.md)，或者如果您想修改 PX4 并创建自定义构建。
如果您只是想在真实硬件上尝试 PX4，则使用 QGroundControl [加载预编译二进制文件](../config/firmware.md)（无需遵循这些说明）。

::: 信息
在遵循这些说明之前，您必须首先为您的主机操作系统和目标硬件安装 [开发者工具链](../dev_setup/dev_env.md)。
如果您在完成这些步骤后遇到任何问题，请参阅下面的 [故障排除](#故障排除) 部分。
:::

## 下载 PX4 源代码

PX4 源代码存储在 GitHub 上的 [PX4/PX4-Autopilot](https://github.com/PX4/PX4-Autopilot) 代码仓库中。

要将最新版本（`main` 分支）代码下载到本地计算机，请在终端中输入以下命令：

```sh
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

请注意，您可能在安装 [开发工具链](../dev_setup/dev_env.md) 时已执行过此操作。

::: info
这是获取最新代码所需执行的全部操作。
如需获取特定发布版本的源代码，也可以[获取特定发布的源代码](../contribute/git_examples.md#get-a-specific-release)。
[Git 示例](../contribute/git_examples.md) 提供了更多关于版本管理和为 PX4 贡献代码的详细信息。
:::

## 首次构建（使用模拟器）

我们首先在控制台环境中构建一个模拟目标。
这允许我们在转向真实硬件和IDE之前验证系统设置。

进入 **PX4-Autopilot** 目录并使用以下命令启动 [Gazebo SITL](../sim_gazebo_gz/index.md)：

```sh
make px4_sitl gz_x500
```

::: details 如果你安装了 Gazebo Classic
使用以下命令启动 [Gazebo Classic SITL](../sim_gazebo_classic/index.md)：

```sh
make px4_sitl gazebo-classic
```

:::

这将启动 PX4 控制台：

![PX4 控制台](../../assets/toolchain/console_gazebo.png)

::: info
在继续之前可能需要先启动 _QGroundControl_ ，因为默认的PX4配置需要地面控制连接才能起飞。
可以从[这里下载](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html)。

:::

通过输入以下命令（如上文控制台所示）可以飞行无人机：

```sh
pxh> commander takeoff
```

机体将起飞，你将在 Gazebo 模拟器 UI 中看到如下效果：

![Gazebo UI 显示机体起飞](../../assets/toolchain/gazebo_takeoff.png)

输入 `commander land` 可以降落无人机，通过 **CTRL+C**（或输入 `shutdown`）可以停止整个模拟。

通过地面控制站进行模拟飞行更接近机体实际运行情况。
当机体处于飞行状态（起飞飞行模式）时，点击地图上的某个位置并启用滑块，这将重新定位机体。

![QGroundControl GoTo](../../assets/toolchain/qgc_goto.jpg)

## NuttX / Pixhawk 基板

### 为 NuttX 构建

要为 NuttX 或 Pixhawk 基板构建，进入 **PX4-Autopilot** 目录并调用 `make` 命令，使用您基板的构建目标。

例如，要为 [Pixhawk 4](../flight_controller/pixhawk4.md) 硬件构建，可以使用以下命令：

```sh
cd PX4-Autopilot
make px4_fmu-v5_default
```

成功构建的输出示例如下：

```sh
-- Build files have been written to: /home/youruser/src/PX4-Autopilot/build/px4_fmu-v4_default
[954/954] Creating /home/youruser/src/PX4-Autopilot/build/px4_fmu-v4_default/px4_fmu-v4_default.px4
```

构建目标的第一部分 `px4_fmu-v4` 表示固件的目标飞控硬件。
后缀（此处为 `_default`）表示固件的 _配置_，例如是否支持或省略某些功能。

::: info
`_default` 后缀是可选的。
例如，`make px4_fmu-v5` 和 `px4_fmu-v5_default` 生成的固件相同。
:::

以下列表展示了 [Pixhawk 标准](../flight_controller/autopilot_pixhawk_standard.md) 基板的构建命令：

- [Holybro Pixhawk 6X-RT (FMUv6X)](../flight_controller/pixhawk6x-rt.md): `make px4_fmu-v6xrt_default`
- [Holybro Pixhawk 6X (FMUv6X)](../flight_controller/pixhawk6x.md): `make px4_fmu-v6x_default`
- [Holybro Pixhawk 6C (FMUv6C)](../flight_controller/pixhawk6c.md): `make px4_fmu-v6c_default`
- [Holybro Pixhawk 6C Mini (FMUv6C)](../flight_controller/pixhawk6c_mini.md): `make px4_fmu-v6c_default`
- [Holybro Pix32 v6 (FMUv6C)](../flight_controller/holybro_pix32_v6.md): `make px4_fmu-v6c_default`
- [Holybro Pixhawk 5X (FMUv5X)](../flight_controller/pixhawk5x.md): `make px4_fmu-v5x_default`
- [Pixhawk 4 (FMUv5)](../flight_controller/pixhawk4.md): `make px4_fmu-v5_default`
- [Pixhawk 4 Mini (FMUv5)](../flight_controller/pixhawk4_mini.md): `make px4_fmu-v5_default`
- [CUAV V5+ (FMUv5)](../flight_controller/cuav_v5_plus.md): `make px4_fmu-v5_default`
- [CUAV V5 nano (FMUv5)](../flight_controller/cuav_v5_nano.md): `make px4_fmu-v5_default`
- [Pixracer (FMUv4)](../flight_controller/pixracer.md): `make px4_fmu-v4_default`
- [Pixhawk 3 Pro](../flight_controller/pixhawk3_pro.md): `make px4_fmu-v4pro_default`
- [Pixhawk Mini](../flight_controller/pixhawk_mini.md): `make px4_fmu-v3_default`
- [Pixhawk 2 (Cube Black) (FMUv3)](../flight_controller/pixhawk-2.md): `make px4_fmu-v3_default`
- [mRo Pixhawk (FMUv3)](../flight_controller/mro_pixhawk.md): `make px4_fmu-v3_default` (支持 2MB Flash)
- [Holybro pix32 (FMUv2)](../flight_controller/holybro_pix32.md): `make px4_fmu-v2_default`
- [Pixfalcon (FMUv2)](../flight_controller/pixfalcon.md): `make px4_fmu-v2_default`
- [Dropix (FMUv2)](../flight_controller/dropix.md): `make px4_fmu-v2_default`
- [Pixhawk 1 (FMUv2)](../flight_controller/pixhawk.md): `make px4_fmu-v2_default`

  :::warning
  你 **必须** 使用支持 GCC 的工具链构建此目标。
  :::

- [Pixhawk 1 (2MB Flash) (FMUv2)](../flight_controller/pixhawk.md): `make px4_fmu-v2_default`

  :::warning
  构建此目标 **必须** 使用支持 GCC 的工具链。
  :::

非 Pixhawk 标准基板的构建命令请参考 [构建文档](https://docs.px4.io/main/en/dev_setup/building_px4.html)。

### 上传固件

使用以下命令上传固件到飞控硬件：

```sh
make px4_fmu-v5_default upload
```

::: tip
上传前请确保飞控硬件已连接并处于引导模式。
:::

## 其他开发板

其他开发板的构建命令在[开发板特定的飞控页面](../flight_controller/index.md)中给出（通常在 _Building Firmware_ 标题下）。

你可以使用以下命令列出所有配置目标：

```sh
make list_config_targets
```

## 在图形化IDE中编译

[VSCode](../dev_setup/vscode.md) 是 PX4 开发的官方支持（且推荐）的集成开发环境。
设置简单，可以用于编译 PX4 的仿真和硬件环境。

## 故障排除

### 通用构建错误

许多构建问题是由子模块不匹配或构建环境未完全清理导致的。
更新子模块并执行 `distclean` 可解决这些错误：

```sh
git submodule update --recursive
make distclean
```

### Flash 超出 XXX 字节

`region 'flash' overflowed by XXXX bytes` 错误表示固件大小超过了目标硬件平台的限制。
这在 `make px4_fmu-v2_default` 构建中常见，因为 Flash 大小被限制为 1MB。

如果你构建的是 _vanilla_ master 分支，最可能的原因是使用了不受支持的 GCC 版本。
在这种情况下，请安装 [Developer Toolchain](../dev_setup/dev_env.md) 指南中指定的版本。

如果你构建的是自己的分支，可能是因为固件大小超过了 1MB 限制。
此时需要从构建中移除不需要的驱动程序/模块。

### macOS: 打开文件数过多错误

macOS 默认允许所有运行进程最多打开 256 个文件。
PX4 构建系统会打开大量文件，因此你可能会超出这个限制。

构建工具链随后会报告许多文件的 `Too many open files` 错误，如下面所示：

```sh
/usr/local/Cellar/gcc-arm-none-eabi/20171218/bin/../lib/gcc/arm-none-eabi/7.2.1/../../../../arm-none-eabi/bin/ld: cannot find NuttX/nuttx/fs/libfs.a: Too many open files
```

解决方法是增加允许的最大打开文件数（例如设为 300）。
你可以在 macOS _终端_ 中为每个会话执行此操作：

- 运行此脚本 [Tools/mac_set_ulimit.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/mac_set_ulimit.sh)，或
- 输入此命令：

  ```sh
  ulimit -S -n 300
  ```

### macOS Catalina: 运行 cmake 时的问题

从 macOS Catalina 10.15.1 开始，尝试使用 _cmake_ 构建模拟器时可能会出现问题。
如果在此平台上遇到构建问题，请在终端中运行以下命令：

```sh
xcode-select --install
sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/* /usr/local/include/
```

### Ubuntu 18.04: 与 arm_none_eabi_gcc 相关的编译错误

与 `arm_none_eabi_gcc` 相关的构建问题可能是由于 g++ 工具链安装损坏导致的。
你可以通过检查缺失依赖项来验证这一点：

```sh
arm-none-eabi-gcc --version
arm-none-eabi-g++ --version
arm-none-eabi-gdb --version
arm-none-eabi-size --version
```

缺少依赖项的 bash 输出示例如下：

```sh
arm-none-eabi-gdb --version
arm-none-eabi-gdb: command not found
```

可以通过移除并[重新安装编译器](https://askubuntu.com/questions/1243252/how-to-install-arm-none-eabi-gdb-on-ubuntu-20-04-lts-focal-fossa)来解决。

### Ubuntu 18.04: Visual Studio Code 无法监控此大型工作区的文件更改

请参见 [Visual Studio Code IDE (VSCode) > 故障排除](../dev_setup/vscode.md#troubleshooting)。

### 无法导入 Python 包

运行 `make px4_sitl jmavsim` 命令时出现 "Failed to import" 错误表示某些 Python 包未安装（在预期位置）。

```sh
Failed to import jinja2: No module named 'jinja2'
You may need to install it using:
    pip3 install --user jinja2
```

如果你已安装这些依赖项，这可能是因为计算机上存在多个 Python 版本（例如 Python 2.7.16 Python 3.8.3），而构建工具链使用的版本中未安装模块。

你可以通过显式安装依赖项来修复此问题，如下所示：

```sh
pip3 install --user pyserial empty toml numpy pandas jinja2 pyyaml pyros-genmsg packaging
```

## PX4 Make 构建目标

前几节展示了如何调用 _make_ 构建不同目标、启动模拟器、使用IDE等。本节将展示如何构建 _make_ 选项以及如何查找可用选项。

调用 _make_ 时使用特定配置和初始化文件的完整语法为：

```sh
make [VENDOR_][MODEL][_VARIANT] [VIEWER_MODEL_DEBUGGER_WORLD]
```

**VENDOR_MODEL_VARIANT**:（也称为 `CONFIGURATION_TARGET`）

- **VENDOR:** 板载制造商：`px4`, `aerotenna`, `airmind`, `atlflight`, `auav`, `beaglebone`, `intel`, `nxp` 等。
  Pixhawk系列板载的厂商名为 `px4`。
- **MODEL:** 板载 "模型"：`sitl`, `fmu-v2`, `fmu-v3`, `fmu-v4`, `fmu-v5`, `navio2` 等。
- **VARIANT:** 表示特定配置：例如 `bootloader`, `cyphal`，这些配置包含在 `default` 中未出现的组件。
  最常见的是 `default`，可省略。

:::tip
你可以使用以下命令获取所有可用的 `CONFIGURATION_TARGET` 选项列表：

```sh
make list_config_targets
```

:::

**VIEWER_MODEL_DEBUGGER_WORLD:**

- **VIEWER:** 要启动和连接的模拟器("查看器")：`gz`, `gazebo`, `jmavsim`, `none` <!-- , ?airsim -->

  :::tip
  如果你想启动PX4并等待模拟器（jmavsim、Gazebo、Gazebo Classic 或其他模拟器），可以使用 `none`。
  例如，`make px4_sitl none_iris` 会启动PX4但不带模拟器（但使用iris机体）。
  :::

- **MODEL:** 使用的 _机体_ 模型（例如 `iris` (_默认_), `rover`, `tailsitter` 等），将由模拟器加载。
  环境变量 `PX4_SIM_MODEL` 将被设置为所选模型，该模型随后在[startup script](../simulation/index.md#startup-scripts)中用于选择合适参数。
- **DEBUGGER:** 使用的调试器：`none` (_默认_), `ide`, `gdb`, `lldb`, `ddd`, `valgrind`, `callgrind`。
  更多信息请参见 [Simulation Debugging](../debug/simulation_debugging.md)。
- **WORLD:** (仅限Gazebo Classic)。
  设置加载的 [世界](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/main/worlds)（位于[PX4-Autopilot/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/main/worlds)）。
  默认为 [empty.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/empty.world)。
  更多信息请参见 [Gazebo Classic > Loading a Specific World](../sim_gazebo_classic/index.md#loading-a-specific-world)。

:::tip
你可以使用以下命令获取所有可用的 `VIEWER_MODEL_DEBUGGER_WORLD` 选项列表：

```sh
make px4_sitl list_vmd_make_targets
```

:::

::: info

- `CONFIGURATION_TARGET` 和 `VIEWER_MODEL_DEBUGGER` 中的大部分值都有默认值，因此是可选的。
  例如，`gazebo-classic` 等效于 `gazebo-classic_iris` 或 `gazebo-classic_iris_none`。
- 如果你想在两个设置之间指定默认值，可以使用三个下划线。
  例如，`gazebo-classic___gdb` 等效于 `gazebo-classic_iris_gdb`。
- 你可以为 `VIEWER_MODEL_DEBUGGER` 使用 `none` 值来启动PX4并等待模拟器。
  例如使用 `make px4_sitl_default none` 启动PX4，然后使用 `./Tools/simulation/jmavsim/jmavsim_run.sh -l` 启动jMAVSim。

:::

`VENDOR_MODEL_VARIANT` 选项映射到PX4源代码树中特定的_px4board_配置文件，位于 [/boards](https://github.com/PX4/PX4-Autopilot/tree/main/boards) 目录下。
具体来说，`VENDOR_MODEL_VARIANT` 映射到配置文件 **boards/VENDOR/MODEL/VARIANT.px4board**
（例如，`px4_fmu-v5_default` 对应 [boards/px4/fmu-v5/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/default.px4board)）。

其他make目标将在相关章节讨论：

- `bloaty_compare_master`: [Binary Size Profiling](../debug/binary_size_profiling.md)
- ...

## 固件版本与 Git 标签

_PX4 固件版本_ 和 _自定义固件版本_ 通过 MAVLink [AUTOPILOT_VERSION](https://mavlink.io/en/messages/common.html#AUTOPILOT_VERSION) 消息发布，并在 _QGroundControl_ **设置 > 概要** 机体面板中显示：

![固件信息](../../assets/gcs/qgc_setup_summary_airframe_firmware.jpg)

这些信息在构建时从您仓库树中当前的 _git 标签_ 提取。
git 标签应格式化为 `<PX4版本>-<厂商版本>`（例如上图中的标签设置为 `v1.8.1-2.22.1`）。

:::warning
如果使用了不同的 git 标签格式，版本信息可能无法正确显示。
:::