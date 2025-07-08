# 模块参考：通信

## frsky_telemetry

源码位置: [drivers/telemetry/frsky_telemetry](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/telemetry/frsky_telemetry)

FrSky 通信功能支持。自动检测 D 或 S.PORT 协议。
### 使用方法 {#frsky_telemetry_usage}

```
frsky_telemetry <command> [arguments...]
 Commands:
   start
     [-d <val>]  选择串口设备
                 可选值: <file:dev>，默认值: /dev/ttyS6
     [-t <val>]  扫描超时时间[秒]（默认：无超时）
                 默认值: 0
     [-m <val>]  选择协议（默认：自动检测）
                 可选值: sport|sport_single|sport_single_invert|dtype，默认值:
                 auto

   stop

   status
```

## mavlink

源码位置: [modules/mavlink](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mavlink)


### 描述
该模块实现了 MAVLink 协议，可通过串口连接或 UDP 网络连接使用。
它通过 uORB 与系统通信：部分消息在模块内直接处理（例如任务协议），其他则通过 uORB 发布（例如 vehicle_command）。

流用于以特定频率发送周期性消息，例如机体姿态。
启动 mavlink 实例时，可以指定模式，该模式定义了启用的流及其频率。
对于正在运行的实例，可以通过 `mavlink stream` 命令配置流。

模块可以有多个独立实例，每个实例连接到一个串口设备或网络端口。

### 实现
该实现使用两个线程，发送线程和接收线程。发送线程以固定频率运行，并且当总带宽超过配置速率(`-r`)或物理链路饱和时，会动态降低流的频率。可以通过 `mavlink status` 检查，如果 `rate mult` 小于 1，则说明发生了限速。

**注意**: 部分数据会被两个线程同时访问和修改，因此在修改代码或扩展功能时，需要考虑这一点，以避免竞态条件和数据损坏。

### 示例
在 ttyS1 串口以 921600 波特率启动 mavlink，并设置最大发送速率为 80kB/s:
```
mavlink start -d /dev/ttyS1 -b 921600 -m onboard -r 80000
```

在 UDP 14556 端口启动 mavlink，并启用 50Hz 的 HIGHRES_IMU 消息:
```
mavlink start -u 14556 -r 1000000
mavlink stream -u 14556 -s HIGHRES_IMU -r 50
```

### 使用方法 {#mavlink_usage}

```
mavlink <command> [arguments...]
 Commands:
   start         启动新实例
     [-d <val>]  选择串口设备
                 可选值: <file:dev>，默认值: /dev/ttyS1
     [-b <val>]  波特率（也可使用 p:<param_name>）
                 默认值: 57600
     [-r <val>]  最大发送速率（B/s）（若为0则使用波特率/20）
                 默认值: 0
     [-p]        启用广播
     [-u <val>]  选择 UDP 网络端口（本地）
                 默认值: 14556
     [-o <val>]  选择 UDP 网络端口（远程）
                 默认值: 14550
     [-t <val>]  合作伙伴IP（可通过 -p 标志启用广播）
                 默认值: 127.0.0.1
     [-m <val>]  模式：设置默认流及其频率
                 可选值: custom|camera|onboard|osd|magic|config|iridium|minimal|
                 extvision|extvisionmin|gimbal|uavionix，默认值: normal
     [-n <val>]  wifi/以太网接口名称
                 可选值: <interface_name>
     [-c <val>]  多播地址（可通过 MAV_{i}_BROADCAST 参数启用多播）
                 可选值: 多播地址范围 [239.0.0.0,239.255.255.255]
     [-F <val>]  设置铱星模式的传输频率
                 默认值: 0.0
     [-f]        启用消息转发到其他 Mavlink 实例
     [-w]        等待接收第一个消息后再发送
     [-x]        启用 FTP
     [-z]        强制始终启用硬件流控制
     [-Z]        强制始终禁用硬件流控制

   stop-all      停止所有实例

   stop          停止正在运行的实例
     [-u <val>]  通过本地网络端口选择 Mavlink 实例
     [-d <val>]  通过串口设备选择 Mavlink 实例
                 可选值: <file:dev>

   status        打印所有实例的状态
     [streams]   打印所有启用的流

   stream        配置正在运行实例的流发送频率
     [-u <val>]  通过本地网络端口选择 Mavlink 实例
     [-d <val>]  通过串口设备选择 Mavlink 实例
                 可选值: <file:dev>
     -s <val>    要配置的 Mavlink 流
     -r <val>    频率（Hz）（0 = 关闭，-1 = 设置为默认值）

   boot_complete 启用消息发送。（必须）作为启动脚本的最后一步调用
```

## uorb

源码位置: [systemcmds/uorb](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/uorb)


### 描述
uORB 是模块间通信的内部发布-订阅消息系统。

### 实现
该实现是异步且无锁的，即发布者不会等待订阅者，反之亦然。
这是通过在发布者和订阅者之间设置独立缓冲区实现的。

代码经过优化以最小化内存占用和消息交换的延迟。

消息在 `/msg` 目录中定义。它们在构建时被转换为 C/C++ 代码。

如果编译时包含 ORB_USE_PUBLISHER_RULES，可以通过 uorb 规则文件启用消息发布控制。

### 使用方法

```
uorb <command> [arguments...]
 Commands:
   list          列出所有主题
   info <topic>  显示主题信息
   sub <topic>   订阅主题
   pub <topic>   发布消息到主题
```

### 示例
列出所有主题:
```
uorb list
```

显示特定主题的信息:
```
uorb info vehicle_attitude
```

订阅姿态主题:
```
uorb sub vehicle_attitude
```

发布测试消息:
```
uorb pub vehicle_attitude '{"q":[1,0,0,0],"roll":0,"pitch":0,"yaw":0}'
```