

# 系统启动

PX4 的启动由 shell 脚本控制。  
在 NuttX 上它们位于 [ROMFS/px4fmu_common/init.d](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d) 文件夹 - 其中一些也用于 Posix（Linux/MacOS）。  
仅在 Posix 上使用的脚本位于 [ROMFS/px4fmu_common/init.d-posix](https://github.com/PX4/PX4-Autopilot/tree/main/ROMFS/px4fmu_common/init.d-posix)。

所有以数字和下划线开头的文件（例如 `10000_airplane`）是预定义的机身配置。  
它们在构建时被导出到一个 `airframes.xml` 文件中，该文件被 [QGroundControl](http://qgroundcontrol.com) 解析以生成机身选择用户界面。  
添加新配置请参考 [此处](../dev_airframes/adding_a_new_frame.md)。

其余文件属于通用启动逻辑。  
第一个执行的文件是 [init.d/rcS](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/rcS) 脚本（或 Posix 上的 [init.d-posix/rcS](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/rcS)），它会调用所有其他脚本。

以下内容将根据 PX4 运行的操作系统进行划分。

## Posix (Linux/MacOS)

在 Posix 系统中，系统 shell 被用作脚本解释器（例如 /bin/sh，在 Ubuntu 上通过符号链接指向 dash）。
要实现这一点，需要满足以下条件：

- PX4 模块需要对系统而言看起来像是独立的可执行文件。
  这通过符号链接实现。
  在构建文件夹的 `bin` 目录中，为每个模块创建一个符号链接 `px4-<module> -> px4`。
  执行时，会检查二进制文件的路径（`argv[0]`），如果路径以 `px4-` 开头（即为模块），则将命令发送到主 px4 实例（见下文）。

  :::tip
  `px4-` 前缀用于避免与系统命令（如 `shutdown`）冲突，并且通过输入 `px4-<TAB>` 可实现简单的标签补全。
  :::

- shell 需要知道符号链接的位置。
  因此在执行启动脚本之前，将包含符号链接的 `bin` 目录添加到 `PATH` 环境变量中。
- shell 会将每个模块作为新（客户端）进程启动。
  每个客户端进程需要与主 px4 实例（服务器）通信，实际模块以线程形式在服务器中运行。
  这通过 [UNIX socket](http://man7.org/linux/man-pages/man7/unix.7.html) 实现。
  服务器监听一个套接字，客户端可以连接并发送命令。
  服务器随后将输出和返回码返回给客户端。
- 启动脚本直接调用模块，例如 `commander start`，而不是使用 `px4-` 前缀。
  这通过别名实现：在文件 `bin/px4-alias.sh` 中，为每个模块创建形如 `alias <module>=px4-<module>` 的别名。
- `rcS` 脚本从主 px4 实例中执行。
  它不会启动任何模块，而是先更新 `PATH` 变量，然后运行一个 shell 并将 `rcS` 文件作为参数传入。
- 此外，可以启动多个服务器实例以支持多 vehicle 模拟。
  客户端通过 `--instance` 参数选择实例。
  实例信息在脚本中通过 `$px4_instance` 变量可用。

当 PX4 已在系统上运行时，模块可以从任意终端执行。
例如：

```sh
cd <PX4-Autopilot>/build/px4_sitl_default/bin
./px4-commander takeoff
./px4-listener sensor_accel
```

### 动态模块

通常情况下，所有模块都会被编译到一个PX4可执行文件中。然而，在Posix系统上，可以选择将模块编译为单独的文件，然后使用`dyn`命令将其加载到PX4中。

```sh
dyn ./test.px4mod
```

## NuttX

NuttX 集成了一个 shell 解释器（[NuttShell (NSH)](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=139629410)），因此可以直接执行脚本。

### 系统启动调试

软件组件的驱动程序故障不会导致启动中止。  
这是通过启动脚本中的 `set +e` 控制的。

可以通过连接[系统控制台](../debug/system_console.md)并对电路板进行电源循环来调试启动序列。  
生成的启动日志包含有关启动序列的详细信息，并应包含导致启动中止的提示。

#### 常见的启动失败原因

- 对于自定义应用：系统内存不足  
  运行 `free` 命令查看可用内存数量  
- 软件故障或断言导致堆栈跟踪

### 替换系统启动

通过在 microSD 卡上创建一个文件 `/etc/rc.txt` 并配置新设置，可以完全替换整个启动过程（旧配置中的任何内容都不会自动启动，如果文件为空，则完全不会启动任何内容）。

自定义默认启动通常是更好的方法。如下文档所述。

### 自定义系统启动

自定义系统启动的最佳方式是引入一个 [新机架配置](../dev_airframes/adding_a_new_frame.md)。  
机架配置文件可以包含在固件中或存储在SD卡中。

如果只需要对现有配置进行"微调"，例如启动一个额外应用或设置少量参数值，可以通过在SD卡的`/etc/`目录下创建两个文件来实现：  

- [/etc/config.txt](#customizing-the-configuration-config-txt): 修改参数值  
- [/etc/extras.txt](#starting-additional-applications-extras-txt): 启动应用  

这两个文件说明如下：

:::warning  
系统启动文件是UNIX文件，需要使用UNIX换行符。  
在Windows上编辑时请使用合适的编辑器。  
:::  

::: info  
这些文件在PX4代码中被引用为`/fs/microsd/etc/config.txt`和`/fs/microsd/etc/extras.txt`，其中microsd卡的根目录通过路径`/fs/microsd`标识。  
:::

#### 自定义配置（config.txt）

`config.txt` 文件可用于修改参数。  
它在主系统配置完成后、系统启动前加载。

例如，你可以在 SD 卡上创建一个文件 `etc/config.txt`，按如下方式设置参数值：

```sh
param set-default PWM_MAIN_DIS3 1000
param set-default PWM_MAIN_MIN3 1120
```

#### 启动附加应用程序 (extras.txt)

`extras.txt` 可用于在主系统启动后启动附加应用程序。  
通常这些是有效载荷控制器或类似的可选定制组件。

:::warning
在系统启动文件中调用未知命令可能导致启动失败。  
通常系统在启动失败后不会发送 mavlink 消息，此时请检查系统控制台打印的错误信息。
:::

以下示例展示如何启动自定义应用程序：

- 在SD卡上创建文件 `etc/extras.txt`，内容如下：

  ```sh
  custom_app start
  ```

- 可通过 `set +e` 和 `set -e` 命令使命令变为可选：

  ```sh
  set +e
  optional_app start      # 如果 optional_app 未知或失败，不会导致启动失败
  set -e

  mandatory_app start     # 如果 mandatory_app 未知或失败，将中止启动
  ```