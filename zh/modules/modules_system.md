# 模块参考：系统

## 电池模拟器

源代码：[modules/simulation/battery_simulator](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/battery_simulator)


### 描述



### 用法 {#battery_simulator_usage}

```
battery_simulator <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## battery_status

源码地址: [modules/battery_status](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/battery_status)

### 描述

提供的功能包括:
- 通过 ioctl 接口读取 ADC 驱动的输出，并发布 `battery_status`。

### 实现
在独立线程中运行，并轮询当前选定的陀螺仪主题。

### 使用方法 {#battery_status_usage}

```
battery_status <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## camera_feedback

源代码: [modules/camera_feedback](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/camera_feedback)


### 描述

当图像捕获被触发时，camera_feedback模块会发布`CameraCapture` UORB主题。

如果启用了相机捕获功能，则发布来自相机捕获引脚的触发信息；
否则发布相机被指令触发时的触发信息
(来自`camera_trigger`模块)。

随后`CAMERA_IMAGE_CAPTURED`消息将被发出(由流媒体代码)跟随`CameraCapture`更新。
`CameraCapture`主题也会被记录，并可用于地理标记。


### 实现

当图像捕获被触发时，`CameraTrigger`主题由`camera_trigger`模块发布(`feedback`字段设为`false`)，
如果相机捕获引脚被激活，也可能由`camera_capture`驱动程序发布(带`feedback`字段设为`true`)。

camera_feedback模块订阅`CameraTrigger`。
如果启用了相机捕获功能，则丢弃来自`camera_trigger`模块的主题。
对于未被丢弃的主题，它会创建一个`CameraCapture`主题，包含来自`CameraTrigger`的时间戳信息
和来自机体的位置信息。


### 使用 {#camera_feedback_usage}

```
camera_feedback <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## cdcacm_autostart

源码位置：[drivers/cdcacm_autostart](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/cdcacm_autostart)


### 描述
该模块通过USB监听并根据接收的字节自动配置协议。
支持的协议包括：MAVLink、nsh 和 ublox 串口透传。当参数 SYS_USB_AUTO=2 时，
只要检测到USB VBUS，模块将只尝试启动mavlink。否则模块将持续循环
检测VBUS并在检测到时启动mavlink。

### 用法 {#cdcacm_autostart_usage}

```
cdcacm_autostart <command> [arguments...]
 命令：
   start

   stop

   status        打印状态信息
```

## 指挥官

源代码: [modules/commander](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/commander)


### 描述
指挥官模块包含用于模式切换和故障安全行为的状态机。

### 用法 {#commander_usage}

```
commander <command> [arguments...]
 Commands:
   start
     [-h]        启用HIL模式

   calibrate     运行传感器校准
     mag|baro|accel|gyro|level|esc|airspeed 校准类型
     quick       快速校准 [mag, accel（不推荐）]

   check         执行预飞检查

   arm
     [-f]        强制上锁（不执行预飞检查）

   disarm
     [-f]        强制解锁（空中解锁）

   takeoff

   land

   transition    VTOL转换

   mode          切换飞行模式
     manual|acro|offboard|stabilized|altctl|posctl|position:slow|auto:mission|au
                 to:loiter|auto:rtl|auto:takeoff|auto:land|auto:precland|ext1
                 飞行模式

   pair

   lockdown
     on|off      开启或关闭锁定模式

   set_ekf_origin
     lat, lon, alt 原点纬度、经度、海拔

   lat|lon|alt   原点纬度经度海拔

   poweroff      关闭板载电源（如支持）

   stop

   status        打印状态信息
```

## dataman

源代码：[modules/dataman](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/dataman)


### 描述
通过C API模块为系统其他部分提供持久化存储的简单数据库。
根据板载情况支持多种后端：
- 文件（例如在SD卡上）
- RAM（这显然不是持久化存储）

用于存储结构化数据的不同类型：任务航点、任务状态和地理围栏多边形。
每种类型都有特定的类型和固定的存储项最大数量，从而实现快速随机访问。


### 实现
读写单个条目始终是原子操作。


### 用法 {#dataman_usage}

```
dataman <command> [arguments...]
 命令:
   start
     [-f <val>]  存储文件
                 值: <file>
     [-r]        使用RAM后端(NOT persistent)

     -f 和 -r 选项互斥。若未指定，则使用文件'dataman'

   stop

   status        打印状态信息
```

## dmesg

源代码: [systemcmds/dmesg](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/dmesg)


### 描述

命令行工具，用于显示启动控制台消息。
注意 NuttX 的工作队列和 syslog 输出不会被捕获。


### 示例

在后台持续打印所有消息：
```
dmesg -f &
```

### 用法 {#dmesg_usage}

```
dmesg <command> [arguments...]
 命令:
     [-f]        Follow: wait for new messages
```

## esc_battery

源码：[modules/esc_battery](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/esc_battery)


### 描述
此模块通过使用电调状态的信息并将其发布为电池状态来实现功能。


### 用法 {#esc_battery_usage}

```
esc_battery <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## gyro_calibration

来源: [modules/gyro_calibration](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/gyro_calibration)


### 描述
简单的在线陀螺仪校准。


### 用法 {#gyro_calibration_usage}

```
gyro_calibration <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## gyro_fft

源代码: [modules/gyro_fft](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/gyro_fft)


### 描述


### 用法 {#gyro_fft_usage}

```
gyro_fft <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## heater

源码: [drivers/heater](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/heater)


### 描述
周期性运行于LP工作队列的后台进程，用于调节IMU温度至设定值。

该任务可通过启动脚本中设置SENS_EN_THERMAL或通过CLI在启动时启动。

### 用法 {#heater_usage}

```
heater <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## i2c_launcher

源码位置: [systemcmds/i2c_launcher](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/i2c_launcher)


### 描述
基于检测到的I2C设备启动驱动程序的守护进程。


### 用法 {#i2c_launcher_usage}

```
i2c_launcher <command> [arguments...]
 命令:
   start
     -b <val>    总线编号
     -t <val>    电池索引(用于校准值)(1 或 3)

   stop

   status        打印状态信息
```

## internal_combustion_engine_control

源代码：[modules/internal_combustion_engine_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/internal_combustion_engine_control)


### 描述
		
该模块控制内燃机（ICE）功能，包括：
点火（开启/关闭）、油门和阻风门开度、启动机延迟，以及用户请求。

### 启用

此功能默认未启用，需要与RPM捕获驱动一起在您的板级构建目标中进行配置：

```
CONFIG_MODULES_INTERNAL_COMBUSTION_ENGINE_CONTROL=y
CONFIG_DRIVERS_RPM_CAPTURE=y
```

此外，启用模块需要：
- 设置 [ICE_EN](../advanced_config/parameter_reference.md#ICE_EN)
为 true，并根据需求调整其他 `ICE_` 模块参数。
- 设置 [RPM_CAP_ENABLE](../advanced_config/parameter_reference.md#RPM_CAP_ENABLE) 为 true。

模块输出点火、油门和阻风门的控制信号，并从RPM传感器获取输入。
这些必须在 [执行器配置](../config/actuators.md) 中映射到AUX输出/输入，
配置方式如下所示。

![ICE执行器配置](../../assets/hardware/ice/ice_actuator_setup.png)

### 实现

ICE通过(4)状态机实现：

![架构](../../assets/hardware/ice/ice_control_state_machine.png)

状态机：
		
- 检查 [Rpm.msg](../msg_docs/Rpm.md) 是否更新以确定发动机是否运行
- 允许从以下来源获取用户输入：
  - AUX{N}
  - [VehicleStatus.msg](../msg_docs/VehicleStatus.md) 中的机体状态

模块发布 [InternalCombustionEngineControl.msg](../msg_docs/InternalCombustionEngineControl.md)。
		
架构如下图所示：

![架构](../../assets/hardware/ice/ice_control_diagram.png)
		
<a id="internal_combustion_engine_control_usage"></a>

### 使用 {#internal_combustion_engine_control_usage}

```
internal_combustion_engine_control <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## land_detector

源码: [modules/land_detector](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/land_detector)


### 描述
模块用于检测机体失重和着陆状态，并发布`vehicle_land_detected`话题。
每种机体类型（多旋翼、固定翼、垂直起降、...）提供其专用算法，综合考量多种状态，如指令推力、上锁状态和机体运动。

### 实现
每种类型都在其独立类中实现，共享一个基类。基类维护状态（着陆、可能着陆、地面接触）。每个可能状态在派生类中实现。内部状态的滞回和固定优先级决定了实际land_detector状态。

#### 多旋翼着陆检测器
**ground_contact**（地面接触）：推力设定值和z方向速度必须低于定义的阈值一段时间（GROUND_CONTACT_TRIGGER_TIME_US）。当检测到地面接触时，位置控制器关闭机体x和y方向的推力设定值。

**maybe_landed**（可能着陆）：需要地面接触状态，同时推力设定值阈值更严格且水平方向无速度。触发时间由MAYBE_LAND_TRIGGER_TIME定义。当检测到可能着陆时，位置控制器将推力设定值设为零。

**landed**（着陆）：需要可能着陆状态持续时间为LAND_DETECTOR_TRIGGER_TIME_US。

该模块在HP工作队列中周期运行。

### 使用方法 {#land_detector_usage}

```
land_detector <command> [arguments...]
 命令:
   start         启动后台任务
     fixedwing|multicopter|vtol|rover|airship 选择机体类型

   stop

   status        打印状态信息
```

## load_mon

源码位置: [modules/load_mon](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/load_mon)


### 描述
周期性运行在低优先级工作队列的后台进程，用于计算CPU负载和RAM使用情况，并发布`cpuload`主题。

在NuttX系统上，它还会检查每个进程的堆栈使用情况，如果剩余空间低于300字节，将输出警告信息，该警告也会记录到日志文件中。

### 使用 {#load_mon_usage}

```
load_mon <command> [arguments...]
 Commands:
   start         启动后台任务

   stop

   status        打印状态信息
```

## logger

源码: [modules/logger](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/logger)

### 描述  
系统日志记录器，用于记录可配置的uORB主题和系统printf消息（`PX4_WARN`和`PX4_ERR`）到ULog文件。这些文件可用于系统和飞行性能评估、参数调整、回放和事故分析。

它支持2种后端模式：  
- 文件：将ULog文件写入文件系统（SD卡）  
- MAVLink：通过MAVLink将ULog数据流式传输到客户端（客户端必须支持此功能）  

两种后端可同时启用并使用。  

文件后端支持2种日志文件类型：完整日志（正常日志）和任务日志。任务日志是精简版的ulog文件，可用于地理标记或机体管理。可通过SDLOG_MISSION参数启用和配置。正常日志始终是任务日志的超集。

### 实现  
实现使用两个线程：  
- 主线程，以固定频率运行（或通过话题轮询启动时）并检查数据更新  
- 写线程，将数据写入文件  

中间有一个可配置大小的写缓冲区（另一个固定大小的缓冲区用于任务日志）。缓冲区应足够大以避免数据丢失。  

### 示例  
立即启动日志记录的典型用法：  
```
logger start -e -t
```  

如果已运行：  
```
logger on
```

### 用法 {#logger_usage}  

```
logger <command> [arguments...]
 命令:
   start
     [-m <val>]  后端模式
                 值: file|mavlink|all, 默认: all
     [-x]        通过Aux1遥控通道启用/禁用日志记录
     [-a]        从首次上锁到关闭时记录
     [-e]        启动后立即启用日志记录直到解锁（否则仅在上锁时记录）
     [-f]        记录直到关闭（隐含 -e）
     [-t]        使用日期/时间命名日志目录和文件
     [-r <val>]  日志记录频率（Hz），0 表示无限制
                 默认: 280
     [-b <val>]  日志缓冲区大小（单位：KiB）
                 默认: 12
     [-p <val>]  轮询话题而非固定频率运行（设置此参数时，日志频率和话题间隔将被忽略）
                 值: <topic_name>
     [-c <val>]  日志记录速率因子（数值越大越快）
                 默认: 1.0

   on            立即开始日志记录，覆盖上锁状态（logger 必须在运行）
   off           立即停止日志记录，覆盖上锁状态（logger 必须在运行）
   trigger_watchdog 立即手动触发看门狗
   stop
   status        打印状态信息
```

## mag_bias_estimator

来源: [modules/mag_bias_estimator](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mag_bias_estimator)


### 描述
在线磁力计偏差估计器。


### 用法 {#mag_bias_estimator_usage}

```
mag_bias_estimator <command> [arguments...]
 Commands:
   start         启动后台任务

   stop

   status        显示状态信息
```

## manual_control

源码: [modules/manual_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/manual_control)


### 描述
模块使用manual_control_inputs并发布一个manual_control_setpoint。


### 用法 {#manual_control_usage}

```
manual_control <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## netman

源码位置: [systemcmds/netman](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/netman)


  ### 描述
  网络配置管理器将网络设置保存在非易失性内存中。在启动时会运行`update`选项。如果不存在网络配置，系统将默认设置保存到非易失性存储中并重启。

  #### update

  `netman update`由[启动脚本](../concept/system_startup.md#system-startup)自动运行。
  执行时，`update`选项会检查SD卡根目录是否存在`net.cfg`文件。
  然后从`net.cfg`中将网络设置保存到非易失性内存，
  删除该文件并重启系统。

  #### save

  `save`选项会将非易失性内存中的设置保存到SD卡文件系统中名为`net.cfg`的文件中以便编辑。使用此功能可修改设置。
  保存操作不会立即应用网络设置；用户必须重启飞行堆栈。
  相比之下，`update`命令由启动脚本运行，将设置提交到非易失性内存，
  并重启飞行控制器（此时将使用新设置）。

  #### show

  `show`选项会将`net.cfg`中的网络设置显示到控制台。

  ### 示例
  $ netman save           # 将参数保存到SD卡
  $ netman show           # 显示当前设置
  $ netman update -i eth0 # 执行更新

### 使用方法 {#netman_usage}

```
netman <command> [arguments...]
 命令:
   show          显示当前持久化网络设置到控制台。

   update        检查SD卡中的net.cfg并更新持久化网络设置。

   save          将当前网络参数保存到SD卡。
     [-i <val>]  设置接口名称
                 默认: eth0
```

## pwm_input

来源: [drivers/pwm_input](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/pwm_input)


### 描述
通过定时器捕获ISR在AUX5（或MAIN5）上测量PWM输入，并通过uORB的'pwm_input'消息发布。


### 用法 {#pwm_input_usage}

```
pwm_input <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## rc_update

源代码地址: [modules/rc_update](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/rc_update)


### 描述
rc_update模块负责处理遥控通道映射：读取原始输入通道(`input_rc`)，  
然后应用校准，将遥控通道映射到配置的通道和模式开关，  
最后发布为`rc_channels`和`manual_control_input`。

### 实现
为减少控制延迟，模块被安排在`input_rc`发布时运行。


### 用法 {#rc_update_usage}

```
rc_update <command> [arguments...]
 命令：
   start

   stop

   status        打印状态信息
```

## 回放

源代码: [modules/replay](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/replay)


### 描述
此模块用于回放ULog文件。

有两个环境变量用于配置：`replay`，必须设置为ULog文件名 - 它是要回放的日志文件。第二个是通过`replay_mode`指定的模式：
- `replay_mode=ekf2`：特定于EKF2的回放模式。该模式只能与ekf2模块一起使用，但允许以尽可能快的速度运行回放。
- 其他情况为通用模式：此模式可用于回放任何模块，但回放将以日志记录时的相同速度进行。

该模块通常与uORB发布规则一起使用，以指定应回放哪些消息。
回放模块将只发布日志中发现的所有消息。它还会应用日志中的参数。

回放过程在[系统级回放](https://docs.px4.io/main/en/debug/system_wide_replay.html)页面中有详细说明。

### 用法 {#replay_usage}

```
replay <命令> [参数...]
 命令:
   start         使用ENV变量'replay'指定的日志文件启动回放

   trystart      与'start'相同，但如果未提供日志文件则静默退出

   tryapplyparams 尝试应用日志文件中的参数

   stop

   status        打印状态信息
```

## send_event

源码位置: [modules/events](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/events)


### 描述
后台进程定期在LP工作队列上运行以执行维护任务。
目前该进程仅负责在遥控丢失（RC Loss）时发出声音警报。

这些任务可以通过CLI或uORB主题（如MAVLink中的vehicle_command）启动。


### 用法 {#send_event_usage}

```
send_event <command> [arguments...]
 命令：
   start         启动后台任务

   stop

   status        打印状态信息
```

## sensor_agp_sim

源代码: [modules/simulation/sensor_agp_sim](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/sensor_agp_sim)


### 描述
用于模拟辅助全局位置测量的模块，可选故障模式用于SIH模拟。


### 用法 {#sensor_agp_sim_usage}

```
sensor_agp_sim <command> [arguments...]
 Commands:
   start

   stop

   status        print status info
```

## sensor_arispeed_sim

源代码: [modules/simulation/sensor_airspeed_sim](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/sensor_airspeed_sim)


### 描述

### 用法 {#sensor_arispeed_sim_usage}

```
sensor_arispeed_sim <命令> [参数...]
 命令:
   start

   stop

   status        打印状态信息
```

## sensor_baro_sim

源代码: [modules/simulation/sensor_baro_sim](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/sensor_baro_sim)


### 描述

### 用法 {#sensor_baro_sim_usage}

```
sensor_baro_sim <command> [参数...]
 命令:
   start

   stop

   status        打印状态信息
```

## sensor_gps_sim

源代码: [modules/simulation/sensor_gps_sim](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/sensor_gps_sim)


### 描述

### 使用方法 {#sensor_gps_sim_usage}

```
sensor_gps_sim <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## sensor_mag_sim

源码: [modules/simulation/sensor_mag_sim](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/sensor_mag_sim)


### 描述


### 用法 {#sensor_mag_sim_usage}

```
sensor_mag_sim <command> [arguments...]
 Commands:
   start

   stop

   status        print status info
```

## 传感器

源代码: [modules/sensors](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/sensors)


### 描述
传感器模块是整个系统的核心组件。它从驱动程序获取底层输出，将其转换为更可用的格式，并发布给系统的其他部分。

提供的功能包括：
- 读取传感器驱动程序的输出（`SensorGyro`等）。
  如果存在相同类型的多个传感器，执行投票和故障转移处理。
  然后应用板载旋转和温度校准（如果启用）。最后发布数据；其中一个主题是`SensorCombined`，被系统多个部分使用。
- 确保传感器驱动程序在参数更改或启动时获得更新的校准参数（比例因子和偏移量）。传感器驱动程序使用ioctl接口进行参数更新。为确保正常工作，传感器驱动程序必须在`sensors`启动前已运行。
- 执行传感器一致性检查并发布`SensorsStatusImu`主题。

### 实现
它在自己的线程中运行，并轮询当前选中的陀螺仪主题。


### 用法 {#sensors_usage}

```
sensors <command> [arguments...]
 命令:
   start
     [-h]        以HIL模式启动

   stop

   status        打印状态信息
```

## 系统电源仿真

源码: [modules/simulation/system_power_simulator](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/system_power_simulator)


### 描述


### 用法 {#system_power_simulation_usage}

```
system_power_simulation <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## tattu_can

来源: [drivers/tattu_can](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/tattu_can)


### 描述
用于读取 Tattu 12S 16000mAh 智能电池数据的驱动程序。


### 用法 {#tattu_can_usage}

```
tattu_can <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## temperature_compensation

源码位置: [modules/temperature_compensation](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/temperature_compensation)


### 描述
温度补偿模块可对系统中的所有陀螺仪(accel)、加速度计(gyro)和气压计(baro)进行温度补偿。模块持续监控传感器数据，当检测到温度变化时，会更新对应的sensor_correction主题。模块还可配置为在下次启动时执行系数计算流程，从而在机体经历温度循环时计算热校准系数。


### 使用方法 {#temperature_compensation_usage}

```
temperature_compensation <command> [arguments...]
 命令:
   start         启动模块，用于监控传感器并更新sensor_correction主题

   calibrate     运行温度校准流程
     [-a]        校准加速度计
     [-g]        校准陀螺仪
     [-m]        校准磁力计
     [-b]        校准气压计(若未指定任何参数，则校准所有传感器)

   stop

   status        打印状态信息
```

## time_persistor

源码: [modules/time_persistor](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/time_persistor)


### 描述
周期性地将RTC时间写入文件，并在启动时重新加载该值。
这使得仅具有软件RTC（非电池供电）的系统也能实现单调时间。
通过系统时间（system_time）显式设置时间向后调整仍有可能。


### 用法 {#time_persistor_usage}

```
time_persistor <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## tune_control

Source: [systemcmds/tune_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/tune_control)


### 描述

用于控制和测试（外部）音调的命令行工具。

音调用于提供可听通知和警告（例如系统解锁、获得定位等时）。
该工具需要运行一个能处理tune_control uorb主题的驱动。

关于音调格式和预定义系统音调的说明请参考：
https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/tunes/tune_definition.desc

### 示例

播放系统音调 #2:
```
tune_control play -t 2
```

### 用法 {#tune_control_usage}

```
tune_control <command> [arguments...]
 命令:
   play          播放系统音调或单个音符。
     error       播放错误音调
     [-t <val>]  播放预定义系统音调
                 默认值: 1
     [-f <val>]  音符的频率（Hz，0-22kHz）
     [-d <val>]  音符的时长（微秒）
     [-s <val>]  音符的音量等级（0-100）
                 默认值: 40
     [-m <val>]  以字符串形式表示的旋律
                 值: <string> - 例如 "MFT200e8a8a"

   libtest       测试库

   stop          停止播放（用于重复播放音调）
```

## uxrce_dds_client

源代码: [modules/uxrce_dds_client](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/uxrce_dds_client)

### 描述
用于通过串口或UDP与Agent通信的UXRCE-DDS客户端，处理uORB主题。

### 示例
```
uxrce_dds_client start -t serial -d /dev/ttyS3 -b 921600
uxrce_dds_client start -t udp -h 127.0.0.1 -p 15555
```

### 用法 {#uxrce_dds_client_usage}

```
uxrce_dds_client <command> [arguments...]
 命令:
   start
     [-t <val>]  传输协议
                 值：串口|UDP，默认：UDP
     [-d <val>]  串口设备
                 值：<文件:dev>
     [-b <val>]  波特率（也可使用p:<参数名>）
                 默认：0
     [-h <val>]  Agent IP地址。若未指定，则使用UXRCE_DDS_AG_IP
                 值：<IP>
     [-p <val>]  Agent监听端口。若未指定，则使用
                 UXRCE_DDS_PRT
     [-n <val>]  客户端DDS命名空间

   stop

   status        打印状态信息
```

## 工作队列

源码: [systemcmds/work_queue](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/work_queue)


### 描述

命令行工具，用于显示工作队列状态。


### 用法 {#work_queue_usage}

```
work_queue <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```