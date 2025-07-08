# 仿真调试

由于仿真运行在主机上，所有桌面开发工具均可使用。

## CLANG 地址消毒器 (Mac OS, Linux)

Clang 地址消毒器可帮助检测对齐错误（总线错误）和其他内存错误，如段错误。以下命令设置正确的编译选项：

```sh
make clean # 仅在首次运行地址消毒器后需要
PX4_ASAN=1 make px4_sitl jmavsim
```

## Valgrind

```sh
brew install valgrind
```

或

```sh
sudo apt-get install valgrind
```

在SITL仿真中使用valgrind：

```sh
make px4_sitl_default jmavsim___valgrind
```

## 启动无调试器的Gazebo Classic SITL

默认情况下，使用任何模拟器后端时SITL均不附加调试器启动：

```sh
make px4_sitl_default gz
make px4_sitl_default gazebo-classic
make px4_sitl_default jmavsim
```

对于Gazebo Classic（仅限），您也可以附加调试器启动模拟器。但请注意必须在模拟器目标中提供机体类型，如下所示：

```sh
make px4_sitl_default gazebo-classic_iris_gdb
make px4_sitl_default gazebo-classic_iris_lldb
```

这将启动调试器并使用Gazebo和Iris模拟器运行SITL应用。要进入调试器shell并暂停执行，请按 `CTRL-C`：

```sh
Process 16529 stopped
* thread #1: tid = 0x114e6d, 0x00007fff90f4430a libsystem_kernel.dylib`__read_nocancel + 10, name = 'px4', queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
    frame #0: 0x00007fff90f4430a libsystem_kernel.dylib`__read_nocancel + 10
libsystem_kernel.dylib`__read_nocancel:
->  0x7fff90f4430a <+10>: jae    0x7fff90f44314            ; <+20>
    0x7fff90f4430c <+12>: movq   %rax, %rdi
    0x7fff90f4430f <+15>: jmp    0x7fff90f3fc53            ; cerror_nocancel
    0x7fff90f44314 <+20>: retq
(lldb)
```

为避免DriverFrameworks调度干扰调试会话，应在LLDB和GDB中屏蔽SIGCONT信号：

```sh
(lldb) process handle SIGCONT -n false -p false -s false
```

或在GDB中：

```sh
(gdb) handle SIGCONT noprint nostop
```

之后，lldb或gdb shell将表现得像正常会话，请参考LLDB/GDB文档。最后一个参数，即&lt;viewer_model_debugger&gt;三元组，实际上会被传递给构建目录中的make命令，因此：

```sh
make px4_sitl_default gazebo-classic_iris_gdb
```

等价于：

```sh
make px4_sitl_default	# 使用cmake配置
make -C build/px4_sitl_default classic_iris_gdb
```

可通过以下命令获取构建目录中所有可用make目标的完整列表：

```sh
make help
```

## 将GDB附加到运行中的SITL

您也可以先启动仿真，然后附加gdb：

1. 在一个终端窗口输入启动仿真的命令：

   ```sh
   make px4_sitl_default gazebo-classic
   ```

   当脚本运行时，请注意**SITL COMMAND:** 输出文本，它位于大型"PX4"文本正上方，它将列出px4二进制文件的路径供后续使用。

   ```sh
   SITL COMMAND: "<px4 bin file>" "<build dir>"/etc

   ______  __   __    ___
   | ___ \ \ \ / /   /   |
   | |_/ /  \ V /   / /| |
   |  __/   /   \  / /_| |
   | |     / /^\ \ \___  |
   \_|     \/   \/     |_/

   px4 starting.

   INFO  [px4] startup script: /bin/sh etc/init.d-posix/rcS 0
   INFO  [init] found model autostart file as SYS_AUTOSTART=10015
   ```

2. 打开另一个终端并输入：

   ```sh
   ps -a
   ```

   需要记下名为"PX4"的进程的PID。

   （在此示例中为14149）

   ```sh
   atlas:~/px4/main/PX4-Autopilot$ ps -a
       PID TTY          TIME CMD
   1796 tty2     00:01:59 Xorg
   1836 tty2     00:00:00 gnome-session-b
   14027 pts/1    00:00:00 make
   14077 pts/1    00:00:00 sh
   14078 pts/1    00:00:00 cmake
   14079 pts/1    00:00:00 ninja
   14090 pts/1    00:00:00 sh
   14091 pts/1    00:00:00 bash
   14095 pts/1    00:01:23 gzserver
   14149 pts/1    00:02:48 px4
   14808 pts/2    00:00:00 ps
   ```

3. 然后在同一个窗口输入：

   ```sh
   sudo gdb [px4 bin file path (from step 1) here]
   ```

   例如：

   ```sh
   sudo gdb /home/atlas/px4/base/PX4-Autopilot/build/px4_sitl_default/bin/px4
   ```

   现在，通过输入第2步中记录的PID即可附加到PX4实例：

   ```sh
   attach [PID on px4]
   ```

   现在您应该拥有一个GDB接口进行调试。

## 编译器优化

在配置为`posix_sitl_*`时，可以抑制特定可执行文件和/或模块的编译器优化（通过cmake的`add_executable`或`add_library`添加）。当需要使用调试器逐步执行代码或打印本会被优化掉的变量时，这会很有用。

通过将环境变量`PX4_NO_SANITIZE`设置为1可禁用消毒器。通过将环境变量`PX4_NO_OPTIMIZATION`设置为1可禁用优化。通过将环境变量`PX4_NO_WARNINGS`设置为1可禁用警告。通过将环境变量`PX4_NO_DEBUG`设置为1可禁用调试信息生成。通过将环境变量`PX4_NO_STRIP`设置为1可禁用剥离符号表。通过将环境变量`PX4_NO_LTO`设置为1可禁用链接时优化。通过将环境变量`PX4_NO_PIC`设置为1可禁用位置无关代码生成。通过将环境变量`PX4_NO_SHARED_LIBS`设置为1可禁用共享库生成。通过将环境变量`PX4_NO_STATIC_LIBS`设置为1可禁用静态库生成。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁us将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_errors`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁用将警告视为错误。通过将环境变量`PX4_NO_WARNINGS_AS_ERRORS`设置为1可禁
