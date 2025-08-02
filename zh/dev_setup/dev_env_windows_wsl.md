# Windows开发环境（基于WSL2）

以下说明解释了如何在Windows 10或11上设置PX4开发环境，该环境在[WSL2](https://docs.microsoft.com/en-us/windows/wsl/about)中运行Ubuntu Linux。

此环境可用于构建PX4：

- [Pixhawk和其他NuttX架构的硬件](../dev_setup/building_px4.md#nuttx-pixhawk-based-boards)
- [Gazebo仿真](../sim_gazebo_gz/index.md)
- [Gazebo-Classic仿真](../sim_gazebo_classic/index.md)

:::tip
此环境设置由PX4开发团队支持。
理论上该环境应能构建任何在Ubuntu上可构建的目标。
上述列表为经过定期测试的目标。
:::

## 概述

[Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/about) ([WSL2](https://docs.microsoft.com/en-us/windows/wsl/compare-versions)) 允许用户在 Windows 上安装并运行 [Ubuntu开发环境](../dev_setup/dev_env_linux_ubuntu.md)，其功能几乎等同于在 Linux 电脑上运行。

通过该环境，开发者可以：

- 在 WSL Shell 中构建任何由 [Ubuntu开发环境](../dev_setup/dev_env_linux_ubuntu.md) 支持的模拟器或硬件目标（Ubuntu 是经过最充分验证和支持的 PX4 开发平台）。
- 在运行于 Windows 上的 [Visual Studio Code](dev_env_windows_wsl.md#visual-studio-code-integration) 中调试代码。
- 使用运行在 WSL 中的 _Linux版QGroundControl_ 监控 _模拟器_。
  Linux版QGroundControl 会自动连接到模拟器。

如果需要：

- [更新固件](#刷写飞行控制板) 到真实机体。
- 监控真实机体。
  注意：你也可以用它监控模拟器，但必须手动[连接到运行在 WSL 中的模拟器](#QGroundControl on Windows)。

::: info
无法从 WSL 内连接 USB 设备，因此不能通过命令行的 [`upload`](../dev_setup/building_px4.md#uploading-firmware-flashing-the-board) 选项，或通过 _Linux版QGroundControl_ 更新固件。
:::

::: info
该方法与[Windows VM-Hosted Toolchain](../dev_setup/dev_env_windows_vm.md)中描述的在 _个人虚拟机_ 中安装 PX4 的方式相似。WSL2 的优势在于其虚拟机与 Windows 深度集成，由系统管理且性能优化。
:::

## 安装

### 安装 WSL2

在 Windows 10 或 11 新系统上安装带有 Ubuntu 的 WSL2：

1. 确保计算机的 BIOS 中启用了虚拟化功能。  
   通常分别称为 "Virtualization Technology"、"Intel VT-x" 或 "AMD-V"。
1. 以管理员身份运行 _cmd.exe_。  
   可通过按下开始键，输入 `cmd`，右键点击 "命令提示符" 并选择 **以管理员身份运行**。
1. 执行以下命令安装 WSL2 及特定 Ubuntu 版本：

   - 默认版本（Ubuntu 22.04）：

     ```sh
     wsl --install
     ```

   - Ubuntu 20.04 ([Gazebo-Classic Simulation](../sim_gazebo_classic/index.md))

     ```sh
     wsl --install -d Ubuntu-20.04
     ```

   - Ubuntu 22.04 ([Gazebo Simulation](../sim_gazebo_gz/index.md))

     ```sh
     wsl --install -d Ubuntu-22.04
     ```

   ::: info
   您也可以从商店安装 [Ubuntu 20.04](https://www.microsoft.com/store/productId/9MTTCL66CPXJ) 和 [Ubuntu 22.04](https://www.microsoft.com/store/productId/9PN20MSR04DW)，这允许通过常规 Windows 添加/删除设置来卸载应用程序：
   :::

1. WSL 会提示您为 Ubuntu 安装设置用户名和密码。  
   请记录这些凭据，后续操作将需要使用！

此时命令提示符已切换为新安装的 Ubuntu 环境终端。

### 打开 WSL Shell

所有安装和构建 PX4 的操作必须在 WSL Shell 中进行（您可以使用安装 WSL2 时的相同 Shell 或打开新的 Shell）。

如果您使用 [Windows Terminal](https://learn.microsoft.com/en-us/windows/terminal/install)，如图所示可以打开已安装的 WSL 环境 Shell，通过关闭标签页即可退出：

![Windows Terminal 显示如何选择 Ubuntu Shell](../../assets/toolchain/wsl_windows_terminal.png)

通过命令提示符打开 WSL Shell：

1. 打开命令提示符：

   - 按下 Windows **开始** 键。
   - 输入 `cmd` 并按下 **Enter** 打开提示符。

1. 要启动 WSL 并访问 WSL Shell，请执行命令：

   ```sh
   wsl -d <distribution_name>
   ```

   例如：

   ```sh
   wsl -d Ubuntu
   ```

   ```sh
   wsl -d Ubuntu-20.04
   ```

   如果您只有一个 Ubuntu 版本，可以直接使用 `wsl`。

输入以下命令首先关闭 WSL Shell，然后关闭 WSL：

```sh
exit
wsl -d <distribution_name> --shutdown
```

或者在输入 `exit` 后直接关闭提示符即可。

### 安装 PX4 工具链

接下来在 WSL2 环境中下载 PX4 源代码，并使用常规的 _Ubuntu 安装脚本_ 设置开发环境。  
这将安装用于 Gazebo Classic 模拟和 Pixhawk/NuttX 硬件的工具链。

要安装开发工具链：

1. [打开 WSL2 Shell](#打开 WSL Shell)（如果尚未关闭，可以使用安装 WSL2 时的相同 Shell）。
1. 执行命令 `cd ~` 切换到 WSL 的家目录进行后续操作。

   :::warning
   这非常重要！  
   如果您在 WSL 文件系统之外的位置工作，会遇到执行速度极慢和访问权限错误等问题。
   :::

1. 使用 `git`（已安装在 WSL2 中）下载 PX4 源代码：

   ```sh
   git clone https://github.com/PX4/PX4-Autopilot.git --recursive
   ```

   ::: info
   源代码中的环境设置脚本通常适用于最近的 PX4 版本。  
   如果使用较旧版本的 PX4，可能需要 [获取与您版本对应的源代码](../contribute/git_examples.md#get-a-specific-release)。
   :::

1. 运行 **ubuntu.sh** 安装脚本，并在脚本执行过程中确认所有提示：

   ```sh
   bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
   ```

   ::: info
   这将安装用于构建 Pixhawk 和 Gazebo 或 Gazebo Classic 目标的工具：  

   - 您可以使用 `--no-nuttx` 和 `--no-sim-tools` 选项来省略 NuttX 和/或模拟工具。
   - 其他 Linux 构建目标尚未测试（您可以通过在 [Ubuntu 开发环境](../dev_setup/dev_env_linux_ubuntu.md) 中输入相应命令尝试在 WSL Shell 中使用）。
     :::

1. 脚本完成后重启 "WSL 计算机"（退出 Shell、关闭 WSL、重新启动 WSL）：

   ```sh
   exit
   wsl --shutdown
   wsl
   ```

1. 切换到 WSL 家目录中的 PX4 仓库：

   ```sh
   cd ~/PX4-Autopilot
   ```

1. 构建 PX4 SITL 目标并测试您的环境：

   ```sh
   make px4_sitl
   ```

更多构建选项请参见 [构建 PX4 软件](../dev_setup/building_px4.md)。

## Visual Studio Code 集成

VS Code 在 Windows 上与 WSL 集成良好。

要设置集成：

1. [下载](https://code.visualstudio.com/) 并在 Windows 上安装 Visual Studio Code (VS Code)，
2. 打开 _VS Code_。
3. 安装名为 [Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) (marketplace) 的扩展
4. [打开 WSL shell](#打开 WSL Shell)
5. 在 WSL shell 中切换到 PX4 文件夹：

   ```sh
   cd ~/PX4-Autopilot
   ```

6. 在 WSL shell 中启动 VS Code：

   ```sh
   code .
   ```

   这将完全集成 WSL shell 打开 IDE。

   确保始终以 Remote WSL 模式打开 PX4 仓库。

7. 下次开发 WSL2 时，可以通过选择 **Open Recent**（如下所示）轻松再次以 Remote WSL 模式打开。
   这将为您启动 WSL。

   ![](../../assets/toolchain/vscode/vscode_wsl.png)

   但请注意，WSL 虚拟机的 IP 地址已更改，因此无法从 Windows 的 QGC 监控仿真（仍可以使用 Linux 的 QGC 进行监控）
   
## QGroundControl

您可以在WSL或Windows中运行QGroundControl以连接到正在运行的模拟器。  
如果您需要[刷写飞行控制板固件](#刷写飞行控制板)，只能通过Windows版QGroundControl完成。

### QGroundControl in WSL

设置和使用QGroundControl最简单的方式是将Linux版本下载到您的WSL中。  
您可以通过WSL shell完成此操作：

1. 在浏览器中访问QGC的[Ubuntu下载页面](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html#ubuntu)  
1. 右键点击 **QGroundControl.AppImage** 链接，选择"复制链接地址"。  
   链接格式类似 _https://d176td9ibe4jno.cloudfront.net/builds/master/QGroundControl.AppImage_  
1. [打开WSL shell](#打开 WSL Shell) 并执行以下命令下载appimage并设置可执行权限（替换对应URL）：

   ```sh
   cd ~
   wget <the_copied_AppImage_URL>
   chmod +x QGroundControl.AppImage
   ```

1. 运行QGroundControl:

   ```sh
   ./QGroundControl.AppImage
   ```

QGroundControl 将启动并自动连接到正在运行的模拟器，允许您监控和控制机体。  
由于WSL无法访问串口设备，您将无法使用它安装PX4固件。

### QGroundControl on Windows

如果需要使用PX4生成的固件更新硬件，需要在Windows上[安装QGroundControl](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html#windows)。  

以下是连接到WSL中运行的模拟器的步骤：  
1. [打开WSL shell](#打开 WSL Shell)  
2. 通过运行命令 `ip addr | grep eth0` 查询WSL虚拟机的IP地址：

   ```sh
   $ ip addr | grep eth0

   6: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
       inet 172.18.46.131/20 brd 172.18.47.255 scope global eth0
   ```

   将 `eth0` 接口 `inet` 地址的前半部分复制到剪贴板。  
   本例中为：`172.18.46.131`。

3. 在QGC中进入 **Q > 应用程序设置 > 通信链接**  
4. 为上述IP地址的 `18570` 端口添加一个名为"WSL"的UDP链接。  
5. 保存设置并连接。

::: info
每次WSL重启后都需要在QGC中更新WSL通信链接（因为它具有动态IP地址）。
:::

## 刷写飞行控制板

使用自定义构建的 PX4 二进制文件进行刷写必须通过 [QGroundControl for Windows](#QGroundControl on Windows) 完成。

::: info
WSL2 本身不提供对连接到计算机的 Pixhawk 飞行控制器等串口/USB 设备的直接访问。
这意味着你无法将运行在 WSL2 中的 QGC 连接到飞行控制器以安装固件，或使用 `upload` 命令 [在构建时上传固件](../dev_setup/building_px4.md#uploading-firmware-flashing-the-board)。
正确的做法是将 [QGroundControl for Windows](#QGroundControl on Windows) 连接到运行在 WSL2 中的 PX4 和飞行控制器，以上传固件。
:::

按照以下步骤在 WSL 中刷写自定义二进制文件：

1. 如果尚未在 WSL 中构建二进制文件（例如通过 [WSL shell](dev_env_windows_wsl.md#opening-a-wsl-shell) 并运行以下命令）：

   ```sh
   cd ~/PX4-Autopilot
   make px4_fmu-v5
   ```

   ::: tip
   请为你的板卡使用正确的 `make` 目标。
   `px4_fmu-v5` 可用于 Pixhawk 4 板。
   :::

1. 如果已连接 Pixhawk 板的 USB 线，请将其从计算机上拔下。
1. 打开 QGC 并导航到 **Q > Vehicle Setup > Firmware**。
1. 通过 USB 连接你的 Pixhawk 板。
1. 连接后选择 "PX4 Flight Stack"，勾选 **Advanced settings**，然后从下方下拉菜单中选择 _Custom firmware file ..._。
1. 继续选择在 WSL 中构建的固件二进制文件。

   在打开的对话框中，左侧面板中寻找带有企鹅图标的 "Linux" 位置。
   通常位于最底部。
   选择路径中的文件：`Ubuntu\home\{your WSL user name}\PX4-Autopilot\build\{your build target}\{your build target}.px4`

   ::: info
   可以将该文件夹添加到收藏夹以便下次快速访问。
   :::

1. 开始刷写。

更多详情请参阅 [安装 PX4 主版、测试版或自定义固件（加载固件）](../config/firmware.md#installing-px4-main-beta-or-custom-firmware)。

## 故障排除

如果您的配置遇到任何问题，请检查当前的[Microsoft WSL 安装文档](https://learn.microsoft.com/en-us/windows/wsl/install)。

我们还建议您安装最新的 Windows GPU 驱动，并在 Ubuntu 环境中安装最新版本的[kisak mesa](https://launchpad.net/~kisak/+archive/ubuntu/kisak-mesa)，以便大多数 OpenGL 功能可以被模拟：

```sh
sudo add-apt-repository ppa:kisak/kisak-mesa
sudo apt update
sudo apt upgrade
```