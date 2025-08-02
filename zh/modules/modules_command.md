

# 模块参考：命令

## actuator_test

来源：[systemcmds/actuator_test](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/actuator_test)


用于测试执行器的工具。

警告：在使用此命令前请移除所有螺旋桨。

### 使用方法 {#actuator_test_usage}

```
actuator_test <command> [arguments...]
 Commands:
   set           设置执行器到特定输出值

 可通过电机、舵机或功能直接指定执行器：
     [-m <val>]  测试的电机 (1...8)
     [-s <val>]  测试的舵机 (1...8)
     [-f <val>]  直接指定功能
     -v <val>    值 (-1...1)
     [-t <val>]  超时时间(秒)(未设置时运行交互模式)
                 默认值: 0

   iterate-motors 依次启动并停止所有电机

   iterate-servos 依次偏转所有舵机
```

## bl_update

源码：[systemcmds/bl_update](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/bl_update)

工具，用于从文件烧录引导加载程序

### 使用方法 {#bl_update_usage}

```
bl_update [arguments...]
   setopt        设置选项位以解锁FLASH（仅当处于锁定状态时需要）

   <file>        Bootloader bin文件
```

## bsondump

源码：[systemcmds/bsondump](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/bsondump)

用于从文件读取BSON并打印或输出文档大小的工具。

### 使用方法 {#bsondump_usage}

```
bsondump [参数...]
     <file>      要解码并打印的BSON文件。
```

## dumpfile

源码位置: [systemcmds/dumpfile](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/dumpfile)

文件转储工具。以二进制模式（不将LF替换为CR LF）打印文件大小和内容到标准输出。

### 使用方法 {#dumpfile_usage}

```
dumpfile [参数...]
     <文件>      要转储的文件
```

## dyn

Source: [系统命令/dyn](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/dyn)

### 描述
加载并运行一个动态PX4模块，该模块未被编译到PX4二进制文件中。

### 示例
```
dyn ./hello.px4mod start
```

### 用法 {#dyn_usage}

```
dyn [arguments...]
     <file>      包含模块的文件
     [arguments...] 传递给模块的参数
```

## 故障

Source: [系统命令/failure](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/failure)

### 描述  
将故障注入系统。

### 实现  
该系统命令通过 uORB 发送机体指令以触发故障。

### 示例  
通过停止GPS来测试GPS故障保护机制：  

failure gps off

### 用法 {#failure_usage}

```
failure [参数...]
   help          显示此帮助文本

   gps|...       指定组件

   ok|off|...    指定故障类型
     [-i <val>]  传感器实例 (0=所有)
                 默认值：0
```

## 通用输入输出

源代码: [systemcmds/gpio](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/gpio)

### 描述
该命令用于读取和写入GPIOs
```
gpio read <PORT><PIN>/<DEVICE> [PULLDOWN|PULLUP] [--force]
gpio write <PORT><PIN>/<DEVICE> <VALUE> [PUSHPULL|OPENDRAIN] [--force]
```

### 示例
读取配置为上拉的端口H引脚4的值，此时其处于高电平
```
gpio read H4 PULLUP
```
1 正确

将端口E引脚7的输出值设置为高电平
```
gpio write E7 1 --force
```

将设备/dev/gpio1的输出值设置为高电平
```
gpio write /dev/gpio1 1
```

### 使用方法 {#gpio_usage}

```
gpio [arguments...]
   read
     <PORT><PIN>/<DEVICE> GPIO端口和引脚或设备
     [PULLDOWN|PULLUP] 下拉/上拉
     [--force]   强制（忽略板载gpio列表）

   write
     <PORT> <PIN> GPIO端口和引脚
     <VALUE>     要写入的值
     [PUSHPULL|OPENDRAIN] 推挽/开漏
     [--force]   强制（忽略板载gpio列表）
```

## hardfault_log

源码位置：[systemcmds/hardfault_log](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/hardfault_log)

硬故障工具

用于启动脚本中处理硬故障

### 使用方法 {#hardfault_log_usage}

```
hardfault_log <command> [arguments...]
 命令:
   check         检查是否存在未提交的严重错误

   rearm         清除未提交的严重错误

   fault         生成一个严重错误（该命令会导致系统崩溃 :)）
     [0|1|2|3]   严重错误类型: 0=除以0, 1=断言失败, 2=跳转到0x0,
                 3=写入0x0 (默认=0)

   commit        将未提交的严重错误写入 /fs/microsd/fault_%i.txt（并
                 重置计数器，但不重启系统）

   count         读取重启计数器，统计未提交严重错误的重启次数（结果
                 作为程序的退出码返回）

   reset         重置重启计数器
```

## hist

源码: [systemcmds/hist](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/hist)

命令行工具用于显示 px4 消息历史记录。该命令没有参数。

### 用法 {#hist_usage}

```
hist [arguments...]
```

## i2cdetect

Source: [systemcmds/i2cdetect](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/i2cdetect)

用于扫描特定总线上I2C设备的工具。

### 用法 {#i2cdetect_usage}

```
i2cdetect [arguments...]
     [-b <val>]  I2C总线
                 默认值：1
```

## LED控制

源代码：[systemcmds/led_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/led_control)

### 描述
用于控制和测试（外部）LED 的命令行工具。

要使用该工具，请确保有一个驱动程序正在运行，该驱动程序处理 `led_control` uorb topic。

存在不同的优先级，例如一个模块可以以低优先级设置颜色，而另一个模块可以以高优先级闪烁 N 次，LED 会在闪烁后自动返回到低优先级状态。`reset` 命令也可用于返回到低优先级。

### 示例
闪烁第一个LED 5次为蓝色：
```
led_control blink -c blue -l 0 -n 5
```

### 使用方法 {#led_control_usage}

```
led_control <命令> [参数...]
 命令:
   test          运行测试模式

   on            打开LED

   off           关闭LED

   reset         重置LED优先级

   blink         闪烁LED N次
     [-n <val>]  闪烁次数
                 默认值：3
     [-s <val>]  设置闪烁速度
                 值：fast|normal|slow， 默认值：normal

   breathe       持续呼吸灯效果

   flash         快速闪烁两次后关闭，频率1Hz

 以下参数适用于除 'test' 以外的所有命令:
     [-c <val>]  颜色
                 值：red|blue|green|yellow|purple|amber|cyan|white， 默认值：
                 white
     [-l <val>]  控制哪个LED：0, 1, 2, ... （默认=all）
     [-p <val>]  优先级
                 默认值：2
```

## 监听器

源代码：[systemcmds/topic_listener](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/topic_listener)

工具用于监听uORB主题并将数据打印到控制台。

监听器随时通过按Ctrl+C、Esc或Q退出。

### 用法 {#listener_usage}

```
listener <command> [arguments...]
 Commands:
     <topic_name> uORB topic name
     [-i <val>]  主题实例
                 默认值: 0
     [-n <val>]  消息数量
                 默认值: 1
     [-r <val>]  订阅速率（如果为0则无限制）
                 默认值: 0
```

## mfd

源代码位置： [systemcmds/mft](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/mft)

工具用于与清单文件交互

### 用法 {#mfd_usage}

```
mfd <command> [arguments...]
 Commands:
   query         如果不存在则返回 true
```

## mft_cfg

来源：[系统命令/mft_cfg](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/mft_cfg)

用于设置和获取清单配置的工具

### 用法 {#mft_cfg_usage}

```
mft_cfg <命令> [参数...]
 命令:
   get           获取清单配置

   set           设置清单配置

   reset         重置清单配置
     hwver|hwrev 选择类型：MTD_MTF_VER|MTD_MTF_REV
     -i <val>    用于设置扩展硬件ID的参数（id == 版本对于<hwver>，id == 修订对于<hwrev>）
```

## mtd

源代码：[systemcmds/mtd](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/mtd)

用于挂载和测试分区的工具（基于主板定义的FRAM/EEPROM存储）

### 用法 {#mtd_usage}

```
mtd <command> [arguments...]
 命令:
   status        打印状态信息

   readtest      执行读取测试

   rwtest        执行读写测试

   erase         擦除分区

 命令 'readtest' 和 'rwtest' 包含可选的实例索引:
     [-i <val>]  存储索引(如果开发板包含多个存储设备)
                 默认值: 0

 命令 'readtest'、'rwtest' 和 'erase' 包含可选参数:
     [<partition_name1> [<partition_name2> ...]] 分区名称(例如:
                 /fs/mtd_params)，未提供时使用系统默认分区
```

## nshterm

来源：[systemcmds/nshterm](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/nshterm)

在指定端口启动一个NSH shell。

此功能之前用于在USB串口启动shell。
现在运行mavlink，并且可以通过mavlink使用shell。

### 用法 {#nshterm_usage}

```
nshterm [arguments...]
     <file:dev>  Device on which to start the shell (eg. /dev/ttyACM0)
```

## 参数

源码位置: [systemcmds/param](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/param)

### 描述  
通过 shell 或脚本访问和操作参数的命令。  

例如在启动脚本中用于设置机体特定参数。  

参数在修改时（例如通过 `param set`）会自动保存。通常存储在 FRAM 或 SD 卡中。`param select` 可用于更改后续保存的存储位置（每次启动后都需要重新配置）。  

如果启用了基于 FLASH 的后端（此功能在编译时启用，例如用于 Intel Aero 或 Omnibus），则 `param select` 无效，默认始终使用 FLASH 后端。但 `param save/load <file>` 仍可用于文件的写入/读取操作。  

每个参数都有一个“已使用”标记，该标记在启动时读取参数时设置。它用于仅向地面控制站显示相关参数。

### 示例
更改机体并确保加载了机体的默认参数：
```
param set SYS_AUTOSTART 4001
param set SYS_AUTOCONFIG 1
reboot
```

### 用法 {#param_usage}

```
param <命令> [参数...]
 命令:
   load          从文件加载参数（覆盖所有）
     [<文件>]    文件名（未指定时使用默认）

   import        从文件导入参数
     [<文件>]    文件名（未指定时使用默认）

   save          将参数保存到文件
     [<文件>]    文件名（未指定时使用默认）

   select        选择默认文件
     [<文件>]    文件名

   select-backup 选择默认备份文件
     [<文件>]    文件名

   show          显示参数值
     [-a]        显示所有参数（不仅限于已使用的）
     [-c]        仅显示已修改的参数（包括未使用的）
     [-q]        静默模式，仅打印参数值（参数名需完全匹配）
     [<筛选器>]  按参数名过滤（允许结尾通配符，例如 sys_*）

   show-for-airframe 显示适用于当前机型的已修改参数

   status        打印参数系统的状态

   set           设置参数值
     <param_name> <值> 参数名和要设置的值
     [fail]      如果提供，当参数不存在时命令失败

   set-default   设置参数默认值
     [-s]        如果提供，当参数不存在时静默错误
     <param_name> <值> 参数名和要设置的值
     [fail]      如果提供，当参数不存在时命令失败

   compare       比较参数与指定值。当值相等时命令成功
     [-s]        如果提供，当参数不存在时静默错误
     <param_name> <值> 要比较的参数名和值

   greater       比较参数与指定值。当参数值大于该值时命令成功
     [-s]        如果提供，当参数不存在时静默错误
     <param_name> <值> 要比较的参数名和值
     <param_name> <值> 要比较的参数名和值

   touch         标记参数为已使用
     [<param_name1> [<param_name2>]] 参数名（一个或多个）

   reset         仅重置指定参数为默认值
     [<param1> [<param2>]] 要重置的参数名（允许结尾通配符）

   reset_all     重置所有参数为默认值
     [<exclude1> [<exclude2>]] 不重置匹配的参数（允许结尾通配符）

   index         按给定索引显示参数
     <index>     索引：整数 >= 0

   index_used    按给定索引显示已使用的参数
     <index>     索引：整数 >= 0

   find          显示参数的索引
     <param>     参数名
```

## 有效载荷投放器

Source: [modules/payload_deliverer](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/payload_deliverer)

### 描述  
通过Gripper或Winch进行有效载荷交付，并设置适当的超时/反馈传感器，  
并内部返回交付结果作为确认

### 用法 {#payload_deliverer_usage}

```
payload_deliverer <command> [arguments...]
 Commands:
   start

   gripper_test  测试夹爪的释放与抓取序列

   gripper_open  打开夹爪

   gripper_close 关闭夹爪

   stop

   status        打印状态信息
```

## perf

源：[systemcmds/perf](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/perf)

用于打印性能计数器的工具

### 用法 {#perf_usage}

```
perf [参数...]
   reset         重置所有计数器

   latency       打印HRT定时器延迟直方图

 如果未提供参数则打印所有性能计数器
```

## 重启

Source: [systemcmds/reboot](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/reboot)

重启系统

### 使用方法 {#reboot_usage}

```
reboot [arguments...]
     [-b]        重启进入引导加载程序
     [-i]        重启进入ISP（第一阶段引导加载程序）
     [lock|unlock] 获取/释放关机锁（用于测试）
```

## sd_bench

源代码: [systemcmds/sd_bench](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/sd_bench)

测试SD卡的速度

### 使用方法 {#sd_bench_usage}

```
sd_bench [参数...]
     [-b <val>]  每次读/写的块大小
                 默认值: 4096
     [-r <val>]  运行次数
                 默认值: 5
     [-d <val>]  单次运行持续时间（毫秒）
                 默认值: 2000
     [-k]        保留测试文件
     [-s]        在每次块操作后调用fsync（默认=每次运行结束时）
     [-u]        用未对齐数据测试性能
     [-U]        用强制字节未对齐数据测试性能
     [-v]        验证数据和块编号
```

## sd_stress

Source: [systemcmds/sd_stress](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/sd_stress)

对SD卡的测试操作

### 用法 {#sd_stress_usage}

```
sd_stress [参数...]
     [-r <val>]  运行次数
                 默认值: 5
     [-b <val>]  字节数
                 默认值: 100
```

## 串口直通

源码：[systemcmds/serial_passthru](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/serial_passthru)

将数据从一个设备传递到另一个设备。

可以用于将u-center通过USB连接到串口上的GPS。

### 用法 {#serial_passthru_usage}

```
serial_passthru [arguments...]
     -e <val>    外部设备路径
                 可选值: <file:dev>
     -d <val>    内部设备路径
                 可选值: <file:dev>
     [-b <val>]  波特率
                 默认值: 115200
     [-t]        在内部设备上跟踪外部设备的波特率
```

## 系统时间

来源：[systemcmds/system_time](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/system_time)

### 描述

设置和获取系统时间的命令行工具。

### 示例

设置系统时间并读取回显
```
system_time set 1600775044
system_time get
```

### 用法 {#system_time_usage}

```
system_time <command> [arguments...]
 Commands:
   set           设置系统时间，提供时间以Unix时间戳格式

   get           获取系统时间
```

## top

源代码: [systemcmds/top](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/top)

监控运行中的进程及其CPU、堆栈使用情况、优先级和状态

### 用法 {#top_usage}

```
top [arguments...]
   once          仅打印负载一次
```

## usb_connected

源代码: [systemcmds/usb_connected](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/usb_connected)

用于检查USB是否连接的实用工具。以前用于启动脚本中。
返回值为0表示USB已连接，1表示未连接。

### 用法 {#usb_connected_usage}

```
usb_connected [arguments...]
```

## 版本信息

源代码: [systemcmds/ver](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/ver)

用于打印各种版本信息的工具

### 使用方法 {#ver_usage}

```
ver <命令> [参数...]
 命令:
   hw            硬件架构

   mcu           MCU信息

   git           git版本信息

   bdate         构建日期和时间

   gcc           编译器信息

   bdate         构建日期和时间

   px4guid       PX4 GUID

   uri           构建URI

   all           打印所有版本

   hwcmp         比较硬件版本（匹配时返回 0）
     <hw> [<hw2>] 要比较的硬件（例如 PX4_FMU_V4）。如果指定多个，将使用 OR 比较

   hwtypecmp     比较硬件类型（匹配时返回 0）
     <hwtype> [<hwtype2>] 要比较的硬件类型（例如 V2）。如果指定多个，将使用 OR 比较

   hwbasecmp     比较硬件基础（匹配时返回 0）
     <hwbase> [<hwbase2>] 要比较的硬件类型（例如 V2）。如果指定多个，将使用 OR 比较
```