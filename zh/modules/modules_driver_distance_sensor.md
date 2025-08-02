# 模块参考：距离传感器（驱动程序）

## afbrs50

源代码: [drivers/distance_sensor/broadcom/afbrs50](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/broadcom/afbrs50)


### 描述

Broadcom AFBRS50 驱动程序。

### 示例

尝试在指定串口设备上启动驱动。
```
afbrs50 start
```
停止驱动
```
afbrs50 stop
```

### 用法 {#afbrs50_usage}

```
afbrs50 <command> [arguments...]
 命令:
   start         启动驱动
     -d <val>    串口设备
     [-r <val>]  传感器旋转 - 默认为向下安装
                 默认值: 25

   test          测试驱动

   stop          停止驱动
```

## gy_us42

源代码: [drivers/distance_sensor/gy_us42](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/gy_us42)

### 用法 {#gy_us42_usage}

```
gy_us42 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板载专用总线（默认=all）（外部SPI: 第n条总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        安静启动（未检测到设备时不提示）
     [-R <val>]  传感器旋转（默认向下）
                 默认: 25

   stop

   status        打印状态信息
```

## leddar_one

来源: [drivers/distance_sensor/leddar_one](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/leddar_one)


### 描述

LeddarOne LiDAR的串行总线驱动。

大多数板载默认通过UART总线启用/启动驱动，使用参数SENS_LEDDAR1_CFG指定通信端口。

设置/使用说明：https://docs.px4.io/main/en/sensor/leddar_one.html

### 示例

尝试在指定的串行设备上启动驱动程序
```
leddar_one start -d /dev/ttyS1
```
停止驱动
```
leddar_one stop
```

### 用法 {#leddar_one_usage}

```
leddar_one <command> [arguments...]
 命令:
   start         启动驱动
     -d <val>    串行设备
     [-r <val>]  传感器旋转 - 默认为向下对准
                 默认值: 25

   stop          停止驱动
```

## lightware_laser_i2c

源代码地址：[drivers/distance_sensor/lightware_laser_i2c](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/lightware_laser_i2c)


### 描述

Lightware SFxx系列激光雷达测距仪的I2C总线驱动程序：SF10/a, SF10/b, SF10/c, SF11/c, SF/LW20。

设置/使用说明：https://docs.px4.io/main/en/sensor/sfxx_lidar.html

### 用法 {#lightware_laser_i2c_usage}

```
lightware_laser_i2c <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板级特定总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示消息）
     [-a <val>]  I2C地址
                 默认：102
     [-R <val>]  传感器旋转角度 - 默认向下
                 默认：25

   stop

   status        打印状态信息
```

## lightware_laser_serial

源代码：[drivers/distance_sensor/lightware_laser_serial](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/lightware_laser_serial)


### 描述

串口总线驱动，适用于LightWare SF02/F、SF10/a、SF10/b、SF10/c、SF11/c激光测距仪。

大多数开发板通过SENS_SF0X_CFG参数配置在指定的UART上启用/启动驱动。

设置/使用说明：https://docs.px4.io/main/en/sensor/sfxx_lidar.html

### 示例

尝试在指定的串口设备上启动驱动
```
lightware_laser_serial start -d /dev/ttyS1
```
停止驱动
```
lightware_laser_serial stop
```

### 用法 {#lightware_laser_serial_usage}

```
lightware_laser_serial <命令> [参数...]
 命令：
   start         启动驱动
     -d <val>    串口设备
     [-R <val>]  传感器方向（默认向下）
                 默认值：25

   stop          停止驱动
```

## lightware_sf45_serial

源代码: [drivers/distance_sensor/lightware_sf45_serial](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/lightware_sf45_serial)


### 描述

Lightware SF45/b 激光测距仪的串行总线驱动程序。


### 示例

尝试在指定的串行设备上启动驱动程序
```
lightware_sf45_serial start -d /dev/ttyS1
```
停止驱动程序
```
lightware_sf45_serial stop
```

### 使用方式 {#lightware_sf45_serial_usage}

```
lightware_sf45_serial <command> [arguments...]
 命令:
   start         启动驱动程序
     -d <val>    串行设备

   stop          停止驱动程序
```

## ll40ls

源代码: [drivers/distance_sensor/ll40ls_pwm](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/ll40ls_pwm)


### 描述

用于LidarLite测距仪的PWM驱动程序。

传感器/驱动程序必须通过参数 SENS_EN_LL40LS 启用。

设置/使用信息: https://docs.px4.io/main/en/sensor/lidar_lite.html

### 使用 {#ll40ls_usage}

```
ll40ls <command> [arguments...]
 命令:
   start         启动驱动程序
     [-R <val>]  传感器旋转 - 默认为向下安装
                 默认值: 25

   status        打印驱动程序状态信息

   stop          停止驱动程序
```

## mappydot

源代码地址: [drivers/distance_sensor/mappydot](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/mappydot)

### 使用方法 {#mappydot_usage}

```
mappydot <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线
     [-X]        外部 I2C 总线
     [-b <val>]  板载特有总线（默认=所有）（外部 SPI：第 n 条总线
                 （默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（如果未找到设备则不显示消息）

   stop

   status        打印状态信息
```

## mb12xx

源代码: [drivers/distance_sensor/mb12xx](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/mb12xx)

### 用法 {#mb12xx_usage}

```
mb12xx <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线
     [-X]        外部 I2C 总线
     [-b <val>]  板级专用总线(默认=all) (外部 SPI: 第n个总线 (默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不提示)

   set_address
     [-a <val>]  I2C 地址
                 默认值: 112

   stop

   status        打印状态信息
```

## pga460

源代码位置：[drivers/distance_sensor/pga460](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/pga460)


### 描述

用于超声波测距的驱动程序，负责与设备通信并通过 uORB 发布距离数据。

### 实现

该驱动作为 NuttX 任务实现。选择此实现方式是因为需要通过 UART 对消息进行轮询，而 work_queue 不支持此功能。驱动在运行时会持续进行测距操作。在驱动级别实现了一个简单算法来检测错误读数，以提高发布数据的质量。如果驱动认为传感器数据无效或不稳定，将完全不发布数据。

### 用法 {#pga460_usage}

```
pga460 <command> [arguments...]
 命令:
   start
     [device_path] pga460 传感器设备路径（例如：/dev/ttyS6）

   status

   stop

   help
```

## srf02

源代码位置: [drivers/distance_sensor/srf02](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/srf02)

### 使用方法 {#srf02_usage}

```
srf02 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板级专用总线(默认=all)(外部SPI: 第n个总线(默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不显示提示)
     [-a <val>]  I2C地址
                 默认: 112
     [-R <val>]  传感器旋转角度 - 默认向下安装
                 默认: 25

   stop

   status        打印状态信息
```

## srf05

源代码：[drivers/distance_sensor/srf05](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/srf05)


  ### 描述

  用于HY-SRF05 / HC-SR05和HC-SR04测距仪的驱动程序。

  必须使用参数SENS_EN_HXSRX0X启用该传感器/驱动程序。

  
### 用法 {#srf05_usage}

```
srf05 <command> [arguments...]
 命令:
   start         启动驱动程序
     [-R <val>]  传感器旋转 - 默认向下朝向
                 默认值: 25

   status        打印驱动程序状态信息

   stop          停止驱动程序

   stop

   status        打印状态信息
```

## teraranger

源代码：[drivers/distance_sensor/teraranger](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/teraranger)


### 描述

TeraRanger测距仪的I2C总线驱动。

必须通过参数SENS_EN_TRANGER启用传感器/驱动。

设置/使用信息：https://docs.px4.io/main/en/sensor/rangefinders.html#teraranger-rangefinders

### 使用方法 {#teraranger_usage}

```
teraranger <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（es）
     [-X]        外部I2C总线（es）
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未找到设备时无提示）
     [-a <val>]  I2C地址
                 默认：48
     [-R <val>]  传感器旋转 - 默认向下
                 默认：25

   stop

   status        打印状态信息
```

## tf02pro

源码位置: [drivers/distance_sensor/tf02pro](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/tf02pro)

### 用法 {#tf02pro_usage}

```
tf02pro <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线
     [-X]        外部 I2C 总线
     [-b <val>]  板级专用总线 (默认=all) (外部 SPI: 第n个总线 (默认=1))
     [-f <val>]  总线频率，单位 kHz
     [-q]        静默启动 (未发现设备时不显示提示)
     [-a <val>]  I2C 地址
                 默认: 16
     [-R <val>]  传感器旋转 - 默认向下
                 默认: 25

   stop

   status        显示状态信息
```

## tfmini

源码位置：[drivers/distance_sensor/tfmini](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/tfmini)


### 描述

Benewake TFmini 激光雷达的串口总线驱动。

大多数开发板通过SENS_TFMINI_CFG参数配置在指定的UART接口上启用/启动该驱动。

设置/使用说明：https://docs.px4.io/main/en/sensor/tfmini.html

### 示例

尝试在指定串口设备上启动驱动。
```
tfmini start -d /dev/ttyS1
```
停止驱动
```
tfmini stop
```

### 用法 {#tfmini_usage}

```
tfmini <command> [arguments...]
 命令:
   start         启动驱动
     -d <val>    串口设备
     [-R <val>]  传感器旋转 - 默认朝下
                 默认值: 25

   status        驱动状态

   stop          停止驱动

   status        打印驱动状态
```

## ulanding_radar

源代码: [drivers/distance_sensor/ulanding_radar](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/ulanding_radar)


### 描述

Aerotenna uLanding 雷达的串口总线驱动。

设置/使用信息: https://docs.px4.io/main/en/sensor/ulanding_radar.html

### 示例

尝试在指定串口设备启动驱动。
```
ulanding_radar start -d /dev/ttyS1
```
停止驱动
```
ulanding_radar stop
```

### 用法 {#ulanding_radar_usage}

```
ulanding_radar <command> [arguments...]
 Commands:
   start         启动驱动
     -d <val>    串口设备
                 可选值: <file:dev>, 默认值: /dev/ttyS3
     [-R <val>]  传感器旋转 - 默认向下视角
                 默认值: 25

   stop          停止驱动
```

## vl53l0x

Source: [drivers/distance_sensor/vl53l0x](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/vl53l0x)

### 使用 {#vl53l0x_usage}

```
vl53l0x <command> [arguments...]
 Commands:
   start
     [-I]        Internal I2C bus(es)
     [-X]        External I2C bus(es)
     [-b <val>]  board-specific bus (default=all) (external SPI: n-th bus
                 (default=1))
     [-f <val>]  bus frequency in kHz
     [-q]        quiet startup (no message if no device found)
     [-a <val>]  I2C address
                 default: 41
     [-R <val>]  Sensor rotation - downward facing by default
                 default: 25

   stop

   status        print status info
```

## vl53l1x

源代码: [drivers/distance_sensor/vl53l1x](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/vl53l1x)

### 使用 {#vl53l1x_usage}

```
vl53l1x <command> [arguments...]
 Commands:
   start
     [-I]        内部 I2C 总线（多总线）
     [-X]        外部 I2C 总线（多总线）
     [-b <val>]  板级专用总线（默认=所有）（外部 SPI：第 n 条总线（默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未找到设备时不提示）
     [-a <val>]  I2C 地址
                 默认：41
     [-R <val>]  传感器旋转 - 默认向下
                 默认：25

   stop

   status        打印状态信息
```