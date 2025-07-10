

# Visual Studio Code 集成开发环境 (VSCode)

[Visual Studio Code](https://code.visualstudio.com/) 是一个功能强大的跨平台源代码编辑器/集成开发环境，可用于 Ubuntu、Windows 和 macOS 上的 PX4 开发。

使用 VSCode 进行 PX4 开发有以下优势：

- 设置工作环境真的只需几分钟。
- 丰富的扩展生态系统，支持 PX4 开发所需的各种工具：C/C++（具备完善的 _cmake_ 集成）、_Python_、_Jinja2_、ROS 消息，甚至 DroneCAN DSDL。
- 优秀的 GitHub 集成。

本主题将介绍如何设置开发环境并开始开发。

::: info
虽然还有其他功能强大的集成开发环境，但它们通常需要更多工作量才能与 PX4 集成。
使用 _VScode_ 时，配置信息存储在 PX4/PX4-Autopilot 树形结构中（[PX4-Autopilot/.vscode](https://github.com/PX4/PX4-Autopilot/tree/main/.vscode)），因此设置过程只需添加项目文件夹即可完成。
:::

## 前置条件

您必须已经为您的平台安装了命令行[ PX4 开发者环境 ]( ../dev_setup/dev_env.md )，并下载了 _固件_ 源代码仓库。

## 安装与设置

1. [下载并安装 VSCode](https://code.visualstudio.com/)（系统会根据您的操作系统提供正确的版本）。
1. 打开 VSCode 并添加 PX4 源代码：

   - 在欢迎页面选择 _Open folder ..._ 选项（或通过菜单：**File > Open Folder**）：

     ![Open Folder](../../assets/toolchain/vscode/welcome_open_folder.jpg)

   - 将弹出文件选择对话框。
     选择 **PX4-Autopilot** 目录然后点击 **OK**。

   项目文件和配置将加载到 _VSCode_ 中。

1. 在 _This workspace has extension recommendations_ 提示上点击 **Install All**（此提示会出现在 IDE 右下角）。
   ![Install extensions](../../assets/toolchain/vscode/prompt_install_extensions.jpg)

   VSCode 将在左侧打开 _Extensions_ 面板，您可查看安装进度。

   ![PX4 loaded into VSCode Explorer](../../assets/toolchain/vscode/installing_extensions.jpg)

1. 可能会在右下角出现多个通知/提示

   :::tip
   如果提示消失，点击底部蓝色条右侧的"alarm"图标。
   :::

   - 如果提示安装新版本 _cmake_：
     - 选择 **No**（正确版本已随 [PX4 开发环境](../dev_setup/dev_env.md) 安装）。
   - 如果提示登录 _github.com_ 并添加凭据：
     - 这由您决定！这将提供 GitHub 与 IDE 的深度集成，可能会简化您的工作流程。
   - 其他提示为可选，如果看起来有用可以安装。 <!-- perhaps add screenshot of these prompts -->

<a id="building"></a>

## 构建 PX4

要构建:

1. 选择你的构建目标("cmake构建配置"):

   - 当前的_cmake构建目标_显示在底部蓝色的_配置_栏中(如果这已经是你的目标配置，可跳到下一步)。
     ![选择Cmake构建目标](../../assets/toolchain/vscode/cmake_build_config.jpg)

     ::: info
     选择的cmake目标会影响[构建/调试](#debugging)时提供的目标选项(例如进行硬件调试时必须选择类似`px4_fmu-v6`的硬件目标)。
     :::

   - 点击配置栏中的目标以显示其他选项，然后选择你需要的配置(这会替换当前选中的目标)。
   - _Cmake_ 会随后配置你的项目(右下角会显示通知)。
     ![Cmake配置项目](../../assets/toolchain/vscode/cmake_configuring_project.jpg)
   - 等待配置完成。
     完成后通知会消失，并显示构建位置：
     ![Cmake配置项目](../../assets/toolchain/vscode/cmake_configuring_project_done.jpg)。

1. 你可以在配置栏中启动构建(选择**Build**或**Debug**)。
   ![运行调试或构建](../../assets/toolchain/vscode/run_debug_build.jpg)

完成至少一次构建后，你现在可以使用[代码补全](#code completion)和其它_VSCode_功能。

## 调试

<a id="debugging_sitl"></a>

### SITL调试

在SITL上调试PX4：

1. 在侧边栏选择调试图标（用红色标记）以显示调试面板。  
   ![运行调试](../../assets/toolchain/vscode/vscode_debug.jpg)

1. 然后从顶部栏的调试下拉菜单（紫色框）中选择你的调试目标（例如 _Debug SITL (Gazebo Iris)_）。

   ::: info
   提供的调试目标（紫色框）与你的构建目标（底部栏的黄色框）匹配。  
   例如，要调试SITL目标，你的构建目标必须包含SITL。
   :::

1. 点击调试顶部栏的“播放”箭头（调试目标旁边，粉色框）开始调试。

调试过程中可以设置断点、逐步执行代码，并以正常方式开发。

### 硬件调试

[SWD调试端口](../debug/swd_debug.md)中的说明解释了如何连接到常见飞控的SWD接口（例如使用Dronecode或Blackmagic探针）。

连接到SWD接口后，VSCode中的硬件调试与[SITL调试](#debugging_sitl)相同，区别在于你需要选择适合自己调试器类型（和固件）的调试目标 - 例如 `jlink (px4_fmu-v5)`。

:::tip
要查看 `jlink` 选项，您必须已选择了 [cmake构建固件的目标](#building-px4)。
:::

![显示不同探测器选项的硬件目标图像](../../assets/toolchain/vscode/vscode_hardware_debugging_options.png)

<a id="code completion"></a>

## 代码补全

为了使代码补全功能正常工作（以及其它IntelliSense相关功能），您需要一个有效的配置，并且已经[构建代码](#building)。

完成上述操作后，无需执行其他任何操作；工具链将在您输入时自动提供符号提示。

![IntelliSense](../../assets/toolchain/vscode/vscode_intellisense.jpg)

## 故障排除

本节包含有关设置和构建错误的指导。

### Ubuntu 18.04: "Visual Studio Code 无法监视此大型工作区中的文件更改"

该错误在启动时出现。
某些系统会对应用程序施加最多 8192 个文件句柄的上限，这意味着 VSCode 可能无法检测到 `/PX4-Autopilot` 中的文件修改。

您可以增加此限制以避免该错误，但会以增加内存消耗为代价。
请按照 [此处的说明](https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc) 操作。
设置值为 65536 即可足够。