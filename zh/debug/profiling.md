

# 简易采样性能分析器

本节介绍如何使用[简易采样性能分析器](https://github.com/PX4/PX4-Autopilot/blob/main/platforms/nuttx/Debug/poor-mans-profiler.sh)（PMSP）shell脚本来评估PX4的性能。
这是对一种已知方法的实现，该方法最初由[Mark Callaghan和Domas Mituzas](https://poormansprofiler.org/)发明。

## 方法

PMSP 是一个通过定期中断固件执行以采样当前堆栈跟踪的 shell 脚本。  
采样的堆栈跟踪会被追加到一个文本文件中。  
当采样完成后（通常需要一个小时或更长时间），收集到的堆栈跟踪将被 _折叠_。  
_折叠_ 的结果是另一个文本文件，其中包含相同的堆栈跟踪，但所有相似的堆栈跟踪（即在程序相同位置获得的）会被合并，并记录其出现次数。  
然后将折叠后的堆栈输入到可视化脚本中，为此我们使用了 [FlameGraph - 一个开源的堆栈跟踪可视化工具](http://www.brendangregg.com/flamegraphs.html)。

## 基本用法

### 先决条件

分析工具依赖于 GDB 在嵌入式目标上运行 PX4。
因此在开始对目标进行性能分析之前，您必须拥有要分析的硬件，并且必须将固件编译并上传到该硬件。
然后您需要一个 [调试探针](../debug/swd_debug.md#debug-probes)（例如 DroneCode Probe），用于运行 GDB 服务器并与开发板进行交互。

### 确定调试设备

如果使用[DroneCode Probe](../debug/probe_bmp.md#dronecode-probe)，`poor-mans-profiler.sh`会自动检测并使用正确的USB设备。  
如果使用其他类型的调试探针，可能需要指定调试器所在的具体_设备_。  
您可以通过bash命令`ls -alh /dev/serial/by-id/`在Ubuntu上枚举可能的设备。例如，当通过USB连接Pixhawk 4和DroneCode Probe时，枚举出以下设备：

```sh
user@ubuntu:~/PX4-Autopilot$ ls -alh /dev/serial/by-id/
total 0
drwxr-xr-x 2 root root 100 Apr 23 18:57 .
drwxr-xr-x 4 root root  80 Apr 23 18:48 ..
lrwxrwxrwx 1 root root  13 Apr 23 18:48 usb-3D_Robotics_PX4_FMU_v5.x_0-if00 -> ../../ttyACM0
lrwxrwxrwx 1 root root  13 Apr 23 18:57 usb-Black_Sphere_Technologies_Black_Magic_Probe_BFCCB401-if00 -> ../../ttyACM1
lrwxrwxrwx 1 root root  13 Apr 23 18:57 usb-Black_Sphere_Technologies_Black_Magic_Probe_BFCCB401-if02 -> ../../ttyACM2
```

在此情况下，脚本会自动选择名为`*Black_Magic_Probe*-if00`的设备。  
如果您使用其他设备，可以通过上述列表找到合适的设备标识符。

然后通过`--gdbdev`参数传入适当的设备，如下所示：

```sh
./poor-mans-profiler.sh --elf=build/px4_fmu-v4_default/px4_fmu-v4_default.elf --nsamples=30000 --gdbdev=/dev/ttyACM2
```

### 运行

通过构建系统可以使用性能分析器的基本功能。
例如，以下命令会构建并分析 px4_fmu-v4pro 目标，使用 10000 次采样（必要时会获取 _FlameGraph_ 并将其添加到路径中）。

```sh
make px4_fmu-v4pro_default profile
```

如需对构建过程进行更多控制（包括设置采样次数），请参见[Implementation](#实现)。

## 理解输出

下方提供了一个示例输出的截图（请注意此处为静态展示）：

![FlameGraph Example](../../assets/debug/flamegraph-example.png)

在火焰图中，水平层级代表堆栈帧，每一帧的宽度与其被采样的次数成正比。相应地，函数被采样的次数与其执行时间与频率的乘积成正比。

## 可能的问题

该脚本作为临时解决方案开发，因此存在一些问题。
使用时请注意以下事项：

- 如果 GDB 运行异常，脚本可能无法检测到此情况并继续运行。
  在这种情况下，显然无法生成可用的堆栈信息。
  为避免此情况，用户应定期检查文件 `/tmp/pmpn-gdberr.log`，该文件包含最近一次 GDB 调用的标准错误输出。
  未来脚本应修改为以静默模式调用 GDB，通过退出码指示问题。

- 有时 GDB 会在采样堆栈跟踪时无限期卡住。
  在此故障期间，目标设备将无限期停止运行。
  解决方案是手动中止脚本并使用 `--append` 选项重新启动。
  未来脚本应修改为为每次 GDB 调用强制设置超时。

- 不支持多线程环境。
  这不会影响单核嵌入式目标，因为它们始终以单一线程执行，但此限制使性能分析器与许多其他应用程序不兼容。
  未来堆栈文件夹应修改为支持每个采样点的多个堆栈跟踪。

## 实现

脚本位于路径 [platforms/nuttx/Debug/poor-mans-profiler.sh](https://github.com/PX4/PX4-Autopilot/blob/main/platforms/nuttx/Debug/poor-mans-profiler.sh)  
启动后，脚本将按照指定的采样次数和时间间隔进行采样。  
采集的样本将存储在系统临时目录（通常是 `/tmp`）的文本文件中。  
采样完成后，脚本将自动调用堆栈处理模块，输出结果将保存在临时目录中的相邻文件中。  
如果堆栈合并成功，脚本将调用 _FlameGraph_ 脚本，并将结果保存为交互式 SVG 文件。  
请注意并非所有图片查看器都支持交互式图像；建议在网页浏览器中打开生成的 SVG 文件。

FlameGraph 脚本必须位于系统 `PATH` 中，否则 PMSP 将拒绝启动。

PMSP 使用 GDB 收集堆栈跟踪。  
当前使用的是 `arm-none-eabi-gdb`，未来可能会增加对其他工具链的支持。

为了将内存地址映射到符号，脚本需要引用目标设备上当前运行的可执行文件。  
这通过选项 `--elf=<file>` 实现，该选项需要提供一个相对于仓库根目录的路径，指向当前运行的 ELF 文件位置。

使用示例：

```sh
./poor-mans-profiler.sh --elf=build/px4_fmu-v4_default/px4_fmu-v4_default.elf --nsamples=30000
```

注意每次运行脚本都会覆盖旧的堆栈数据。  
如果希望追加而不是覆盖旧数据，请使用 `--append` 选项：

```sh
./poor-mans-profiler.sh --elf=build/px4_fmu-v4_default/px4_fmu-v4_default.elf --nsamples=30000 --append
```

如您所料，`--append` 与 `--nsamples=0` 组合使用时，将指示脚本仅重新生成 SVG 而完全不访问目标设备。

请阅读脚本源码以深入了解其工作原理。