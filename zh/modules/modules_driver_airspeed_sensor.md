# 模块参考：空速传感器（驱动）

## asp5033

源代码：[drivers/differential_pressure/asp5033](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure/asp5033)

### 描述
驱动外部[ASP5033](https://www.qio-tek.com/index.php/product/qiotek-asp5033-dronecan-airspeed-and-compass-module/)
通过I2C连接的TE设备。
该驱动默认不包含在固件中。可通过终端命令添加："make <your_board> boardconfig"
或在default.px4board中添加行："CONFIG_DRIVERS_DIFFERENTIAL_PRESSURE_ASP5033=y"
可通过将参数"SENS_EN_ASP5033"设置为1来启用。

### 使用 {#asp5033_usage}

```
asp5033 <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：109

   stop

   status        打印状态信息
```

## auav

源代码：[drivers/differential_pressure/auav](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure/auav)

### 使用 {#auav_usage}

```
auav <command> [arguments...]
 Commands:
   start
     [-D]        差压感应
     [-A]        绝对压感应
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：38

   stop

   status        打印状态信息
```

## ets_airspeed

源代码：[drivers/differential_pressure/ets](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure/ets)

### 使用 {#ets_airspeed_usage}

```
ets_airspeed <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：117

   stop

   status        打印状态信息
```

## ms4515

源代码：[drivers/differential_pressure/ms4515](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure/ms4515)

### 使用 {#ms4515_usage}

```
ms4515 <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：70

   stop

   status        打印状态信息
```

## ms4525do

源代码：[drivers/differential_pressure/ms4525do](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure/ms4525do)

### 使用 {#ms4525do_usage}

```
ms4525do <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：40

   stop

   status        打印状态信息
```

## ms5525dso

源代码：[drivers/differential_pressure/ms5525dso](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure/ms5525dso)

### 使用 {#ms5525dso_usage}

```
ms5525dso <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：118

   stop

   status        打印状态信息
```

## sdp3x

源代码：[drivers/differential_pressure/sdp3x](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure/sdp3x)

### 使用 {#sdp3x_usage}

```
sdp3x <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：33
     [-k]        初始化（探测）失败时定期重试

   stop

   status        打印状态信息
```