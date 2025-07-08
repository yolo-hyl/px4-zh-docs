# 系统启动

PX4 的启动由 shell 脚本控制。  
在 NuttX 上，这些脚本位于 [ROMFS/px4fmu_common/init.d](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d) 文件夹中 - 其中一些脚本也用于 Posix (Linux/MacOS)。  
仅在 Posix 上使用的脚本位于 [ROMFS/px4fmu_common/init.d-posix](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d-posix) 文件夹中。

所有以数字和下划线开头的文件（例如 `10000_airplane`）都是预定义的机型配置。  
这些文件在构建时被导出为 `airframes.xml` 文件，该文件由 [QGroundControl](http://qgroundcontrol.com) 解析以实现机型选择的用户界面。  
添加新配置的方法请参见 [here](../dev_airframes/adding_a_new_frame.md)。

其余文件属于通用启动逻辑。  
第一个执行的文件是 [init.d/rcS](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/rcS) 脚本（或 Posix 上的 [init.d-posix/rcS](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/rcS)），它会调用所有其他脚本。

以下部分按 PX4 运行的操作系统进行划分。

## Posix (Linux/MacOS)

在 Posix 上，系统 shell 用作脚本解释器（例如 /bin/sh，在 Ubuntu 上符号链接到 dash）。  
要实现此功能，需要满足以下条件：

- PX4 模块需要在系统中表现为独立的可执行文件。  
  这通过符号链接实现。  
  在构建文件夹的 `bin` 目录中，为每个模块创建符号链接 `px4-<module> -> px4`。  
  当执行时，会检查二进制路径（`argv[0]`），如果以 `px4-` 开头，则会将命令发送到主 px4 实例（见下文）。

  :::tip
  `px4-` 前缀用于避免与系统命令（如 `shutdown`）冲突，并且可以通过输入 `px4-<TAB>` 实现简单的命令补全。
  :::

- shell 需要知道如何找到这些符号链接。  
  在执行启动脚本之前，将包含符号链接的 `bin` 目录添加到 `PATH` 变量中。
- shell 以新进程（客户端）启动每个模块。  
  每个客户端进程需要与主 px4 实例（服务器）通信，实际模块以线程形式运行在服务器中。  
  这通过 [UNIX socket](http://man7.org/linux/man-pages/man7/unix.7.html) 实现。  
  服务器监听一个套接字，客户端可以连接并发送命令。  
  服务器将输出和返回代码返回给客户端。
- 启动脚本直接调用模块，例如 `commander start`，而不是使用 `px4-` 前缀。  
  这通过别名实现：在文件 `bin/px4-alias.sh` 中为每个模块创建 `alias <module>=px4-<module>` 形式的别名。
- `rcS` 脚本从主 px4 实例执行。  
  它不会启动任何模块，而是首先更新 `PATH` 变量，然后运行一个带有 `rcS` 文件作为参数的 shell。
- 此外，可以启动多个服务器实例以进行多车辆模拟。  
  客户端通过 `--instance` 选择实例。  
  实例在脚本中通过 `$px4_instance` 变量可用。

当 PX4 已在系统上运行时，可以从任何终端执行模块。  
例如：

```sh
cd <PX4-Autopilot>/build/px4_sitl_default/bin
./px4-commander takeoff
./px4-listener sensor_accel
```

### 动态模块

通常，所有模块都被编译进一个 PX4 可执行文件中。  
然而，在 Posix 上，可以将模块编译为单独的文件，并通过 `dyn` 命令加载到 PX4 中。

```sh
dyn ./test.px4mod
```

## NuttX

NuttX 集成了 shell 解释器（[NuttShell (NSH)](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=139629410)），因此脚本可以直接执行。

### 调试系统启动

驱动程序或软件组件的故障不会导致启动中止。  
这是通过启动脚本中的 `set +e` 控制的。

可以通过连接 [系统控制台](../debug/system_console.md) 并重新上电开发板来调试启动序列。  
生成的启动日志包含有关启动序列的详细信息，并应包含导致启动中止的提示。

#### 常见启动故障原因

- 对于自定义应用程序：系统内存不足（RAM）。  
  运行 `free` 命令查看可用 RAM 量。
- 软件故障或断言导致堆栈跟踪

### 替换系统启动

通过在 microSD 卡上创建 `/etc/rc.txt` 文件并写入新配置，可以完全替换整个启动过程（旧配置中的任何内容都不会自动启动，如果文件为空则不会启动任何内容）。

### 自定义系统启动

以下是您提供的 PX4 文档内容的地道中文翻译，专有名词（如 `config.txt`、`extras.txt`、`param`、`PWM_MAIN_DIS3` 等）均已保留未翻译：

---

## 自定义系统启动

自定义系统启动的最佳方式是引入一个新的机架（frame）配置文件。该配置文件可以包含在固件中，也可以放在 SD 卡上。

如果您只需要对现有配置进行一些“微调”，例如启动一个额外的应用程序或设置几个参数的值，可以在 SD 卡的 `/etc/` 目录下创建以下两个文件：

* `/etc/config.txt`：用于修改参数值
* `/etc/extras.txt`：用于启动应用程序

下面将分别介绍这两个文件的用途。

:::warning
系统启动文件是 UNIX 文件，必须使用 **UNIX 换行符**。如果您在 Windows 系统上编辑这些文件，请使用合适的编辑器（如 VS Code、Notepad++ 等支持 UNIX 换行符的工具）。
:::

::: info
在 PX4 的代码中，这些文件被引用为：
`/fs/microsd/etc/config.txt` 和 `/fs/microsd/etc/extras.txt`
其中 `/fs/microsd` 表示 SD 卡的根目录路径。
:::

## 自定义参数配置（`config.txt`）

`config.txt` 文件用于修改系统参数。它会在主系统配置完成之后、启动之前加载。

例如，您可以在 SD 卡上创建一个 `etc/config.txt` 文件，并设置如下参数值：

```sh
param set-default PWM_MAIN_DIS3 1000
param set-default PWM_MAIN_MIN3 1120
```

---

## 启动额外应用程序（`extras.txt`）

`extras.txt` 文件可用于在主系统启动后启动额外的应用程序。通常这些应用程序是有效载荷控制器或其他可选的自定义组件。

:::warning
如果在系统启动文件中调用了未知命令，可能会导致启动失败。
通常在启动失败后，系统不会继续发送 MAVLink 消息。在这种情况下，请检查系统控制台上输出的错误信息。
:::

以下示例展示了如何启动自定义应用程序：

在 SD 卡上创建文件 `etc/extras.txt`，内容如下：

```sh
custom_app start
```

如果某个命令是可选的（即使它不存在也不应导致启动失败），可以使用 `set +e` 和 `set -e` 来包裹该命令：

```sh
set +e
optional_app start      # 如果 optional_app 未知或启动失败，不会导致系统启动失败
set -e

mandatory_app start     # 如果 mandatory_app 未知或启动失败，将中止系统启动
```