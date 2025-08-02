# 模块参考：控制器

## airship_att_control

源码地址: [modules/airship_att_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/airship_att_control)


### 描述
该模块实现了飞艇的姿态和速率控制。理想情况下它会接收姿态设定值(`vehicle_attitude_setpoint`)或速率设定值(通过`manual_control_setpoint`话题在acro模式中)作为输入，并输出执行器控制消息。

当前该模块直接将`manual_control_setpoint`话题传递给执行器。


### 实现
为减少控制延迟，模块直接轮询IMU驱动发布的陀螺仪话题。


### 使用方法 {#airship_att_control_usage}

```
airship_att_control <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## control_allocator

源码位置: [modules/control_allocator](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/control_allocator)


### 描述
该模块实现了控制分配功能。它以扭矩和推力设定值为输入，输出执行器设定值消息。

### 用法 {#control_allocator_usage}

```
control_allocator <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## flight_mode_manager

源代码: [modules/flight_mode_manager](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/flight_mode_manager)


### 描述
该模块实现了所有模式的设定点生成。它以机体当前模式状态作为输入，并输出控制器的设定点。


### 用法 {#flight_mode_manager_usage}

```
flight_mode_manager <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## fw_att_control

源码: [modules/fw_att_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/fw_att_control)


### 描述
fw_att_control 是固定翼姿态控制器。


### 用法 {#fw_att_control_usage}

```
fw_att_control <command> [arguments...]
 命令:
   start
     [vtol]      垂直起降模式

   stop

   status        打印状态信息
```

## fw_lat_lon_control

Source: [modules/fw_lateral_longitudinal_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/fw_lateral_longitudinal_control)


### Description
fw_lat_lon_control 从横向和纵向控制设定点计算姿态和油门设定点。


### Usage {#fw_lat_lon_control_usage}

```
fw_lat_lon_control <command> [arguments...]
 Commands:
   start
     [vtol]      VTOL mode

   stop

   status        print status info
```

## fw_mode_manager

源码地址: [modules/fw_mode_manager](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/fw_mode_manager)


### 描述
该模块实现了所有PX4内部固定翼模式的设定点生成，包括高度率控制及更上层的逻辑。
它以机体当前模式状态为输入，输出被固定翼横向-纵向控制器及下层控制器（姿态、角速度）使用的设定点。


### 用法 {#fw_mode_manager_usage}

```
fw_mode_manager <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## fw_rate_control

来源: [modules/fw_rate_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/fw_rate_control)


### 描述
fw_rate_control 是固定翼速率控制器。


### 使用方法 {#fw_rate_control_usage}

```
fw_rate_control <command> [arguments...]
 Commands:
   start
     [垂直起降]      垂直起降模式

   stop

   status        打印状态信息
```

## mc_att_control

源代码：[modules/mc_att_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mc_att_control)


### 描述
该模块实现了多旋翼姿态控制器。它以姿态设定值(`vehicle_attitude_setpoint`)作为输入，输出角速度设定值。

该控制器包含用于角度误差的比例环

记录实现四元数姿态控制的论文：
Nonlinear Quadrocopter Attitude Control (2013)
作者：Dario Brescianini, Markus Hehn 和 Raffaello D'Andrea
机构：Institute for Dynamic Systems and Control (IDSC), ETH Zurich

https://www.research-collection.ethz.ch/bitstream/handle/20.500.11850/154099/eth-7387-01.pdf


### 使用方法 {#mc_att_control_usage}

```
mc_att_control <command> [arguments...]
 命令：
   start
     [vtol]      VTOL模式

   stop

   status        打印状态信息
```

## mc_pos_control

源代码: [modules/mc_pos_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mc_pos_control)


### 描述
该控制器包含两个控制回路：用于位置误差的P环和用于速度误差的PID环。
速度控制器的输出是推力矢量，其被分解为推力方向
（即多旋翼姿态的旋转矩阵）和推力标量（即多旋翼的实际推力）。

控制器不使用欧拉角进行计算，仅在需要更人性化的控制和
日志记录时生成。

### 使用说明 {#mc_pos_control_usage}

```
mc_pos_control <command> [arguments...]
 命令:
   start
     [vtol]      VTOL模式

   stop

   status        打印状态信息
```

## mc_rate_control

源码: [modules/mc_rate_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mc_rate_control)


### 描述
该模块实现了多旋翼速率控制器。它接收速率设定值（在acro模式下通过`manual_control_setpoint`主题）作为输入，并输出执行器控制消息。

控制器包含一个用于角速率误差的PID环。


### 使用方法 {#mc_rate_control_usage}

```
mc_rate_control <command> [arguments...]
 命令:
   start
     [vtol]      VTOL模式

   stop

   status        打印状态信息
```

## navigator

源码位置: [modules/navigator](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/navigator)


### 描述
负责自主飞行模式的模块。包括任务（从dataman读取）、起飞和RTL。
还负责地理围栏违规检查。


### 实现
不同的内部模式通过继承自公共基类`NavigatorMode`的独立类实现。
成员`_navigation_mode`包含当前激活的模式。

Navigator发布位置设定点三元组（`position_setpoint_triplet_s`），这些设定点将被位置控制器使用。


### 使用方式 {#navigator_usage}

```
navigator <command> [arguments...]
 Commands:
   start

   fencefile     从SD卡加载地理围栏文件，存储位置为 etc/geofence.txt

   fake_traffic  发布24个伪造的 transponder_report_s uORB 消息

   stop

   status        打印状态信息
```

## rover_ackermann

源码地址：[modules/rover_ackermann](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/rover_ackermann)


### 描述
Rover Ackermann模块。


### 用法 {#rover_ackermann_usage}

```
rover_ackermann <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## rover_differential

源代码: [modules/rover_differential](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/rover_differential)


### 描述
差速车模块。


### 用法 {#rover_differential_usage}

```
rover_differential <command> [arguments...]
 命令：
   start

   stop

   status        打印状态信息
```

## rover_mecanum

源码地址: [modules/rover_mecanum](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/rover_mecanum)


### 描述
Rover mecanum模块。

### 用法 {#rover_mecanum_usage}

```
rover_mecanum <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## rover_pos_control

源码位置: [modules/rover_pos_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/rover_pos_control)


### 描述
使用L1控制器控制地面车的位姿。

以IMU_GYRO_RATEMAX频率发布`vehicle_thrust_setpoint (仅x轴)和vehicle_torque_setpoint (仅偏航)`消息。

### 实现
当前实现仅支持以下几种模式：

 * 完全手动：油门和偏航控制直接传递给执行器
 * 自动任务：地面车执行任务
 * 盘旋：地面车导航到盘旋半径范围内后停止电机

### 示例
CLI使用示例：
```
rover_pos_control start
rover_pos_control status
rover_pos_control stop
```


### 用法 {#rover_pos_control_usage}

```
rover_pos_control <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## spacecraft

源代码: [modules/spacecraft](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/spacecraft)


	### 描述
	该模块为航天器机体实现了控制分配功能。
	它以扭矩和推力设定值作为输入，并输出
	执行器设定值消息。
	
### 用法 {#spacecraft_usage}

```
spacecraft <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## uuv_att_control

Source: [modules/uuv_att_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/uuv_att_control)


### 描述
控制无人水下航行器（UUV）的姿态。

以恒定的250Hz频率发布`机体推力设定值`和`机体扭矩设定值`消息。

### 实现
目前，该实现仅支持以下几种模式：

 * 完全手动模式：滚转、俯仰、偏航和油门控制直接传递到执行器
 * 自动任务模式：UUV执行任务

### 示例
CLI使用示例：
```
uuv_att_control start
uuv_att_control status
uuv_att_control stop
```


### 用法 {#uuv_att_control_usage}

```
uuv_att_control <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## uuv_pos_control

源码位置: [modules/uuv_pos_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/uuv_pos_control)


### 描述
控制无人水下航行器（UUV）的姿态。
发布 `attitude_setpoint` 消息。
### 实现
当前实现仅支持以下模式:
 * 完全手动: 横滚、俯仰、偏航和油门控制直接传递给执行器
 * 自动任务: UUV 执行任务
### 示例
CLI 使用示例:
```
uuv_pos_control start
uuv_pos_control status
uuv_pos_control stop
```

### 使用方法 {#uuv_pos_control_usage}

```
uuv_pos_control <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## vtol_att_control

源码位置: [modules/vtol_att_control](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/vtol_att_control)


### 描述
vtol_att_control 是 VTOL 态控制器。

### 用法 {#vtol_att_control_usage}

```
vtol_att_control <command> [arguments...]
 命令:

   stop

   status        打印状态信息
```