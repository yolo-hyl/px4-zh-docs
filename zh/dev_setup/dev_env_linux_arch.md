# Arch Linux 开发环境

:::warning
此开发环境为[社区支持和维护](../advanced/community_supported_dev_env)。
其可能与当前版本的 PX4 兼容，也可能不兼容。

请参阅[工具链安装](../dev_setup/dev_env.md)了解核心开发团队支持的环境和工具信息。
:::

PX4-Autopilot 仓库提供了一个便捷脚本来设置您的 Arch 系统以进行 PX4 开发：[Tools/setup/arch.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/arch.sh)。 <!-- NEED px4_version -->

该脚本默认会安装所有构建 PX4 所需的工具（针对 NuttX 目标）并运行 [JMAVSim](../sim_jmavsim/index.md) 模拟器。
您可以通过指定命令行参数 `--gazebo` 来额外安装 [Gazebo Classic](../sim_gazebo_classic/index.md) 模拟器。

![Gazebo on Arch](../../assets/simulation/gazebo_classic/arch-gazebo.png)

::: info
这些说明已在 [Manjaro](https://manjaro.org/)（基于 Arch 的发行版）上测试过，因为其设置比 Arch Linux 更容易。
:::

获取并运行脚本的方式有以下两种：

- [下载 PX4 源代码](../dev_setup/building_px4.md) 并在原路径运行脚本：

  ```sh
  git clone https://github.com/PX4/PX4-Autopilot.git
  bash PX4-Autopilot/Tools/setup/arch.sh
  ```

- 仅下载所需的脚本然后运行：

  ```sh
  wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/arch.sh
  wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/requirements.txt
  bash arch.sh
  ```

脚本接受以下可选参数：

- `--gazebo`: 添加此参数以从 [AUR](https://aur.archlinux.org/packages/gazebo/) 安装 Gazebo。

  ::: info
  Gazebo 将从源代码编译。
  安装需要一些时间，并且需要多次输入 `sudo` 密码（用于依赖项）。
  :::

- `--no-nuttx`: 不安装 NuttX/Pixhawk 工具链（例如仅使用模拟器时）。
- `--no-sim-tools`: 不安装 jMAVSim/Gazebo（例如仅针对 Pixhawk/NuttX 目标时）