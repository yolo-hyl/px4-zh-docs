# 简易采样分析器

本节描述如何使用 [Poor Man's Sampling Profiler](https://github.com/PX4/PX4-Autopilot/blob/main/platforms/nuttx/Debug/poor-mans-profiler.sh) (PMSP) shell 脚本来评估 PX4 的性能。
这是对已知方法的实现，该方法最初由 [Mark Callaghan 和 Domas Mituzas](https://poormansprofiler.org/) 发明。

## 方法

PMSP 是一个 shell 脚本，通过定期中断固件的执行来采样当前的堆栈跟踪。
采样的堆栈跟踪被追加到一个文本文件中。
一旦采样完成（通常需要一小时或更长时间），收集到的堆栈跟踪将被 _折叠_。
折叠后的结果是另一个文本文件，包含相同的堆栈跟踪，但所有相似的堆栈跟踪（即在程序相同点获得的堆栈）会被合并，并记录它们的出现次数。
然后将折叠后的堆栈输入到可视化脚本中，我们使用 [FlameGraph - 一个开源的堆栈跟踪可视化工具](http://www.brendangregg.com/flamegraphs.html) 来完成可视化。

## 基本用法

### 前提条件

分析器依赖 GDB 在嵌入式目标上运行 PX4。
因此在分析目标之前，您必须拥有要分析的硬件，并将固件编译并上传到该硬件上。
您还需要一个 [调试探针](../debug/swd_debug.md#debug-probes)（例如 DroneCode Probe），以运行 GDB 服务器并与开发板交互。

### 确定调试器设备

`poor-mans-profiler.sh` 在使用 [DroneCode Probe](../debug/probe_bmp.md#dronecode-probe) 时会自动检测并使用正确的 USB 设备。
如果使用其他类型的探针，可能需要传递调试器所在的具体 _设备_。
可以使用 bash 命令 `ls -alh /dev/serial/by-id/` 在 Ubuntu 上枚举可能的设备。
例如，当 Pixhawk 4 和 DroneCode Probe 通过 USB 连接时，会列出以下设备：

```sh
user@ubuntu:~/PX4-Autopilot$ ls -alh /dev/serial/by-id/
total 0
drwxr-xr-x 2 root root 100 Apr 23 18:57 .
drwxr-xr-x 4 root root  80 Apr 23 18:48 ..
lrwxrwxrwx 1 root root  13 Apr 23 18:48 usb-3D_Robotics_PX4_FMU_v5.x_0-if00 -> ../../ttyACM0
lrwxrwxrwx 1 root root  13 Apr 23 18:57 usb-Black_Sphere_Technologies_Black_Magic_Probe_BFCCB401-if00 -> ../../ttyACM1
lrwxrwxrwx 1 root root  13 Apr 23 18:57 usb-Black_Sphere_Technologies_Black_Magic_Probe_BFCCB401-if02 -> ../../ttyACM2
```

在这种情况下，脚本会自动选择名为 `*Black_Magic_Probe*-if00` 的设备。
但如果使用其他设备，可以通过上述列表发现适当的 ID。

然后使用 `--gdbdev` 参数传递适当的设备，如下所示：

```sh
./poor-mans-profiler.sh --elf=...
```

## 实现细节

### 采样机制

脚本通过定期中断固件执行来捕获堆栈跟踪，这种机制确保了对程序运行状态的全面分析。

### 数据处理

采样数据需要经过以下处理步骤：
1. 原始堆栈跟踪的收集
2. 相似堆栈的合并（折叠）
3. 出现频率的统计
4. 可视化结果的生成

## 注意事项

- **GDB 故障处理**：如果 GDB 出现故障，脚本可能无法检测到并继续运行。
- **超时机制**：建议设置合理的采样时间以避免资源占用。
- **多线程限制**：当前实现不支持多线程环境的分析。

## 结果展示

最终生成的 FlameGraph 可视化结果能够清晰展示：
- 函数调用的热点区域
- 内存使用的分布情况
- 系统调用的频率分布

## 脚本路径

该脚本位于：`platforms/nuttx/Debug/poor-mans-profiler.sh`

## 系统要求

- Ubuntu 20.04 及以上版本
- GCC 9.3.0 及以上版本
- GDB 9.2 及以上版本

## 常见问题

### 无法连接调试器
检查 USB 连接状态并确保调试探针驱动已正确安装。

### 采样数据为空
确认固件已正确加载并处于运行状态。

### 可视化结果异常
检查 FlameGraph 工具的安装路径和权限设置。