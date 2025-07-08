# 模块参考：驱动

子分类：

- [空速传感器](modules_driver_airspeed_sensor.md)
- [气压计](modules_driver_baro.md)
- [相机](modules_driver_camera.md)
- [距离传感器](modules_driver_distance_sensor.md)
- [IMU](modules_driver_imu.md)
- [INS](modules_driver_ins.md)
- [磁力计](modules_driver_magnetometer.md)
- [光流](modules_driver_optical_flow.md)
- [遥控](modules_driver_radio_control.md)
- [转速传感器](modules_driver_rpm_sensor.md)
- [应答机](modules_driver_transponder.md)

## MCP23009

源代码: [drivers/gpio/mcp23009](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/gpio/mcp23009)

### 用法 {#MCP23009_usage}

```
MCP23009 <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线（复数）
     [-X]        外部I2C总线（复数）
     [-b <val>]  板级专用总线（默认=所有）（外部SPI：第n条总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示信息）
     [-a <val>]  I2C地址
                 默认值: 37
     [-D <val>]  方向
                 默认值: 0
     [-O <val>]  输出
                 默认值: 0
     [-P <val>]  上拉电阻
                 默认值: 0
     [-U <val>]  更新间隔 [ms]
                 默认值: 0

   stop

   status        打印状态信息
```

## adc

源代码：[drivers/adc/board_adc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/adc/board_adc)


### 描述
ADC驱动。


### 用法 {#adc_usage}

```
adc <command> [arguments...]
 命令:
   start

   test
     [-n]        不发布ADC报告，仅显示系统电源信息

   stop

   status        打印状态信息
```

## ads1115

源代码：[drivers/adc/ads1115](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/adc/ads1115)


### 描述

驱动程序用于启用通过 I2C 连接的外部 [ADS1115](https://www.adafruit.com/product/1085) ADC。

该驱动程序默认包含在没有内部模数转换器的开发板固件中，例如 [PilotPi](../flight_controller/raspberry_pi_pilotpi.md) 或 [CUAV Nora](../flight_controller/cuav_nora.md)
（在开发板配置文件中搜索 `CONFIG_DRIVERS_ADC_ADS1115`）。

通过
[ADC_ADS1115_EN](../advanced_config/parameter_reference.md#ADC_ADS1115_EN)
参数启用/禁用此功能，默认为禁用状态。
如果启用，将不再使用内部 ADC。


### 使用方法 {#ads1115_usage}

```
ads1115 <command> [arguments...]
 Commands:
   start
     [-I]        使用内部 I2C 总线
     [-X]        使用外部 I2C 总线
     [-b <val>]  板载特定总线（默认=全部）（外部 SPI：第 n 个总线（默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未找到设备时不显示消息）
     [-a <val>]  I2C 地址
                 默认值：72

   stop

   status        打印状态信息
```

## atxxxx

源代码: [drivers/osd/atxxxx](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/osd/atxxxx)


### 描述
ATXXXX芯片的OSD驱动，例如安装在OmnibusF4SD板上的芯片。

可以通过OSD_ATXXXX_CFG参数启用。

### 使用方法 {#atxxxx_usage}

```
atxxxx <command> [arguments...]
 Commands:
   start
     [-s]        Internal SPI bus(es)
     [-S]        External SPI bus(es)
     [-b <val>]  board-specific bus (default=all) (external SPI: n-th bus
                 (default=1))
     [-c <val>]  chip-select pin (for internal SPI) or index (for external SPI)
     [-m <val>]  SPI mode
     [-f <val>]  bus frequency in kHz
     [-q]        quiet startup (no message if no device found)

   stop

   status        print status info
```

## batmon

源代码：[drivers/smart_battery/batmon](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/smart_battery/batmon)


### 描述
支持与BatMon启用的智能电池进行SMBUS通信的驱动程序  
设置/使用说明：https://rotoye.com/batmon-tutorial/
### 示例
在总线4上以地址0x0B启动
```
batmon start -X -a 11 -b 4
```


### 用法 {#batmon_usage}

```
batmon <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板级特定总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未发现设备时不显示消息）
     [-a <val>]  I2C地址
                 默认：11

   man_info      打印制造商信息。

   suspend       挂起驱动程序，停止周期性调度。

   resume        从挂起状态恢复驱动程序。

   stop

   status        打印状态信息
```

## batt_smbus

源代码: [drivers/batt_smbus](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/batt_smbus)


### 描述
用于BQ40Z50电量计IC的智能电池驱动程序。


### 示例
通过写入闪存设置参数。地址，字节数，字节0，...，字节N
```
batt_smbus -X write_flash 19069 2 27 0
```


### 用法 {#batt_smbus_usage}

```
batt_smbus <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（多条）
     [-X]        外部I2C总线（多条）
     [-b <val>]  板级专用总线（默认=所有）（外部SPI：第n条总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：11

   man_info      打印制造商信息。

   unseal        解除设备闪存写保护以启用write_flash
                 命令。

   seal          封锁设备闪存写保护以禁用write_flash命令。

   suspend       暂停驱动程序的周期性调度。

   resume        从暂停状态恢复驱动程序。

   write_flash   写入闪存。设备必须先通过unseal命令解除写保护。
     [address]   开始写入的地址。
     [number of bytes] 要发送的字节数。
     [data[0]...data[n]] 每个数据字节以空格分隔。

   stop

   status        打印状态信息
```

## bst

源代码: [drivers/telemetry/bst](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/telemetry/bst)

### 用法 {#bst_usage}

```
bst <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线(可选)
     [-X]        外部I2C总线(可选)
     [-b <val>]  特定于开发板的总线(默认=all)(外部SPI: 第n个总线
                 (默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        安静启动(未检测到设备时不显示消息)
     [-a <val>]  I2C地址
                 默认: 118

   stop

   status        打印状态信息
```

## dshot

源码：[drivers/dshot](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/dshot)


### 描述
这是DShot输出驱动程序。它与fmu驱动程序类似，可作为即插即用的替代方案，用于将DShot作为ESC通信协议代替PWM。

启动时，该模块会尝试占用所有可用的DShot输出引脚。
它会跳过已使用的引脚（例如被相机触发模块占用的引脚）。

支持功能：
- DShot150，DShot300，DShot600
- 通过独立UART进行遥测并以esc_status消息形式发布
- 通过CLI发送DShot命令

### 示例
永久反转电机1方向：
```
dshot reverse -m 1
dshot save -m 1
```
保存后，反转方向将被视为正常方向。如需再次反转，重复相同命令。

### 用法 {#dshot_usage}

```
dshot <command> [arguments...]
 命令:
   start

   telemetry     在UART上启用遥测
     -d <val>    UART设备
                 值：<device>
     [-x]        交换RX/TX引脚

   reverse       反转电机方向
     [-m <val>]  电机索引（从1开始，缺省为全部）

   normal        正常电机方向
     [-m <val>]  电机索引（从1开始，缺省为全部）

   save          保存当前设置
     [-m <val>]  电机索引（从1开始，缺省为全部）

   3d_on         启用3D模式
     [-m <val>]  电机索引（从1开始，缺省为全部）

   3d_off        禁用3D模式
     [-m <val>]  电机索引（从1开始，缺省为全部）

   beep1         发送蜂鸣器模式1
     [-m <val>]  电机索引（从1开始，缺省为全部）

   beep2         发送蜂鸣器模式2
     [-m <val>]  电机索引（从1开始，缺省为全部）

   beep3         发送蜂鸣器模式3
     [-m <val>]  电机索引（从1开始，缺省为全部）

   beep4         发送蜂鸣器模式4
     [-m <val>]  电机索引（从1开始，缺省为全部）

   beep5         发送蜂鸣器模式5
     [-m <val>]  电机索引（从1开始，缺省为全部）

   esc_info      请求ESC信息
     -m <val>    电机索引（从1开始）

   stop

   status        打印状态信息
```

## fake_gps

源码：[examples/fake_gps](https://github.com/PX4/PX4-Autopilot/tree/main/src/examples/fake_gps)


### 描述


### 用法 {#fake_gps_usage}

```
fake_gps <command> [arguments...]
 命令：
   start

   stop

   status        打印状态信息
```

## fake_imu

源代码: [examples/fake_imu](https://github.com/PX4/PX4-Autopilot/tree/main/src/examples/fake_imu)


### 描述


### 使用方法 {#fake_imu_usage}

```
fake_imu <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## fake_magnetometer

源码：[examples/fake_magnetometer](https://github.com/PX4/PX4-Autopilot/tree/main/src/examples/fake_magnetometer)


### 描述
将地球磁场以虚拟磁强计形式发布(sensor_mag)。需要机体姿态和机体GPS位置。

### 用法 {#fake_magnetometer_usage}

```
fake_magnetometer <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## ft_technologies_serial

源代码位置: [drivers/wind_sensor/ft_technologies](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/wind_sensor/ft_technologies)


### 描述

FT Technologies 数字风速传感器 FT742 的串口总线驱动。该驱动必须与 RS485 到 UART 信号转换模块配合使用。

大多数开发板通过 SENS_FTX_CFG 参数配置为在指定 UART 接口启用/启动该驱动。


### 示例

尝试在指定串口设备上启动驱动。
```
ft_technologies_serial start -d /dev/ttyS1
```
停止驱动
```
ft_technologies_serial stop
```

### 使用方法 {#ft_technologies_serial_usage}

```
ft_technologies_serial <command> [arguments...]
 命令:
   start         启动驱动
     -d <val>    串口设备

   stop          停止驱动
```

## 云台

源码：[modules/gimbal](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/gimbal)


### 描述
云台控制驱动。它将多种输入方式（例如RC或MAVLink）映射到配置的输出（例如AUX通道或MAVLink）。

使用文档请参阅 [gimbal_control](https://docs.px4.io/main/en/advanced/gimbal_control.html) 页面。

### 示例
通过设置角度测试输出（未指定轴默认设为0）：
```
gimbal test pitch -45 yaw 30
```

### 用法 {#gimbal_usage}

```
gimbal <command> [arguments...]
 命令:
   start

   status

   primary-control 设置云台控制权归属
     <sysid> <compid> MAVLink系统ID和MAVLink组件ID

   test          测试输出：设置一个或多个轴的固定角度
                 （云台必须已启动）
     roll|pitch|yaw <angle> 指定轴和角度（单位：度）
     rollrate|pitchrate|yawrate <angle rate> 指定轴和角度速率
                 （单位：度/秒）

   stop

   status        打印状态信息
```

## GPS

源码位置: [drivers/gps](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/gps)

### 描述  
GPS驱动模块处理与设备的通信并通过uORB发布位置信息。  
该模块支持多种协议（设备厂商），默认情况下会自动选择正确的协议。  

模块支持通过`-e`参数指定的备用GPS设备。位置信息将发布在第二个uORB主题实例上，但当前系统其他部分尚未使用该信息（不过数据会被记录，可用于对比分析）。

### 实现  
每个设备都有独立的线程轮询数据。GPS协议类通过回调函数实现，以便在其他项目中复用（例如QGroundControl也使用了这些协议）。  

### 示例  

启动两个GPS设备（主GPS在/dev/ttyS3，备用GPS在/dev/ttyS4）:  
```
gps start -d /dev/ttyS3 -e /dev/ttyS4
```  

执行GPS设备的热重启:  
```
gps reset warm
```  

### 用法 {#gps_usage}  

```
gps <command> [arguments...]
 命令:
   start
     [-d <val>]  GPS设备
                 值: <file:dev>, 默认: /dev/ttyS3
     [-b <val>]  波特率（也可使用p:<param_name>）
                 默认: 0
     [-e <val>]  可选的备用GPS设备
                 值: <file:dev>
     [-g <val>]  波特率（备用GPS，也可使用p:<param_name>）
                 默认: 0
     [-i <val>]  GPS接口
                 值: spi|uart, 默认: uart
     [-j <val>]  备用GPS接口
                 值: spi|uart, 默认: uart
     [-p <val>]  GPS协议（默认=自动选择）
                 值: ubx|mtk|ash|eml|fem|nmea

   stop

   status        打印状态信息

   reset         重置GPS设备
     cold|warm|hot 指定重置类型
```

## gz_bridge

源代码位置: [modules/simulation/gz_bridge](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/gz_bridge)


### 描述


### 用法 {#gz_bridge_usage}

```
gz_bridge <command> [arguments...]
 命令:
   start
     [-w <val>]  世界名称
     -n <val>    模型名称

   stop

   status        打印状态信息
```

## ina220

Source: [drivers/power_monitor/ina220](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/power_monitor/ina220)


### 描述
INA220电源监控器的驱动程序。

如果每个实例具有独立的总线或I2C地址，可以同时运行多个此驱动程序的实例。

例如，一个实例可以在总线2、地址0x41运行，另一个实例可以在总线2、地址0x43运行。

如果INA220模块未通电，则默认情况下驱动程序初始化将失败。要更改此行为，请使用
`-f`标志。如果设置此标志，初始化失败时，驱动程序将每隔0.5秒重新尝试初始化。
设置此标志后，可以在驱动程序启动后插入电池，它将正常工作。未设置此标志时，必须在启动驱动程序前插入电池。


### 用法 {#ina220_usage}

```
ina220 <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板载特定总线（默认=所有）（外部SPI：第n个总线（默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        安静启动（如果未找到设备则不显示信息）
     [-a <val>]  I2C地址
                 默认：65
     [-k]        如果初始化（探测）失败，则周期性重试
     [-t <val>]  校准值的电池索引（1或3）
                 默认：1
     [-T <val>]  类型
                 可选值：VBATT|VREG，默认：VBATT

   stop

   status        打印状态信息
```

## ina226

源码地址: [drivers/power_monitor/ina226](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/power_monitor/ina226)


### 描述
INA226电源监控模块的驱动。

如果每个实例使用独立的总线或I2C地址，可以同时运行多个该驱动的实例。

例如，一个实例可以在总线2、地址0x41运行，另一个实例可以在总线2、地址0x43运行。

如果INA226模块未供电，则默认情况下驱动初始化会失败。要更改此行为，请使用-f标志。如果设置此标志，初始化失败时驱动将每隔0.5秒重新尝试初始化。设置此标志后，即使在驱动启动后插入电池也能正常工作。不设置此标志时，必须在启动驱动前插入电池。


### 使用 {#ina226_usage}

```
ina226 <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线（多个）
     [-X]        外部I2C总线（多个）
     [-b <val>]  板级专用总线（默认=全部）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        安静启动（如果没有找到设备则不显示消息）
     [-a <val>]  I2C地址
                 默认值：65
     [-k]        如果初始化（探测）失败，周期性重试
     [-t <val>]  校准值的电池索引（1或3）
                 默认值：1

   stop

   status        打印状态信息
```

## ina228

源码地址: [drivers/power_monitor/ina228](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/power_monitor/ina228)


### 描述
INA228电源监控器的驱动程序。

如果每个实例具有独立的总线或I2C地址，可以同时运行多个驱动程序实例。

例如，一个实例可以在总线2、地址0x45运行，另一个实例也可以在总线2、地址0x45运行。

如果INA228模块未通电，则默认情况下驱动程序初始化将失败。要更改此行为，请使用
-f 标志。如果设置了此标志，则初始化失败时，驱动程序将每0.5秒尝试重新初始化一次。
设置此标志后，可以在驱动程序启动后插入电池，驱动程序将正常工作。未设置此标志时，必须在启动驱动程序前插入电池。


### 使用方法 {#ina228_usage}

```
ina228 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(多个)
     [-X]        外部I2C总线(多个)
     [-b <val>]  板载特定总线(默认=所有) (外部SPI: 第n个总线
                 (默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未发现设备时不显示消息)
     [-a <val>]  I2C地址
                 默认: 69
     [-k]        如果初始化(探测)失败，则周期性重试
     [-t <val>]  校准值对应的电池索引(1或3)
                 默认: 1

   stop

   status        打印状态信息
```

## ina238

源代码地址: [drivers/power_monitor/ina238](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/power_monitor/ina238)


### 描述
INA238电源监控器的驱动程序。

如果每个实例使用不同的总线或I2C地址，可以同时运行多个驱动实例。
例如，一个实例可以运行在总线2、地址0x45，另一个可以运行在总线2、地址0x45。

如果INA238模块未通电，则默认情况下驱动初始化将失败。要更改此行为，请使用-f标志。如果设置了此标志，初始化失败后，驱动程序将每0.5秒尝试重新初始化一次。启用此标志后，可以在驱动启动后插入电池，它仍能正常工作。未启用此标志时，必须在启动驱动前插入电池。


### 用法 {#ina238_usage}

```
ina238 <command> [arguments...]
 命令:
   start
     [-I]        使用内部I2C总线
     [-X]        使用外部I2C总线
     [-b <val>]  板级特定总线（默认=所有）（外部SPI：第n个总线（默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        安静启动（未找到设备时不显示消息）
     [-a <val>]  I2C地址（默认：69）
     [-k]        如果初始化（探测）失败，定期重试
     [-t <val>]  用于校准值的电池索引（1或3）（默认：1）

   stop

   status        打印状态信息
```

## iridiumsbd

源代码: [drivers/telemetry/iridiumsbd](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/telemetry/iridiumsbd)


### 描述
IridiumSBD 驱动程序。

创建一个虚拟串口供其他模块通信使用（例如 mavlink）。

### 使用方法 {#iridiumsbd_usage}

```
iridiumsbd <command> [arguments...]
 Commands:
   start
     -d <val>    串口设备
                 值: <文件:设备>
     [-v]        启用详细输出

   test
     [s|read|AT <cmd>] 测试命令

   stop

   status        打印状态信息
```

## irlock

源代码: [drivers/irlock](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/irlock)

### 用法 {#irlock_usage}

```
irlock <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（复数）
     [-X]        外部I2C总线（复数）
     [-b <val>]  板载专用总线（默认=所有）（外部SPI: 第n个总线
                 （默认=1））
     [-f <val>]  总线频率（单位kHz）
     [-q]        安静启动（未找到设备时不显示信息）
     [-a <val>]  I2C地址
                 默认: 84

   stop

   status        打印状态信息
```

## linux_pwm_out

源代码: [drivers/linux_pwm_out](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/linux_pwm_out)


### 描述
带有板级特定后端实现的Linux PWM输出驱动。

### 用法 {#linux_pwm_out_usage}

```
linux_pwm_out <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## lsm303agr

Source: [drivers/magnetometer/lsm303agr](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/lsm303agr)

### 使用 {#lsm303agr_usage}

```
lsm303agr <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板载专用总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未发现设备时不显示信息）
     [-R <val>]  旋转方向
                 默认值：0

   stop

   status        显示状态信息
```

## msp_osd

源码位置: [drivers/osd/msp_osd](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/osd/msp_osd)


### 描述
MSP遥测流传输器

### 实现
将uORB消息转换为MSP遥测数据包

### 示例
CLI使用示例：
```
msp_osd
```

### 使用说明 {#msp_osd_usage}

```
msp_osd <命令> [参数...]
 命令:
   stop

   status        打印状态信息
```

## newpixel

源码位置: [drivers/lights/neopixel](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/lights/neopixel)


### 描述
该模块负责驱动与Neopixel串行LED的接口

### 示例
通常通过以下命令启动：
```
neopixel -n 8
```
用于驱动所有可用的LED。

### 使用方法 {#newpixel_usage}

```
newpixel <command> [arguments...]
 命令:
   stop

   status        打印状态信息
```

## paa3905

源代码: [drivers/optical_flow/paa3905](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/optical_flow/paa3905)

### 用法 {#paa3905_usage}

```
paa3905 <命令> [参数...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        安静启动（未找到设备时不显示消息）
     [-Y <val>]  自定义偏航旋转（度）
                 默认：0

   stop

   status        打印状态信息
```

## paw3902

源码位置: [drivers/optical_flow/paw3902](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/optical_flow/paw3902)

### 用法 {#paw3902_usage}

```
paw3902 <命令> [参数...]
 命令:
   start
     [-s]        内部SPI总线（默认：全部）
     [-S]        外部SPI总线（默认：全部）
     [-b <val>]  板级特有总线（默认：全部）（外部SPI：第n个总线
                 （默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示信息）
     [-Y <val>]  自定义偏航旋转（度）
                 默认：0

   stop

   status        打印状态信息
```
## pca9685_pwm_out

Source: [drivers/pca9685_pwm_out](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/pca9685_pwm_out)


### 描述
这是一个PCA9685 PWM输出驱动程序。

它运行在I2C工作队列中，该队列与飞控主循环异步执行，通过调度周期获取最新的混合结果并写入PCA9685。

它能够以占空比模式提供完整的12位输出，同时也能输出被大多数电调和舵机接受的精确脉宽信号。

### 示例
通常通过以下命令启动：
```
pca9685_pwm_out start -a 0x40 -b 1
```


### 用法 {#pca9685_pwm_out_usage}

```
pca9685_pwm_out <command> [arguments...]
 命令:
   start         启动任务
     [-a <val>]  PCA9685的7位I2C地址
                 取值范围: <addr>, 默认值: 0x40
     [-b <val>]  PCA9685连接的总线编号
                 默认值: 1

   stop

   status        打印状态信息
```

## pm_selector_auterion

Source: [drivers/power_monitor/pm_selector_auterion](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/power_monitor/pm_selector_auterion)


### 描述
用于启动和自动检测不同电源监控器的驱动程序。


### 用法 {#pm_selector_auterion_usage}

```
pm_selector_auterion <command> [arguments...]
 命令：
   start

   stop

   status        打印状态信息
```

## pmw3901

源代码：[drivers/optical_flow/pmw3901](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/optical_flow/pmw3901)

### 使用 {#pmw3901_usage}

```
pmw3901 <command> [arguments...]
 命令：
   start
     [-s]        内部SPI总线(可多选)
     [-S]        外部SPI总线(可多选)
     [-b <val>]  特定于板的总线(默认=all)(外部SPI：第n个总线
                 (默认=1))
     [-c <val>]  芯片选择引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不显示消息)
     [-R <val>]  旋转
                 默认值：0

   stop

   status        打印状态信息
```

## pps捕获

源码位置: [drivers/pps_capture](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/pps_capture)


### 描述
该模块从GNSS模块捕获PPS信息，并计算PPS与实时时钟之间的偏移。


### 使用说明 {#pps_capture_usage}

```
pps_capture <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## pwm_out

来源：[drivers/pwm_out](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/pwm_out)


### 描述
此模块负责驱动输出引脚。对于没有独立IO芯片的板载（如Pixracer），它使用主通道。在带有IO芯片的板载（如Pixhawk）上，它使用AUX通道，主通道则通过px4io驱动实现。


### 用法 {#pwm_out_usage}

```
pwm_out <command> [arguments...]
 命令：
   start

   stop

   status        打印状态信息
```

## pwm_out_sim

源码位置: [modules/simulation/pwm_out_sim](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/pwm_out_sim)


### 描述
用于模拟PWM输出的驱动程序。

其唯一功能是接收`actuator_control` uORB消息，将其与任何已加载的混合器混合，并将结果输出到`actuator_output` uORB主题。

它用于SITL和HITL。


### 用法 {#pwm_out_sim_usage}

```
pwm_out_sim <command> [arguments...]
 命令:
   start         启动模块
     [-m <val>]  模式
                 值：hil|sim，默认：sim

   stop

   status        打印状态信息
```

## px4flow

源代码: [drivers/optical_flow/px4flow](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/optical_flow/px4flow)

### 用法 {#px4flow_usage}

```
px4flow <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板载专用总线（默认=全部）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（如果未找到设备则不显示消息）
     [-a <val>]  I2C地址
                 默认：66

   stop

   status        显示状态信息
```

## px4io

源代码: [drivers/px4io](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/px4io)


### 描述
与IO协处理器通信的输出驱动程序。

### 使用方法 {#px4io_usage}

```
px4io <command> [arguments...]
 命令:
   start

   checkcrc      检查固件文件与IO上当前代码的CRC校验
     <filename>  固件文件

   update        更新IO固件
     [<filename>] 固件文件

   debug         设置IO调试级别
     <debug_level> 0=禁用, 9=最大输出量

   bind          DSM绑定
     dsm2|dsmx|dsmx8 协议

   sbus1_out     启用sbus1输出

   sbus2_out     启用sbus2输出

   supported     如果px4io受支持则返回0

   test_fmu_fail 测试: 关闭IO更新

   test_fmu_ok   重新启用IO更新

   stop

   status        打印状态信息
```

## rgbled

源码位置: [drivers/lights/rgbled_ncp5623c](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/lights/rgbled_ncp5623c)

### 用法 {#rgbled_usage}

```
rgbled <command> [arguments...]
 Commands:
   start
     [-I]        Internal I2C bus(es)
     [-X]        External I2C bus(es)
     [-b <val>]  board-specific bus (default=all) (external SPI: n-th bus
                 (default=1))
     [-f <val>]  bus frequency in kHz
     [-q]        quiet startup (no message if no device found)
     [-a <val>]  I2C address
                 default: 57
     [-o <val>]  RGB PWM Assignment
                 default: 123

   stop

   status        print status info
```

## rgbled_is31fl3195

来源: [drivers/lights/rgbled_is31fl3195](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/lights/rgbled_is31fl3195)

### 用法 {#rgbled_is31fl3195_usage}

```
rgbled_is31fl3195 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（es）
     [-X]        外部I2C总线（es）
     [-b <val>]  板载特有总线（默认=所有）（外部SPI: 第n条总线（默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认: 84
     [-o <val>]  RGB PWM分配
                 默认: 123
     [-i <val>]  电流带
                 默认: 0.5

   stop

   status        显示状态信息
```

## rgbled_lp5562

源码地址: [drivers/lights/rgbled_lp5562](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/lights/rgbled_lp5562)


### 描述
通过I2C连接的[LP5562](https://www.ti.com/product/LP5562) LED驱动器驱动程序。

Holybro的某些GPS模块使用该驱动器实现[PX4状态通知](../getting_started/led_meanings.md)

该驱动程序默认包含在固件中（KConfig键 DRIVERS_LIGHTS_RGBLED_LP5562），且始终启用。

### 使用方法 {#rgbled_lp5562_usage}

```
rgbled_lp5562 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（可选多个）
     [-X]        外部I2C总线（可选多个）
     [-b <val>]  板级特定总线（默认=all）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不显示消息）
     [-a <val>]  I2C地址
                 默认值：48
     [-u <val>]  电流（mA）
                 默认值：17.5

   stop

   status        打印状态信息
```

## roboclaw

源码位置: [drivers/roboclaw](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/roboclaw)


### 描述

该驱动通过UART与[Roboclaw电机驱动](https://www.basicmicro.com/motor-controller)进行通信。
其执行两项任务：

 - 基于OutputModuleInterface控制电机
 - 读取轮式编码器并将原始数据发布在`wheel_encoders` uORB主题中

要使用此驱动，需将Roboclaw设置为Packet Serial模式（见链接文档），并按照文档说明将飞控的UART端口连接到Roboclaw。需要通过参数`RBCLW_SER_CFG`启用驱动，正确设置波特率，且地址`RBCLW_ADDRESS`需与电调配置匹配。

启动该驱动的命令为：`$ roboclaw start <UART设备> <波特率>`

### 使用方式 {#roboclaw_usage}

```
roboclaw <命令> [参数...]
 命令:
   start

   stop

   status        打印状态信息
```

## rpm_capture

源码: [drivers/rpm_capture](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/rpm_capture)

### 用法 {#rpm_capture_usage}

```
rpm_capture <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## safety_button

来源：[drivers/safety_button](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/safety_button)


### 描述
此模块负责安全按钮。
快速按下安全按钮三次将触发GCS配对请求。


### 用法 {#safety_button_usage}

```
safety_button <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```

## septentrio

源代码: [drivers/gnss/septentrio](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/gnss/septentrio)


### 描述
Septentrio GNSS 接收机的驱动程序。
它可以自动配置接收机并将其输出提供给系统其他部分。
支持次要接收机以实现冗余、日志记录和双接收机航向。
支持 Septentrio 接收机波特率范围为 57600 至 1500000。
如果使用其他波特率，驱动程序将使用 230400 并发出警告。

### 示例

在端口 `/dev/ttyS0` 使用一个接收机，自动配置为 230400 波特率：
```
septentrio start -d /dev/ttyS0 -b 230400
```

在端口 `/dev/ttyS3` 使用主接收机，在 `/dev/ttyS4` 使用次接收机，
自动检测波特率并保留：
```
septentrio start -d /dev/ttyS3 -e /dev/ttyS4
```

对接收机执行温重置：
```
gps reset warm
```

### 使用 {#septentrio_usage}

```
septentrio <命令> [参数...]
 命令:
   start
     -d <值>    主接收机端口
                 可选值: <文件:dev>
     [-b <值>]  主接收机波特率
                 默认值: 0
     [-e <值>]  次接收机端口
                 可选值: <文件:dev>
     [-g <值>]  次接收机波特率
                 默认值: 0

   stop

   status        打印状态信息

   reset         重置已连接接收机
     cold|warm|hot 指定重置类型
```

## sht3x

源代码：[drivers/hygrometer/sht3x](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/hygrometer/sht3x)


### 描述
SHT3x 温度和湿度传感器驱动，由 Senserion 提供。


### 示例
CLI 使用示例：
```
sht3x start -X
```
  在外部总线启动传感器驱动

```
sht3x status
```
  打印驱动状态

```
sht3x values
```
  打印最近测量值

```
sht3x reset
```
  重新初始化 senzor，重置标志位


### 用法 {#sht3x_usage}

```
sht3x <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（默认全部）
     [-X]        外部I2C总线（默认全部）（外部SPI：第n个总线（默认=1））
     [-b <val>]  板载专用总线（默认全部）
     [-f <val>]  总线频率（单位kHz）
     [-q]        静默启动（未找到设备时不提示）
     [-a <val>]  I2C地址（默认：68）
     [-k]        初始化（探测）失败时定期重试

   stop

   status        打印状态信息

   values        打印实时数据

   reset         重新初始化传感器
```

## tap_esc

来源: [drivers/tap_esc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/tap_esc)


### 描述

该模块通过UART控制TAP_ESC硬件。它监听actuator_controls话题，进行混控并输出PWM信号。

### 实现方式

当前该模块仅以线程版本实现，意味着它在自己的线程中运行，而非工作队列中。

### 示例

模块通常通过以下命令启动：

```
tap_esc start -d /dev/ttyS2 -n <1-8>
```

### 用法 {#tap_esc_usage}

```
tap_esc <command> [arguments...]
 命令:
   start         启动任务
     [-d <val>]  与电调通信的设备
                 值： <device>
     [-n <val>]  电调数量
                 默认值：4
```

## 音调警报

源代码位置：[drivers/tone_alarm](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/tone_alarm)


### 描述
此模块负责音调警报。


### 用法 {#tone_alarm_usage}

```
tone_alarm <command> [arguments...]
 Commands:
   start

   stop

   status        print status info
```

## uwb

源代码：[drivers/uwb/uwb_sr150](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/uwb/uwb_sr150)


### 描述

NXP UWB_SR150超宽带定位系统的驱动程序。当UWB_SR150有位置测量数据可用时，该驱动程序会发布一个`uwb_distance`消息。


### 示例

使用指定设备启动驱动程序：

```
uwb start -d /dev/ttyS2
```
	
### 用法 {#uwb_usage}

```
uwb <命令> [参数...]
 命令:
   start
     -d <val>    与UWB进行串行通信的设备名称
                 可选值: <文件:dev>
     -b <val>    串行通信的波特率
                 可选值: <整数>

   stop

   status
```

## vertiq_io

源码位置: [drivers/actuators/vertiq_io](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/actuators/vertiq_io)

### 使用 {#vertiq_io_usage}

```
vertiq_io <command> [arguments...]
 命令:
   start
     <device>    UART设备

   stop

   status        打印状态信息
```

## voxl2_io

源代码: [drivers/voxl2_io](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/voxl2_io)


### 描述
该模块负责驱动输出引脚。对于没有独立IO芯片的板载设备(例如Pixracer)，它使用主通道。对于带有IO芯片的板载设备(例如Pixhawk)，它使用AUX通道，并通过px4io驱动处理主通道。


### 用法 {#voxl2_io_usage}

```
voxl2_io <command> [arguments...]
 命令:
   start         启动任务
     -v          显示详细信息
     -d          禁用PWM
     -e          禁用RC
     -p <val>    UART端口

   calibrate_escs 校准电机控制器的最小/最大范围

   enable_debug  启用驱动调试

   stop

   status        打印状态信息
```

## voxl_esc

源码位置: [drivers/actuators/voxl_esc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/actuators/voxl_esc)


### 描述
该模块负责...

### 实现
默认情况下模块在工作队列中运行，并通过uORB actuator_controls主题回调。

### 示例
通常通过以下命令启动：
```
todo
```


### 使用 {#voxl_esc_usage}

```
voxl_esc <command> [arguments...]
 命令:
   start         启动任务

   reset         发送重置请求到ESC
     -i <val>    ESC ID，0-3

   version       发送版本请求到ESC
     -i <val>    ESC ID，0-3

   version-ext   发送扩展版本请求到ESC
     -i <val>    ESC ID，0-3

   rpm           闭环RPM测试控制请求
     -i <val>    ESC ID，0-3
     -r <val>    RPM，-32,768 到 32,768
     -n <val>    命令重复次数，0 到 INT_MAX
     -t <val>    重复命令之间的延迟（微秒），0 到 INT_MAX

   pwm           开环PWM测试控制请求
     -i <val>    ESC ID，0-3
     -r <val>    占空比值，0 到 800
     -n <val>    命令重复次数，0 到 INT_MAX
     -t <val>    重复命令之间的延迟（微秒），0 到 INT_MAX

   tone          发送音频生成请求到ESC
     -i <val>    音频周期（频率倒数），0-255
     -d <val>    音频持续时间，0-255，1LSB = 13ms
     -v <val>    音量（功率），0-100

   led           发送LED控制请求
     -l <val>    位掩码 0x0FFF (12 位) - ESC0 (RGB) ESC1 (RGB) ESC2 (RGB)
                 ESC3 (RGB)

   stop

   status        打印状态信息
```

## voxlpm

源地址: [drivers/power_monitor/voxlpm](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/power_monitor/voxlpm)

### 使用方法 {#voxlpm_usage}

```
voxlpm [arguments...]
   start
     [-I]        Internal I2C bus(es)
     [-X]        External I2C bus(es)
     [-b <val>]  board-specific bus (default=all) (external SPI: n-th bus
                 (default=1))
     [-f <val>]  bus frequency in kHz
     [-q]        quiet startup (no message if no device found)
     [-a <val>]  I2C address
                 default: 68
     [-T <val>]  Type
                 values: VBATT|P5VDC|P12VDC, default: VBATT
     [-k]        if initialization (probing) fails, keep retrying periodically

   stop

   status        print status info
```

## zenoh

源代码: [modules/zenoh](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/zenoh)


### 描述

Zenoh 演示桥接器
	
### 使用方法 {#zenoh_usage}

```
zenoh <command> [arguments...]
 命令:
   start

   stop

   status

   config
```