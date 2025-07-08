# 模块参考：Command## 执行器测试

源代码：[systemcmds/actuator_test](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/actuator_test)

用于测试执行器的工具。

WARNING: 使用此命令前请移除所有螺旋桨。

### 使用方法 {#actuator_test_usage}

```
actuator_test <command> [arguments...]
 命令:
   set           将执行器设置为特定输出值

 可通过电机、舵机或功能直接指定执行器：
     [-m <val>]  要测试的电机 (1...8)
     [-s <val>]  要测试的舵机 (1...8)
     [-f <val>]  直接指定功能
     -v <val>    值 (-1...1)
     [-t <val>]  超时时间（秒）（未设置时运行交互模式）
                 默认值：0

   iterate-motors 循环所有电机，依次启动和停止

   iterate-servos 循环所有舵机，依次偏转
```

## bl_update

源代码: [systemcmds/bl_update](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/bl_update)

用于从文件中烧录引导程序的工具
### 用法 {#bl_update_usage}

```
bl_update [arguments...]
   setopt        设置选项位以解锁FLASH（仅在锁定状态下需要）

   <file>        引导程序二进制文件
```

## bsondump

源码：[systemcmds/bsondump](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/bsondump)

用于从文件中读取 BSON 并打印或输出文档大小的工具。
### 用法 {#bsondump_usage}

```
bsondump [arguments...]
     <file>      要解码并打印的 BSON 文件。
```

## dumpfile

源码: [systemcmds/dumpfile](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/dumpfile)

文件转储工具。以二进制模式（不将 LF 替换为 CR LF）打印文件大小和内容到 stdout。
### 用法 {#dumpfile_usage}

```
dumpfile [arguments...]
     <file>      要转储的文件
```

## dyn  

源代码: [systemcmds/dyn](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/dyn)  


### 描述  
加载并运行一个未编译到PX4二进制文件中的动态PX4模块。


### 示例  
```
dyn ./hello.px4mod start
```


### 用法 {#dyn_usage}  

```
dyn [arguments...]
     <file>      包含模块的文件
     [arguments...] 模块的参数
```

## failure

源代码: [systemcmds/failure](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/failure)

### 描述
向系统注入故障。

### 实现
该系统命令通过uORB发送机体命令以触发故障。

### 示例
通过关闭GPS测试GPS故障保护：

failure gps off

### 用法 {#failure_usage}

```
failure [arguments...]
   help          显示此帮助文本

   gps|...       指定组件

   ok|off|...    指定故障类型
     [-i <val>]  传感器实例 (0=全部)
                 默认值: 0
```

## gpio

源代码: [systemcmds/gpio](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/gpio)


### 描述
该命令用于读取和写入GPIO
```
gpio read <PORT><PIN>/<DEVICE> [PULLDOWN|PULLUP] [--force]
gpio write <PORT><PIN>/<DEVICE> <VALUE> [PUSHPULL|OPENDRAIN] [--force]
```


### 示例
读取配置为上拉的H口4号引脚的高电平值
```
gpio read H4 PULLUP
```
1 OK

将E口7号引脚输出设为高电平
```
gpio write E7 1 --force
```

将设备/dev/gpio1的输出设为高电平
```
gpio write /dev/gpio1 1
```


### 用法 {#gpio_usage}

```
gpio [arguments...]
   read
     <PORT><PIN>/<DEVICE> GPIO端口和引脚或设备
     [PULLDOWN|PULLUP] 下拉/上拉
     [--force]   强制（忽略板载GPIO列表）

   write
     <PORT> <PIN> GPIO端口和引脚
     <VALUE>     写入值
     [PUSHPULL|OPENDRAIN] 推挽/开漏
     [--force]   强制（忽略板载GPIO列表）
```

## hardfault_log

源码地址: [systemcmds/hardfault_log](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/hardfault_log)

硬故障实用工具

用于启动脚本中处理硬故障

### 用法 {#hardfault_log_usage}

```
hardfault_log <command> [arguments...]
 命令:
   check         检查是否存在未提交的硬故障

   rearm         丢弃未提交的硬故障

   fault         生成硬故障（此命令将导致系统崩溃 :)
     [0|1|2|3]   硬故障类型: 0=除以0, 1=断言失败, 2=跳转到0x0,
                 3=写入0x0 (默认=0)

   commit        将未提交的硬故障写入 /fs/microsd/fault_%i.txt (并
                 重置, 但不会重启)

   count         读取重启计数器, 记录未提交硬故障的重启次数 (通过
                 程序的退出码返回)

   reset         重置重启计数器
```

## hist

源码: [systemcmds/hist](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/hist)

用于显示 px4 消息历史的命令行工具。该工具没有参数。
### 使用方法 {#hist_usage}

```
hist [arguments...]
```

## i2cdetect

源代码: [systemcmds/i2cdetect](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/i2cdetect)

用于在特定总线上扫描I2C设备的工具。
### 使用方法 {#i2cdetect_usage}

```
i2cdetect [arguments...]
     [-b <val>]  I2C总线
                 默认值：1
```

## led_control

源代码: [systemcmds/led_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/led_control)


### 描述
命令行工具，用于控制和测试(外部)LED。

使用前需确保有驱动正在运行，该驱动处理led_control uorb主题。

存在不同的优先级，例如一个模块可以用低优先级设置颜色，另一个模块可以用高优先级使LED闪烁N次，闪烁结束后LED会自动恢复到低优先级状态。`reset`命令也可用于返回到低优先级状态。

### 示例
让第一个LED闪烁5次蓝色：
```
led_control blink -c blue -l 0 -n 5
```


### 用法 {#led_control_usage}

```
led_control <command> [arguments...]
 命令:
   test          运行测试模式

   on            打开LED

   off           关闭LED

   reset         重置LED优先级

   blink         使LED闪烁N次
     [-n <val>]  闪烁次数
                 默认值: 3
     [-s <val>]  设置闪烁速度
                 可选值: fast|normal|slow， 默认值: normal

   breathe       持续渐亮渐暗LED

   flash         两次快速闪烁后关闭，频率为1Hz

 以下参数适用于除'test'外的所有命令:
     [-c <val>]  颜色
                 可选值: red|blue|green|yellow|purple|amber|cyan|white， 默认值:
                 white
     [-l <val>]  要控制的LED编号: 0, 1, 2, ... (默认=all)
     [-p <val>]  优先级
                 默认值: 2
```

## 监听器

源代码: [systemcmds/topic_listener](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/topic_listener)

用于监听uORB主题并将数据打印到控制台的工具。

在任何时候通过按下Ctrl+C、Esc或Q来退出监听器。

### 用法 {#listener_usage}

```
listener <command> [arguments...]
 命令:
     <topic_name> uORB主题名称
     [-i <val>]  主题实例
                 默认: 0
     [-n <val>]  消息数量
                 默认: 1
     [-r <val>]  订阅速率（0表示无限制）
                 默认: 0
```

## mfd

源代码：[systemcmds/mft](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/mft)

与清单文件交互的实用工具
### 用法 {#mfd_usage}

```
mfd <command> [arguments...]
 命令：
   query         如果不存在则返回 true
```

## mft_cfg

源码地址: [systemcmds/mft_cfg](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/mft_cfg)

用于设置和获取清单配置的工具  
### 用法 {#mft_cfg_usage}

```
mft_cfg <command> [arguments...]
 命令:
   get           获取清单配置

   set           设置清单配置

   reset         重置清单配置
     hwver|hwrev 选择类型: MTD_MTF_VER|MTD_MTF_REV
     -i <val>    设置扩展硬件ID的参数 (id == 版本用于 <hwver>, id == 修订用于 <hwrev>)
```

## mtd

源码: [systemcmds/mtd](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/mtd)

用于挂载和测试分区的工具（基于开发板定义的FRAM/EEPROM存储）
### 用法 {#mtd_usage}

```
mtd <command> [arguments...]
 命令:
   status        打印状态信息

   readtest      执行读取测试

   rwtest        执行读写测试

   erase         擦除分区

 命令 'readtest' 和 'rwtest' 包含可选的实例索引:
     [-i <val>]  存储索引（如果开发板包含多个存储）
                 默认值: 0

 命令 'readtest'、'rwtest' 和 'erase' 包含可选参数:
     [<partition_name1> [<partition_name2> ...]] 分区名称（例如:
                 /fs/mtd_params），未提供时使用系统默认
```

## nshterm

源代码: [systemcmds/nshterm](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/nshterm)

在给定端口启动NSH壳层。

此功能曾用于在USB串行端口启动壳层。现在该端口运行mavlink，可以通过mavlink使用壳层。

### 用法 {#nshterm_usage}

```
nshterm [arguments...]
     <file:dev>  启动壳层的设备（例如 /dev/ttyACM0）
```

## param

来源: [systemcmds/param](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/param)


### 描述
通过shell或脚本访问和操作参数的命令。

例如在启动脚本中设置机体特定参数时使用。

参数在更改时会自动保存，例如通过 `param set`。它们通常存储到FRAM或SD卡中。`param select` 可用于更改后续保存的存储位置（每次启动后都需要重新配置）。

如果启用了基于FLASH的后端（这在编译时完成，例如用于Intel Aero或Omnibus），
`param select` 无效，默认始终使用FLASH后端。不过 `param save/load <file>` 仍可用于写入/读取文件。

每个参数都有一个'used'标志，该标志在启动时读取参数时设置。它用于向地面控制站仅显示相关参数。

### 示例
更改机体并确保加载该机体的默认参数：
```
param set SYS_AUTOSTART 4001
param set SYS_AUTOCONFIG 1
reboot
```

### 用法 {#param_usage}

```
param <command> [arguments...]
 命令:
   load          从文件加载参数（覆盖所有）
     [<file>]    文件名（未指定时使用默认）

   import        从文件导入参数
     [<file>]    文件名（未指定时使用默认）

   save          保存参数到文件
     [<file>]    文件名（未指定时使用默认）

   select        选择默认文件
     [<file>]    文件名

   select-backup 选择默认备份文件
     [<file>]    文件名

   show          显示参数值
     [-a]        显示所有参数（不仅限于已使用）
     [-c]        仅显示已更改参数（包括未使用）
     [-q]        静默模式，仅打印参数值（名称需完全匹配）
     [<filter>]  按参数名过滤（允许结尾通配符，例如 sys_*）

   show-for-airframe 显示机体配置的已更改参数

   status        打印参数系统的状态

   set           设置参数值
     <param_name> <value> 参数名和要设置的值
     [fail]      如果提供，当参数未找到时命令失败

   set-default   设置参数默认值
     [-s]        如果提供，当参数不存在时静默错误
     <param_name> <value> 参数名和要设置的值
     [fail]      如果提供，当参数未找到时命令失败

   compare       比较参数与值。当相等时命令成功
     [-s]        如果提供，当参数不存在时静默错误
     <param_name> <value> 要比较的参数名和值

   greater       比较参数与值。当参数大于该值时命令成功
     [-s]        如果提供，当参数不存在时静默错误
     <param_name> <value> 要比较的参数名和值
     <param_name> <value> 要比较的参数名和值

   touch         标记参数为已使用
     [<param_name1> [<param_name2>]] 参数名（一个或多个）

   reset         仅将指定参数重置为默认值
     [<param1> [<param2>]] 要重置的参数名（允许结尾通配符）

   reset_all     将所有参数重置为默认值
     [<exclude1> [<exclude2>]] 不重置匹配的参数（允许结尾通配符）

   index         显示给定索引的参数
     <index>     索引：整数 >= 0

   index_used    显示给定索引的已使用参数
     <index>     索引：整数 >= 0

   find          显示参数的索引
     <param>     参数名
```

## 载荷投送器

源码位置: [modules/payload_deliverer](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/payload_deliverer)


### 描述
通过夹爪或绞车配合合适的超时设置/反馈传感器配置执行载荷投送，  
并在内部返回投送结果作为确认信息

### 使用方法 {#payload_deliverer_usage}

```
payload_deliverer <command> [arguments...]
 命令:
   start

   gripper_test  测试夹爪的释放与抓取序列

   gripper_open  打开夹爪

   gripper_close 关闭夹爪

   stop

   status        打印状态信息
```

## perf

来源: [systemcmds/perf](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/perf)

打印性能计数器的工具
### 用法 {#perf_usage}

```
perf [arguments...]
   reset         重置所有计数器

   latency       打印 HRT timer latency histogram

 无参数时打印所有性能计数器
```

## 重启

来源: [systemcmds/reboot](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/reboot)

重启系统
### 用法 {#reboot_usage}

```
reboot [arguments...]
     [-b]        重启进入引导加载程序
     [-i]        重启进入ISP(一级引导加载程序)
     [lock|unlock] 获取/释放关机锁定(用于测试)
```

## sd_bench

来源：[systemcmds/sd_bench](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/sd_bench)

测试SD卡的速度
### 使用方式 {#sd_bench_usage}

```
sd_bench [arguments...]
     [-b <val>]  每次读写的数据块大小
                 默认值：4096
     [-r <val>]  运行次数
                 默认值：5
     [-d <val>]  单次运行持续时间（单位：ms）
                 默认值：2000
     [-k]        保留测试文件
     [-s]        每次写入块后调用fsync（默认：每次运行结束时调用）
     [-u]        测试未对齐数据的性能
     [-U]        测试强制字节未对齐数据的性能
     [-v]        验证数据和块编号
```

## sd_stress

源代码: [systemcmds/sd_stress](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/sd_stress)

测试SD卡的操作
### 用法 {#sd_stress_usage}

```
sd_stress [arguments...]
     [-r <val>]  运行次数
                 默认值: 5
     [-b <val>]  字节数
                 默认值: 100
```

## serial_passthru

源代码: [systemcmds/serial_passthru](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/serial_passthru)

将数据从一个设备传递到另一个设备。

此功能可用于通过USB连接u-center软件时，将串口上的GPS数据传递出去。

### 用法 {#serial_passthru_usage}

```
serial_passthru [arguments...]
     -e <val>    外部设备路径
                 可选值: <file:dev>
     -d <val>    内部设备路径
                 可选值: <file:dev>
     [-b <val>]  波特率
                 默认值: 115200
     [-t]        跟踪外部设备的波特率并应用到内部设备
```

## system_time

来源: [systemcmds/system_time](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/system_time)


### 描述

命令行工具，用于设置和获取系统时间。

### 示例

设置系统时间并读取回显
```
system_time set 1600775044
system_time get
```

### 用法 {#system_time_usage}

```
system_time <command> [arguments...]
 命令:
   set           设置系统时间，提供时间需使用Unix纪元时间格式

   get           获取系统时间
```

## 进程监控

源码位置: [systemcmds/top](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/top)

监控正在运行的进程及其CPU、堆栈使用情况、优先级和状态
### 用法 {#top_usage}

```
top [arguments...]
   once          打印负载一次
```

## usb_connected

源码位置：[systemcmds/usb_connected](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/usb_connected)

用于检查USB是否连接的实用工具。曾用于启动脚本中。
返回值为0表示USB已连接，1表示未连接。
### 使用方法 {#usb_connected_usage}

```
usb_connected [arguments...]
```

## ver

源码位置: [systemcmds/ver](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/ver)

打印各类版本信息的工具
### 使用方法 {#ver_usage}

```
ver <命令> [参数...]
 命令：
   hw            硬件架构

   mcu           MCU信息

   git           git版本信息

   bdate         构建日期和时间

   gcc           编译器信息

   bdate         构建日期和时间

   px4guid       PX4 GUID

   uri           构建URI

   all           打印所有版本信息

   hwcmp         比较硬件版本（匹配时返回0）
     <hw> [<hw2>] 要比较的硬件（例如PX4_FMU_V4）。当指定多个硬件时使用OR比较

   hwtypecmp     比较硬件类型（匹配时返回0）
     <hwtype> [<hwtype2>] 要比较的硬件类型（例如V2）。当指定多个硬件类型时使用OR比较

   hwbasecmp     比较硬件基型（匹配时返回0）
     <hwbase> [<hwbase2>] 要比较的硬件基型（例如V2）。当指定多个硬件基型时使用OR比较
```