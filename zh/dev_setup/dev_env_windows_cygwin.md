# Windows 开发环境 (基于 Cygwin)

:::warning
此开发环境由[社区支持和维护](../advanced/community_supported_dev_env.md)。
它可能与当前版本的 PX4 兼容或不兼容。

此前推荐的工具链由于打包问题，与 PX4 v1.12 及更高版本不兼容。
应优先使用 [Windows WSL2 基于的开发环境](../dev_setup/dev_env_windows_wsl.md)。

关于核心开发团队支持的环境和工具，请参阅 [工具链安装](../dev_setup/dev_env.md)。
:::

以下说明解释了如何在 Windows 10 上设置 (基于 Cygwin 的) PX4 开发环境。
此环境可用于构建 PX4 以支持：

- Pixhawk 及其他基于 NuttX 的硬件
- [jMAVSim 仿真](../sim_jmavsim/index.md)

<a id="installation"></a>

## 安装说明

1. 从以下地址下载最新版本的即用型 MSI 安装程序：[Github 发布](https://github.com/PX4/windows-toolchain/releases) 或 [Amazon S3](https://s3-us-west-2.amazonaws.com/px4-tools/PX4+Windows+Cygwin+Toolchain/PX4+Windows+Cygwin+Toolchain+0.9.msi) (快速下载)。
1. 运行安装程序，选择所需的安装位置，进行安装：

   ![jMAVSimOnWindows](../../assets/toolchain/cygwin_toolchain_installer.png)

1. 在安装结束时勾选复选框以 _克隆 PX4 仓库、构建并运行 jMAVSim 仿真_ (这将简化入门流程)。

   ::: info
   如果错过了这一步，将需要[手动克隆 PX4-Autopilot 仓库](#getting-started)。
   :::

:::warning
编写本文时，安装程序缺少一些依赖项（目前尚无法重新构建添加这些依赖项 - 请参阅 [PX4-windows-toolchain#31](https://github.com/PX4/PX4-windows-toolchain/issues/31)）。

要自行添加这些依赖项：

1. 浏览到工具链安装目录（默认 **C:\\PX4\\**）
1. 双击运行 **run-console.bat** 启动类 Linux 的 Cygwin bash 控制台
1. 在控制台中输入以下命令：

   ```sh
   pip3 install --user kconfiglib jsonschema future
   ```

:::

## 入门指南

工具链使用一个特殊配置的控制台窗口（通过运行 **run-console.bat** 脚本启动），您可以在此调用常规的 PX4 构建命令：

1. 浏览到工具链安装目录（默认 **C:\\PX4\\**）
1. 双击运行 **run-console.bat** 启动类 Linux 的 Cygwin bash 控制台（必须使用此控制台构建 PX4）。
1. 在控制台中克隆 PX4 PX4-Autopilot 仓库：

   ::: info
   如果勾选了安装程序选项 _克隆 PX4 仓库、构建并运行 jMAVSim 仿真_，请跳过此步骤。
   克隆操作只需执行一次！
   :::

   ```sh
   # 将 PX4-Autopilot 仓库克隆到主目录并并行加载子模块
   git clone --recursive -j8 https://github.com/PX4/PX4-Autopilot.git
   ```

   现在您可以使用控制台/PX4-Autopilot 仓库来构建 PX4。

1. 例如，运行 JMAVSim：

   ```sh
   # 进入 PX4-Autopilot 仓库
   cd Firmware
   # 构建并运行 SITL 仿真以测试配置
   make px4_sitl jmavsim
   ```

   控制台将显示以下内容：

   ![jMAVSimOnWindows](../../assets/simulation/jmavsim/jmavsim_windows_cygwin.png)

## 下一步

完成命令行工具链设置后：

- 安装 [QGroundControl 每日构建](../dev_setup/qgc_daily_build.md)
- 继续 [构建说明](../dev_setup/building_px4.md)

## 故障排除

### 文件监控工具与工具链速度

杀毒软件和其他后台文件监控工具会显著减缓工具链安装和 PX4 构建速度。

您可能希望在构建期间暂时停止这些工具（自行承担风险）。

### Windows 与 Git 特殊情况

#### Windows CR+LF 与 Unix LF 换行符

我们建议您通过此工具链强制所有仓库使用 Unix 风格的 LF 换行符（并使用保存时保留它们的编辑器，如 Eclipse 或 VS Code）。
源文件的编译在本地检查出 CR+LF 换行符时也可以工作，但在 Cygwin 中（例如执行 shell 脚本）需要 Unix 换行符（否则会收到类似 `$'\r': Command not found.` 的错误）。
幸运的是，git 可以为您执行此操作，您只需在仓库根目录执行以下两个命令：

```sh
git config core.autocrlf false
git config core.eol lf
```

如果您在多个仓库中使用此工具链，也可以为您的机器全局设置这两个配置：

```sh
git config --global ...
```

不建议这样做，因为它可能会影响您 Windows 机器上的其他（无关）git 使用。

#### Unix 权限执行位

在 Unix 系统中，每个文件的权限中有一个标志，告诉操作系统该文件是否可以被执行。
Cygwin 下的 _git_ 支持并关心这个标志（尽管 Windows NTFS 文件系统不使用它）。
这通常会导致 _git_ 发现权限的"假阳性"差异。
生成的差异可能如下所示：

```sh
diff --git ...
old mode 100644
new mode 100755
```

我们建议在 Windows 上全局禁用权限检查以避免此问题：

```sh
# 为整个机器全局禁用执行位检查
git config --global core.fileMode false
```

对于因本地配置而出现此问题的现有仓库，此外还需：

```sh
# 移除该仓库的本地选项以应用全局设置
git config --unset core.filemode

# 移除所有子模块的本地选项
git submodule foreach --recursive git config --unset core.filemode
```

<!--
构建/更新此工具链的说明请参阅 [Windows Cygwin 开发环境 (维护说明)](../dev_setup/dev_env_windows_cygwin_packager_setup.md)
-->