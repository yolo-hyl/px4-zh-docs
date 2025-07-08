# MacOS 开发环境

以下说明为 macOS 设置 PX4 开发环境。
该环境可用于构建 PX4：

- Pixhawk 及其他基于 NuttX 的硬件
- [Gazebo Classic 仿真](../sim_gazebo_classic/index.md)

:::tip
该设置由 PX4 开发团队支持。
若要构建其他目标，您需要使用 [不同操作系统](../dev_setup/dev_env.md#supported-targets)（或 [不受支持的开发环境](../advanced/community_supported_dev_env.md)）。
:::

## 视频指南

<lite-youtube videoid="tMbMGiMs1cQ" title="在 macOS 上设置 PX4 开发环境"/>

## 基础环境

"基础" macOS 设置安装构建固件所需的工具，并包含安装/使用仿真器时需要的通用工具。

### 环境设置

:::details Apple Silicon MacBook 用户！
如果您使用的是 Apple M1、M2 等 MacBook，需通过以下步骤设置 x86 终端：

1. 在实用工具文件夹中找到终端应用（**访达 > 前往菜单 > 实用工具**）
2. 选择 _Terminal.app_，右键点击，选择 **复制**
3. 将复制的终端应用重命名为 _x86 Terminal_
4. 现在选择重命名的 _x86 Terminal_ 应用，右键选择 **获取信息**
5. 勾选 **使用 Rosetta 打开**，然后关闭窗口
6. 按照常规方式运行 _x86 Terminal_，将完全支持当前 PX4 工具链
   :::

首先设置环境

1. 通过向 `~/.zshenv` 文件添加以下行来增加打开文件数（如文件不存在则创建）：

   ```sh
   echo ulimit -S -n 2048 >> ~/.zshenv
   ```

   ::: info
   如果不执行此操作，构建工具链可能会报告错误：`"LD: too many open files"`
   :::

1. 通过向 `~/.zshenv` 添加以下行强制使用 Python 3：

   ```sh
   # 将 pip3 指向 macOS 系统 Python 3 pip
   alias pip3=/usr/bin/pip3
   ```

### 通用工具

要设置环境以构建 Pixhawk/NuttX 硬件（并安装使用仿真器所需的通用工具）：

1. 按照这些 [安装说明](https://brew.sh) 安装 Homebrew。
1. 在终端运行以下命令安装通用工具：

   ```sh
   brew tap PX4/px4
   brew install px4-dev
   ```

1. 安装所需的 Python 包：

   ```sh
   # 使用 pip3 安装所需包
   python3 -m pip install --user pyserial empty toml numpy pandas jinja2 pyyaml pyros-genmsg packaging kconfiglib future jsonschema
   # 如果出现权限错误，您的 Python 安装在系统路径中 - 请使用此命令：
   sudo -H python3 -m pip install --user pyserial empty toml numpy pandas jinja2 pyyaml pyros-genmsg packaging kconfiglib future jsonschema
   ```

## Gazebo Classic 仿真

要设置 [Gazebo Classic](../sim_gazebo_classic/index.md) 仿真环境：

1. 在终端运行以下命令：

   ```sh
   brew unlink tbb
   sed -i.bak '/disable! date:/s/^/  /; /disable! date:/s/./#/3' $(brew --prefix)/Library/Taps/homebrew/homebrew-core/Formula/tbb@2020.rb
   brew install tbb@2020
   brew link tbb@2020
   ```

   ::: info
   2021年9月：上述命令是此错误的变通方案：[PX4-Autopilot#17644](https://github.com/PX4/PX4-Autopilot/issues/17644)。
   一旦该错误解决（连同此注释），这些命令将被移除。
   :::

1. 要安装带 Gazebo Classic 的 SITL 仿真：

   ```sh
   brew install --cask temurin
   brew install --cask xquartz
   brew install px4-sim-gazebo
   ```

1. 运行 macOS 设置脚本：`PX4-Autopilot/Tools/setup/macos.sh`
   最简单的方法是克隆 PX4 源代码，然后从目录运行脚本，如：

   ```sh
   git clone https://github.com/PX4/PX4-Autopilot.git --recursive
   cd PX4-Autopilot/Tools/setup
   sh macos.sh
   ```

## 下一步

完成命令行工具链设置后：

- 安装 [VSCode](../dev_setup/vscode.md)（如果您更喜欢使用 IDE 而不是命令行）。
- 安装 [QGroundControl Daily Build](../dev_setup/qgc_daily_build.md)

  :::tip
  _每日构建_ 包含在发布版本中隐藏的开发工具。
  它还可能提供尚未在发布版本中支持的新 PX4 功能。
  :::

- 继续查看 [构建说明](../dev_setup/building_px4.md)。