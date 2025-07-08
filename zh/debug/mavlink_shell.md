# MAVLink Shell

MAVLink Shell 是一个可通过 MAVLink 协议访问的 _NSH 控制台_，支持通过串口（USB/遥测）或 WiFi（UDP/TCP）链接访问（特别是在基于 NuttX 的系统上，如：Pixhawk、Pixracer 等）。

该控制台可用于运行命令和模块，并显示其输出。虽然控制台无法 _直接_ 显示未由其启动的模块的输出，但可以使用 `dmesg` 命令间接实现（`dmesg -f &` 可用于显示工作队列上运行的其他模块和任务的输出）。

:::tip
[QGroundControl MAVLink 控制台](#qgroundcontrol) 是访问控制台的最简单方式。  
如果系统无法正常启动，应改用 [系统控制台](../debug/system_console.md)。

:::

## 打开 Shell

<a id="qgroundcontrol"></a>

### QGroundControl MAVLink 控制台

访问控制台的最简便方式是使用 [QGroundControl MAVLink 控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)（见 **Analyze View > Mavlink Console**）。

### mavlink_shell.py

你也可以通过终端使用 **mavlink_shell.py** 脚本访问控制台：

1. 关闭 _QGroundControl_。
1. 安装依赖项：

   ```sh
   pip3 install --user pymavlink pyserial
   ```

1. 在 PX4-Autopilot 目录中打开终端并启动控制台：

   ```sh
   # 串口连接
   ./Tools/mavlink_shell.py /dev/ttyACM0
   ```

   ```sh
   # WiFi 连接
   ./Tools/mavlink_shell.py 0.0.0.0:14550
   ```

使用 `mavlink_shell.py -h` 可查看所有可用参数的描述。

## 使用 MAVLink Shell

相关信息请参阅：[PX4 控制台/Shell > 使用控制台/Shell](../debug/consoles.md#using_the_console)。