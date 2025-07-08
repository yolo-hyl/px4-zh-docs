# Ubuntu 开发环境

以下说明通过 bash 脚本为 PX4 支持的 [Ubuntu Linux LTS](https://wiki.ubuntu.com/LTS) 版本设置开发环境：Ubuntu 24.04 (Nimble Numbat) 和 Ubuntu 22.04 (Jammy Jellyfish)。

该环境包含：

- [Gazebo 模拟器](../sim_gazebo_gz/index.md) ("Harmonic")
- [Pixhawk（及其他 NuttX 基础硬件）的构建工具链](../dev_setup/building_px4.md#nuttx-pixhawk-based-boards)

在 Ubuntu 22.04 上：

- 可以使用 [Gazebo Classic 模拟器](../sim_gazebo_classic/index.md) 替代 Gazebo
  Gazebo 在 PX4 上的功能几乎与 Gazebo-Classic 相同，很快将完全取代其所有用例

其他飞控器、模拟器和与 ROS 的构建工具链请参见下文 [Other Targets](#other-targets) 章节

::: details 我可以使用旧版 Ubuntu 吗？
PX4 支持当前及上一版 Ubuntu LTS（如可能）。
旧版不受支持（因此不能对其提出缺陷报告），但可能仍可运行。
例如，Gazebo Classic 安装包含在 macOS、Ubuntu 18.04 和 20.04 以及 Windows on WSL2 的标准构建说明中。
:::

## 模拟器和 NuttX (Pixhawk) 目标

使用 [ubuntu.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/ubuntu.sh) 脚本设置开发环境，该环境允许您为模拟器和/或 [NuttX/Pixhawk](../dev_setup/building_px4.md#nuttx-pixhawk-based-boards) 工具链进行构建。

:::tip
该脚本旨在在 _干净的_ Ubuntu LTS 安装上运行，如果在现有系统上运行或在不同 Ubuntu 版本上运行可能无法工作。
:::

安装工具链：

1. [下载 PX4 源代码](../dev_setup/building_px4.md)：

   ```sh
   git clone https://github.com/PX4/PX4-Autopilot.git --recursive
   ```

   ::: info
   源代码中的环境设置脚本通常适用于最近的 PX4 发布版本。
   如果使用旧版 PX4，可能需要 [获取特定于您版本的源代码](../contribute/git_examples.md#get-a-specific-release)。
   :::

2. 不带参数运行 **ubuntu.sh**（在 bash shell 中）以安装所有内容：

   ```sh
   bash ./PX4-Autopilot/Tools/setup/ubuntu.sh
   ```

   - 在脚本运行过程中确认任何提示
   - 可使用 `--no-nuttx` 和 `--no-sim-tools` 选项省略 NuttX 和/或模拟工具

3. 如果需要 Gazebo Classic（仅限 Ubuntu 22.04），可以手动移除 Gazebo 并按照 [Gazebo Classic > 安装](../sim_gazebo_classic/index.md#installation) 中的说明进行安装

4. 完成后重新启动计算机

:::details 补充说明
以下说明仅作为参考：

- 该设置由 PX4 开发团队支持
  指南可能在其他基于 Debian 的 Linux 系统上也适用
- 可通过确认 `gcc` 版本来验证 NuttX 安装：

  ```sh
  $arm-none-eabi-gcc --version

  arm-none-eabi-gcc (15:13.2.rel1-2) 13.2.1 20231009
  Copyright (C) 2023 Free Software Foundation, Inc.
  This is free software; see the source for copying conditions.  There is NO
  warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
  ```

- 您无论如何都需要 PX4 源代码
  但如果您只想设置开发环境而不获取所有源代码，可以仅下载 [ubuntu.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/ubuntu.sh) 和 [requirements.txt](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/requirements.txt) 然后运行 **ubuntu.sh**：

  ```sh
  wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/ubuntu.sh
  wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/requirements.txt
  bash ubuntu.sh
  ```

  :::

## 其他目标

ROS、其他模拟器和其他硬件目标的 Ubuntu 开发环境，请参阅其对应的文档。
相关主题的链接如下：

Raspberry Pi

- [Raspberry Pi 2/3 Navio2 自动驾驶仪 > PX4 开发环境](../flight_controller/raspberry_pi_navio2.md#px4-development-environment)
- [Raspberry Pi 2/3/4 PilotPi Shield](../flight_controller/raspberry_pi_pilotpi.md)

ROS

- ROS 2: [ROS 2 用户指南 > 安装与设置](../ros2/user_guide.md#installation-setup)
- ROS (1): [ROS (1) 安装指南](../ros/mavros_installation.md)

## 下一步

完成命令行工具链设置后：

- 安装 [VSCode](../dev_setup/vscode.md)（如果您更喜欢使用 IDE 而不是命令行）
- 安装 [QGroundControl Daily Build](../dev_setup/qgc_daily_build.md)

  :::tip
  _每日构建版_ 包含在发布版中隐藏的开发工具
  它还可能提供尚未在发布版中支持的新 PX4 功能
  :::

- 继续 [构建说明](../dev_setup/building_px4.md)