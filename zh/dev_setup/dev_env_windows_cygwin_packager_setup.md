# Windows Cygwin 开发环境（维护说明）

:::warning
此开发环境由[社区支持和维护](../advanced/community_supported_dev_env.md)。
它可能与当前PX4版本兼容或不兼容。

有关核心开发团队支持的环境和工具的信息，请参阅[工具链安装](../dev_setup/dev_env.md)。
:::

本主题说明如何构建和扩展不再支持的 [基于Cygwin的Windows开发环境](../dev_setup/dev_env_windows_cygwin.md) 的开发环境。

## 附加信息

### 功能/问题

以下功能已知可正常工作（版本2.0）：

- 构建和运行SITL与jMAVSim，性能显著优于虚拟机（生成原生Windows二进制文件 **px4.exe**）。
- 构建和上传NuttX构建（例如：px4_fmu-v2和px4_fmu-v4）
- 使用_astyle_进行样式检查（支持命令：`make format`）
- 命令行自动补全
- 非侵入式安装程序！安装程序不会影响您的系统和全局路径（仅修改所选安装目录例如 \*\*C:\PX4\*\* 并使用临时本地路径）。
- 安装程序支持更新到新版本，同时保留工具链文件夹内的个人修改

缺失功能：

- 模拟：Gazebo和ROS不支持。
- 仅支持NuttX和JMAVSim/SITL构建。
- [已知问题](https://github.com/orgs/PX4/projects/6)（也可用于报告问题）。

### Shell 脚本安装

您也可以通过Github项目中的shell脚本安装环境。

1. 确保已安装 [Git for Windows](https://git-scm.com/download/win)。
1. 将仓库 https://github.com/PX4/windows-toolchain 克隆到您希望安装工具链的目录。通过打开 `Git Bash` 并执行以下命令可获得默认位置和命名：

   ```sh
   cd /c/
   git clone https://github.com/PX4/windows-toolchain PX4
   ```

1. 如果要安装所有组件，请导航到新克隆的文件夹并双击位于 `toolchain` 文件夹中的脚本 `install-all-components.bat`。如果只需要某些组件并希望节省网络流量或磁盘空间，可以导航到不同组件文件夹（例如 `toolchain\cygwin64`）并点击 **install-XXX.bat** 脚本以仅获取特定内容。
1. 继续 [入门指南](../dev_setup/dev_env_windows_cygwin.md#getting-started)。

### 手动安装（针对工具链开发者）

本节描述如何手动设置Cygwin工具链，同时指向基于脚本安装仓库中的相应脚本。
结果应与使用脚本或MSI安装程序相同。

::: info
工具链由社区维护，因此这些说明可能无法涵盖所有未来更改的每个细节。
:::

1. 创建 _文件夹_：**C:\PX4\*\*, **C:\PX4\toolchain\*\* 和 \*\*C:\PX4\home\*\*
1. 从 [官方Cygwin网站](https://cygwin.com/install.html) 下载 _Cygwin安装文件_ [setup-x86_64.exe](https://cygwin.com/setup-x86_64.exe)
1. 运行下载的安装文件
1. 在向导中选择安装到文件夹：\*\*C:\PX4\toolchain\cygwin64\*\*
1. 选择安装默认Cygwin基础和以下附加包的最新可用版本：

   - **Category:Packagename**
   - Devel:cmake (3.3.2不会生成弃用警告，3.6.2可用但有警告)
   - Devel:gcc-g++
   - Devel:gdb
   - Devel:git
   - Devel:make
   - Devel:ninja
   - Devel:patch
   - Editors:xxd
   - Editors:nano (除非您是vim专家)
   - Python:python2
   - Python:python2-pip
   - Python:python2-numpy
   - Python:python2-jinja2
   - Python:python2-pyyaml
   - Python:python2-cerberus
   - Archive:unzip
   - Utils:astyle
   - Shells:bash-completion
   - Web:wget

   ::: info
   不要选择过多不在此列表中的包，有些包会产生冲突并破坏构建。
   :::

   ::: info
   这就是 [cygwin64/install-cygwin-px4.bat](https://github.com/MaEtUgR/PX4Toolchain/blob/master/toolchain/cygwin64/install-cygwin-px4.bat) 所做的事情。
   :::

1. 编写或复制 **批处理脚本** [`run-console.bat`](https://github.com/MaEtUgR/PX4Toolchain/blob/master/run-console.bat) 和 [`setup-environment.bat`](https://github.com/PX4/windows-toolchain/blob/master/toolchain/scripts/setup-environment.bat)。

   通过准备好的批处理脚本启动所有开发工具的原因是它们预先配置启动程序以使用工具链文件夹内的本地、可移植Cygwin环境。
   这是通过始终首先调用脚本 [**setup-environment.bat**](https://github.com/PX4/windows-toolchain/blob/master/toolchain/scripts/setup-environment.bat) 并随后调用所需应用程序（如控制台）来实现的。

   脚本 [setup-environment.bat](https://github.com/PX4/windows-toolchain/blob/master/toolchain/scripts/setup-environment.bat) 本地设置环境变量，包括工作区路径（`PATH`）、工作区目录（`WORKSPACE_DIR`）和项目根目录（`PROJECT_ROOT`）。

1. 确保所有已安装组件的二进制文件夹都在由 [**setup-environment.bat**](https://github.com/PX4/windows-toolchain/blob/master/toolchain/scripts/setup-environment.bat) 配置的 `PATH` 变量中正确列出。