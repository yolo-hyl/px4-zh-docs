# 使用 Eclipse 和 J-Link 调试

本主题说明如何通过 [MCU Eclipse](https://gnu-mcu-eclipse.github.io/) 和 Segger Jlink 适配器调试运行在 NuttX 上的 PX4（例如 Pixhawk 系列板）。

## 所需硬件

- [J-Link EDU Mini](https://www.segger.com/products/debug-probes/j-link/models/j-link-edu-mini/)
- 用于连接 Segger JLink 和飞控 [SWD 调试端口](../debug/swd_debug.md) 的适配器
- Micro USB 数据线

## 安装

### PX4

按照常规指南配置 PX4：

- [设置 PX4 开发环境/工具链](../dev_setup/dev_env.md)（Linux 平台示例详见：[Ubuntu LTS/Debian Linux 开发环境](../dev_setup/dev_env_linux_ubuntu.md)）
- [下载 PX4](../dev_setup/building_px4.md) 并选择性地通过命令行构建

### Eclipse

安装 Eclipse 的步骤：

1. 下载 [Eclipse CDT for C/C++ 开发者](https://github.com/gnu-mcu-eclipse/org.eclipse.epp.packages/releases/)（MCU GitHub）
1. 解压 Eclipse 文件夹并复制到任意位置（无需运行安装脚本）
1. 运行 Eclipse 并选择初始工作台位置

### Segger Jlink 工具

安装 Segger Jlink 工具：

1. 下载并运行 [J-Link 软件和文档包](https://www.segger.com/downloads/jlink/#J-LinkSoftwareAndDocumentationPack)（支持 Windows 和 Linux）
   - Linux 系统工具安装在 **/usr/bin** 目录
   - 更多信息参见：[https://gnu-mcu-eclipse.github.io/debug/jlink/install/](https://gnu-mcu-eclipse.github.io/debug/jlink/install/)

## 首次使用

1. 将 Segger JLink 连接到主机和飞控 [调试端口](../debug/swd_debug.md)（通过适配器）
1. 给飞控供电
1. 运行 Eclipse
1. 通过 **File > Import > C/C++ > Existing Code as Makefile Project** 导入源代码并点击 **Next**
1. 指定 **PX4-Autopilot** 文件夹，选择 _ARM Cross GCC_ 作为索引器工具链并点击 **Finish**
   - 导入过程可能较慢，请等待完成
1. 配置 MCU 设置：右键顶层项目选择 _Properties_，在 MCU 部分设置 _SEGGER J-Link Path_
   ![Eclipse: Segger J-Link 路径](../../assets/debug/eclipse_segger_jlink_path.png)
1. 更新设备包：

   - 点击右上角 _Open Perspective_ 图标，打开 _Packs_ 视图
     ![Eclipse: 工作区](../../assets/debug/eclipse_workspace_perspective.png)
   - 点击 **update all** 按钮

     :::tip
     此过程耗时较长（约10分钟）
     可忽略弹出的缺失包错误提示
     :::

     ![Eclipse: 工作区包视图](../../assets/debug/eclipse_packs_perspective.jpg)

   - STM32Fxx 设备位于 Keil 文件夹，右键选择 **install** 安装对应F4/F7设备

1. 配置目标调试设置：

   - 右键项目选择 **Settings**（菜单：**C/C++ Build > Settings**）
   - 选择 _Devices_ 选项卡（非 _Boards_）
   - 找到要调试的 FMU 芯片

   ![Eclipse: 设置中选择 FMU](../../assets/debug/eclipse_settings_devices_fmu.png)

1. 通过调试配置下拉菜单选择配置：
   ![Eclipse: 调试配置](../../assets/debug/eclipse_settings_debug_config.png)
1. 选择 _GDB SEGGER J-Link Debugging_ 并点击左上角 **New config**
   ![Eclipse: GDB Segger 调试配置](../../assets/debug/eclipse_settings_debug_config_gdb_segger.png)
1. 配置构建选项：

   - 设置名称并指定 _C/C++ Application_ 为对应 **.elf** 文件
   - 选择 _Disable Auto build_

     ::: info
     记得在启动调试会话前通过命令行构建目标
     :::

   ![Eclipse: GDB Segger 调试配置](../../assets/debug/eclipse_settings_debug_config_gdb_segger_build_config.png)

1. _Debugger_ 和 _Startup_ 选项卡通常无需修改（请通过截图核对设置）

   ![Eclipse: 调试器选项卡](../../assets/debug/eclipse_settings_debug_config_gdb_segger_build_config_debugger_tab.png)
   ![Eclipse: 启动选项卡](../../assets/debug/eclipse_settings_debug_config_gdb_segger_build_config_startup_tab.png)

## SEGGER 任务感知调试

任务感知调试（也称为[线程感知调试](https://www.segger.com/products/debug-probes/j-link/tools/j-link-gdb-server/thread-aware-debugging/)）可显示所有运行线程/任务的上下文，而不仅限于当前任务栈。这对调试 PX4 特别有用，因为 PX4 通常运行多个任务。

在 Eclipse 中启用此功能：

1. 在 NuttX 配置中启用 `CONFIG_DEBUG_TCBINFO`（用于暴露 TCB 偏移量）

   - 在 PX4-Autopilot 源码根目录打开终端
   - 执行 `make px4_fmu-v5_default menuconfig` 配置

1. 构建 Jlink 适配器：
   - 执行 `make jlink`
   - 更多信息参见：[https://github.com/PX4/px4_jlink](https://github.com/PX4/px4_jlink)

## 故障排查

### 目标CPU未在包管理器中出现

1. 下载 SVD 文件：
   - 从 [https://github.com/posborne/cmsis-pack-xml](https://github.com/posborne/cmsis-pack-xml) 获取
   - 保存为 `.svd` 格式

2. 配置 Eclipse：
   - 在 _Device_ 设置中加载 `.svd` 文件
   - 通过 _Window > Preferences > MCU > GDB Server_ 添加路径

3. 验证连接：
   ```bash
   jlink -device STM32F767ZI -if SWD -speed 4000 -autoconnect 1
   ```