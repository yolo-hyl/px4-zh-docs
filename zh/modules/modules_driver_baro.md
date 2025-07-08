# 模块参考：气压计（驱动程序）## bmp280

源代码: [drivers/barometer/bmp280](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/bmp280)

### 用法 {#bmp280_usage}

```
bmp280 <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线(默认=全部)(外部SPI: 第n个总线(默认=1))
     [-c <val>]  芯片选择引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率，单位kHz
     [-q]        静默启动(未发现设备时不显示提示)
     [-a <val>]  I2C地址
                 默认值: 118
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线(默认=全部)(外部SPI: 第n个总线(默认=1))
     [-c <val>]  芯片选择引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率，单位kHz
     [-q]        静默启动(未发现设备时不显示提示)

   stop

   status        打印状态信息
```

## bmp388

源代码: [drivers/barometer/bmp388](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/bmp388)

### 用法 {#bmp388_usage}

```
bmp388 <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线(多个)
     [-X]        外部 I2C 总线(多个)
     [-s]        内部 SPI 总线(多个)
     [-S]        外部 SPI 总线(多个)
     [-b <val>]  板级专用总线(默认=全部)(外部 SPI: 第 n 个总线(默认=1))
     [-c <val>]  片选引脚(内部 SPI)或索引(外部 SPI)
     [-m <val>]  SPI 模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(如果没有找到设备则不显示消息)
     [-a <val>]  I2C 地址
                 默认: 118

   stop

   status        打印状态信息
```

## bmp581

源码地址: [drivers/barometer/bmp581](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/bmp581)

### 使用方法 {#bmp581_usage}

```
bmp581 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线（默认=所有）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        安静启动（未找到设备时不显示消息）
     [-a <val>]  I2C地址
                 默认: 70

   stop

   status        打印状态信息
```

## dps310

源代码：[drivers/barometer/dps310](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/dps310)

### 使用方式 {#dps310_usage}

```
dps310 <command> [arguments...]
 命令：
   start
     [-I]        内部 I2C 总线(多个)
     [-X]        外部 I2C 总线(多个)
     [-s]        内部 SPI 总线(多个)
     [-S]        外部 SPI 总线(多个)
     [-b <val>]  板载特定总线(默认=所有) (外部 SPI：第n个总线(默认=1))
     [-c <val>]  片选引脚(内部 SPI)或索引(外部 SPI)
     [-m <val>]  SPI 模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未发现设备时不提示)
     [-a <val>]  I2C 地址
                 默认：119
     [-s]        内部 SPI 总线(多个)
     [-S]        外部 SPI 总线(多个)
     [-b <val>]  板载特定总线(默认=所有) (外部 SPI：第n个总线(默认=1))
     [-c <val>]  片选引脚(内部 SPI)或索引(外部 SPI)
     [-m <val>]  SPI 模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未发现设备时不提示)

   stop

   status        打印状态信息
```

## icp101xx

来源: [drivers/barometer/invensense/icp101xx](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/invensense/icp101xx)

### 用法 {#icp101xx_usage}

```
icp101xx <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线
     [-X]        外部 I2C 总线
     [-b <val>]  板载专用总线 (默认=全部) (外部 SPI: 第 n 条总线
                 (默认=1))
     [-f <val>]  总线频率 (单位: kHz)
     [-q]        静默启动 (未发现设备时不提示)
     [-a <val>]  I2C 地址
                 默认值: 99

   stop

   status        打印状态信息
```

## icp201xx

Source: [drivers/barometer/invensense/icp201xx](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/invensense/icp201xx)

### 用法 {#icp201xx_usage}

```
icp201xx <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线（默认=all）
     [-X]        外部I2C总线（默认=all）
     [-b <val>]  板载专用总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未检测到设备时不显示消息）
     [-a <val>]  I2C地址（默认：99）

   stop

   status        显示状态信息
```

## lps22hb

源码位置: [drivers/barometer/lps22hb](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/lps22hb)

### 用法 {#lps22hb_usage}

```
lps22hb <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线(可为多个)
     [-X]        外部I2C总线(可为多个)
     [-s]        内部SPI总线(可为多个)
     [-S]        外部SPI总线(可为多个)
     [-b <val>]  板载特定总线(默认=all)(外部SPI: 第n个总线(默认=1))
     [-c <val>]  片选引脚(用于内部SPI)或索引(用于外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(单位: kHz)
     [-q]        静默启动(若未找到设备则不显示消息)

   stop

   status        打印状态信息
```

## lps25h

源代码: [drivers/barometer/lps25h](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/lps25h)

### 用法 {#lps25h_usage}

```
lps25h <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线(默认=all) (外部SPI: 第n条总线(默认=1))
     [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未检测到设备时不显示消息)

   stop

   status        打印状态信息
```

## lps33hw

源代码: [drivers/barometer/lps33hw](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/lps33hw)

### 用法 {#lps33hw_usage}

```
lps33hw <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(可多条)
     [-X]        外部I2C总线(可多条)
     [-s]        内部SPI总线(可多条)
     [-S]        外部SPI总线(可多条)
     [-b <val>]  板级专用总线(默认=全部) (外部SPI: 第n条总线
                 (默认=1))
     [-c <val>]  芯片选择引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不提示)
     [-a <val>]  I2C地址
                 默认: 93
     [-k]        如果初始化(检测)失败，持续周期性重试

   stop

   status        打印状态信息
```

## mpc2520

源码: [drivers/barometer/maiertek/mpc2520](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/maiertek/mpc2520)

### 使用方法 {#mpc2520_usage}

```
mpc2520 <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板级专用总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未发现设备时不输出信息）
     [-a <val>]  I2C地址
                 默认：118

   stop

   status        打印状态信息
```

## mpl3115a2

源代码: [drivers/barometer/mpl3115a2](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/mpl3115a2)

### 使用方法 {#mpl3115a2_usage}

```
mpl3115a2 <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板级专用总线 (默认=all) (外部SPI: 第n个总线
                 (默认=1))
     [-f <val>]  总线频率 (kHz)
     [-q]        静默启动 (未发现设备时不提示)
     [-a <val>]  I2C地址
                 默认值: 96

   stop

   status        打印状态信息
```

## ms5611

源码: [drivers/barometer/ms5611](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/ms5611)

### 用法 {#ms5611_usage}

```
ms5611 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（es）
     [-X]        外部I2C总线（es）
     [-s]        内部SPI总线（es）
     [-S]        外部SPI总线（es）
     [-b <val>]  板载专用总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不提示）
     [-T <val>]  设备类型
                 可选值: 5607|5611， 默认: 5611

   stop

   status        打印状态信息
```

## ms5837

源码位置：[drivers/barometer/ms5837](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/ms5837)

### 使用方法 {#ms5837_usage}

```
ms5837 <command> [arguments...]
 命令：
   start
     [-I]        内部 I2C 总线
     [-X]        外部 I2C 总线
     [-b <val>]  板级专用总线（默认=全部）（外部 SPI：第 n 条总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不显示提示）

   stop

   status        打印状态信息
```

## spa06

源代码: [drivers/barometer/goertek/spa06](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/goertek/spa06)

### 用法 {#spa06_usage}

```
spa06 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认值: 118
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位kHz）
     [-q]        静默启动（未发现设备时不提示）

   stop

   status        打印状态信息
```

## spl06

源代码: [drivers/barometer/goertek/spl06](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/goertek/spl06)

### 用法 {#spl06_usage}

```
spl06 <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线（复数）
     [-X]        外部 I2C 总线（复数）
     [-s]        内部 SPI 总线（复数）
     [-S]        外部 SPI 总线（复数）
     [-b <val>]  板载特定总线（默认=all）（外部 SPI：第n条总线（默认=1））
     [-c <val>]  片选引脚（内部 SPI）或索引（外部 SPI）
     [-m <val>]  SPI 模式
     [-f <val>]  总线频率 kHz
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C 地址
                 默认: 118
     [-s]        内部 SPI 总线（复数）
     [-S]        外部 SPI 总线（复数）
     [-b <val>]  板载特定总线（默认=all）（外部 SPI：第n条总线（默认=1））
     [-c <val>]  片选引脚（内部 SPI）或索引（外部 SPI）
     [-m <val>]  SPI 模式
     [-f <val>]  总线频率 kHz
     [-q]        静默启动（未发现设备时不提示）

   stop

   status        显示状态信息
```