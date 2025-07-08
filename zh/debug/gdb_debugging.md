# 使用 GDB 调试

[GNU 调试器 (GDB)](https://sourceware.org/gdb/documentation/) 以 `arm-none-eabi-gdb` 二进制文件的形式随编译器工具链一起安装。调试器会读取 ELF 文件中的调试符号，以理解 PX4 固件的静态和动态内存布局。要访问 PX4 自主控制器微控制器，需要连接到 [远程目标](https://sourceware.org/gdb/current/onlinedocs/gdb.html/Connecting.html)，这由 [SWD 调试探针](swd_debug.md) 提供。

信息流如下所示：

```sh
开发者 <=> GDB <=> GDB 服务器 <=> 调试探针 <=> SWD <=> PX4 自主控制器。
```

## 快速入门

要开始调试会话，通常需要：

1. 一个专用的 [SWD 调试探针](../debug/swd_debug.md#调试探针)。
2. 找到并连接到 [SWD 调试端口](../debug/swd_debug.md#自主控制器调试端口)。
   可能需要一个 [调试适配器](swd_debug.md#调试适配器)。
3. 配置并启动调试探针以创建 GDB 服务器。
4. 启动 GDB 并将其作为远程目标连接到 GDB 服务器。
5. 交互式调试你的固件。

有关如何设置调试连接的详细信息，请参阅调试探针文档：

- [SEGGER J-Link](probe_jlink.md)：商业探针，无内置串口控制台，需要适配器。
- [Black Magic Probe](probe_bmp.md)：集成 GDB 服务器和串口控制台，需要适配器。
- [STLink](probe_stlink)：性价比最高，集成串口控制台，必须焊接适配器。

我们建议使用 J-Link 与 Pixhawk 调试适配器或焊接了定制电缆的 STLinkv3-MINIE。

连接后，可以使用常规的 GDB 命令，例如：

- `continue` 继续程序执行
- `run` 从头开始运行
- `backtrace` 查看调用堆栈
- `break somewhere.cpp:123` 设置断点
- `delete somewhere.cpp:123` 删除断点
- `info locals` 打印局部变量
- `info registers` 打印寄存器

有关更多细节，请参阅 [GDB 文档](https://sourceware.org/gdb/documentation/)。

:::tip
为了避免每次在 GDB 中输入所有连接命令，可以将它们写入 `~/.gdbinit`。
:::

## 下一步

你现在已将飞控连接到 SWD 调试探针！

以下主题解释了如何开始目标调试：

- [MCU Eclipse/J-Link 调试 PX4](eclipse_jlink.md)。
- [Visual Studio Code IDE (VSCode)](../dev_setup/vscode.md)。

## 视频

以下视频概述了通过 GDB 进行 PX4 高级调试的可用工具。该视频在 PX4 开发者大会 2023 上发表。

<lite-youtube videoid="1c4TqEn3MZ0" title="调试 PX4 - Niklas Hauser, Auterion AG"/>

**概述：** 通过 Mavlink Shell (NSH) 内置的检查工具以及飞行后对 PX4 uLog 的解析需要 PX4 仍能正常工作。然而，最严重的问题往往表现为系统（部分）挂起或崩溃。因此，我们介绍了开源的 Embedded Debug Tools 项目，该工具管理并配置探针、调试和分析工具：

- 调试接口（SWD）和相关调试探针（J-Link, STLink）以及库（JLinkGDBServer, OpenOCD）。
- 如何安装和配置 `arm-none-eabi-gdb(-py3)` 以调试你的 ELF。
- 常用 GDB 命令和脚本。
- 通过其 Python API 的高级 GDB 脚本。
- NuttX RTOS 组件内部检查：任务、信号量、调度器。
- 使用 CMSIS-SVD 文件和自定义可视化检查外设状态。
- 通过 CrashDebug 进行事后调试的核心转储。
- 在实时系统中和通过硬故障日志进行硬故障分析。
- 通过 Machine Interface 的远程 GDB 脚本。
- 通过组合 GDB 和 NSH 脚本实现 PX4 的 HiL 自动化测试。
- 使用 Orbuculum 通过 SWO 引脚进行 ITM 采样。
- 使用 perfetto 进行线程/中断/工作队列/堆可视化和延迟分析。
- 通过 TRACE 引脚进行高带宽 ETM 跟踪：J-Trace 和 ORBtrace mini。
- 最后概述了相关项目的前景以及 PX4 调试的未来发展方向。

## 嵌入式调试工具

[嵌入式调试工具](https://pypi.org/project/emdbg/) 通过用户友好的 Python 包将多个软件和硬件调试工具连接在一起，以更轻松地实现 ARM Cortex-M 微控制器和相关设备的高级用例。

该库协调了硬件调试和跟踪探针、调试器、逻辑分析仪和波形发生器的启动和配置，并提供分析工具、转换器和插件，以在执行过程中或之后深入了解软件和硬件状态。

`emdbg` 库包含 [许多有用的 GDB 插件](https://github.com/Auterion/embedded-debug-tools/blob/main/src/emdbg/debug/gdb.md#用户命令)，这些插件使调试 PX4 更加便捷。
它还提供了 [实时分析 PX4 的工具](https://github.com/Auterion/embedded-debug-tools/tree/main/ext/orbetto)。