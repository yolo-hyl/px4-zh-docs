# PX4 控制台/外壳

PX4 通过 [MAVLink Shell](../debug/mavlink_shell.md) 和 [系统控制台](../debug/system_console.md) 提供系统终端访问功能。

本页面将解释主要区别以及控制台/外壳的使用方法。

<a id="console_vs_shell"></a>

## 系统控制台与外壳

PX4 的 _系统控制台_ 提供对系统的底层访问、调试输出及系统启动过程的分析功能。

系统控制台只有一个，运行于特定的 UART 接口（调试端口，由 NuttX 配置），通常通过 FTDI 电缆（或其他调试适配器如 [Dronecode probe](https://kb.zubax.com/display/MAINKB/Dronecode+Probe+documentation)）连接到计算机。

- 用于 _底层调试/开发_：启动过程、NuttX、启动脚本、板级启动、PX4 中央部分的开发（如 uORB）。
- 特别是，是唯一能显示所有启动输出（包括自动启动应用信息）的地方。

外壳提供对系统的高级访问：

- 用于基础模块测试/运行命令。
- 仅 _直接_ 显示所启动模块的输出。
- 无法 _直接_ 显示工作队列上运行任务的输出。
- 在系统未启动时无法调试问题（因为系统尚未运行）。

::: info
`dmesg` 命令现在在部分板载外壳中可用，使得底层调试比以往更深入。例如，使用 `dmesg -f &` 还能查看后台任务的输出。
:::

可以有多个外壳，既可在专用 UART 上运行，也可通过 MAVLink 运行。由于 MAVLink 提供了更多灵活性，目前仅使用 [MAVLink Shell](../debug/mavlink_shell.md)。

[系统控制台](../debug/system_console.md) 在系统无法启动时至关重要（在板载重启时显示系统启动日志）。[MAVLink Shell](../debug/mavlink_shell.md) 更容易设置，因此对大多数调试场景更推荐使用。

<a id="using_the_console"></a>

## 使用控制台/外壳

MAVLink 外壳/控制台和 [系统控制台](../debug/system_console.md) 的使用方式基本相同。

例如，输入 `ls` 查看本地文件系统，`free` 查看剩余可用 RAM，`dmesg` 查看启动输出。

```sh
nsh> ls
nsh> free
nsh> dmesg
```

以下是一些可在 [NuttShell](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=139629410) 中使用的命令，用于获取系统信息。

此 NSH 命令显示剩余可用内存：

```sh
free
```

`top` 命令显示每个应用的堆栈使用情况：

```sh
top
```

注意堆栈使用量是通过堆栈着色计算的，显示的是任务启动以来的最大使用量（而非当前使用量）。

要查看工作队列中运行的内容及其速率，使用：

```sh
work_queue status
```

调试 uORB 话题时使用：

```sh
uorb top
```

检查特定 uORB 话题时使用：

```sh
listener <topic_name>
```

其他系统命令和模块详见 [模块和命令参考](../modules/modules_main.md)（如 `top`、`listener` 等）。

:::tip
某些命令在部分板载上可能被禁用（即由于 RAM 或 FLASH 限制，部分模块未包含在固件中）。此时会收到响应：`command not found`
:::