# 模块参考：磁强计（驱动）

## ak09916

源码位置: [drivers/magnetometer/akm/ak09916](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/akm/ak09916)

### 使用方法 {#ak09916_usage}

```
ak09916 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板载专用总线 (默认=所有) (外部SPI: 第n个总线
                 (默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动 (未发现设备时不提示)
     [-a <val>]  I2C地址
                 默认: 12
     [-R <val>]  旋转角度
                 默认: 0

   stop

   status        打印状态信息
```

## ak8963

源代码: [drivers/magnetometer/akm/ak8963](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/akm/ak8963)

### 用法 {#ak8963_usage}

```
ak8963 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线（默认=all）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示提示）
     [-a <val>]  I2C地址
                 默认值：12
     [-R <val>]  旋转
                 默认值：0

   stop

   status        打印状态信息
```

## bmm150

源代码位置: [drivers/magnetometer/bosch/bmm150](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/bosch/bmm150)

### 使用 {#bmm150_usage}

```
bmm150 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载专用总线(默认=all) (外部SPI: 第n条总线
                 (默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未发现设备时不提示)
     [-a <val>]  I2C地址
                 默认值: 16
     [-R <val>]  旋转角度
                 默认值: 0

   stop

   status        打印状态信息
```

## bmm350

源代码地址：[drivers/magnetometer/bosch/bmm350](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/bosch/bmm350)

### 用法 {#bmm350_usage}

```
bmm350 <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线(可复数)
     [-X]        外部I2C总线(可复数)
     [-b <val>]  板载专用总线（默认=所有）（外部SPI：第n条总线（默认=1））
     [-f <val>]  总线频率（单位kHz）
     [-q]        静默启动（未检测到设备时不显示提示）
     [-a <val>]  I2C地址
                 默认值：20
     [-R <val>]  旋转
                 默认值：0

   stop

   status        显示状态信息
```

## hmc5883

来源: [drivers/magnetometer/hmc5883](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/hmc5883)

### 使用方法 {#hmc5883_usage}

```
hmc5883 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板载总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  芯片选择引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位kHz）
     [-q]        静默启动（未检测到设备时不提示）
     [-R <val>]  旋转
                 默认值: 0
     [-T]        启用温度补偿

   stop

   status        打印状态信息
```

## iis2mdc

源代码位置: [drivers/magnetometer/st/iis2mdc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/st/iis2mdc)

### 用法 {#iis2mdc_usage}

```
iis2mdc <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线（多个）
     [-X]        外部 I2C 总线（多个）
     [-b <val>]  板载专用总线（默认=所有）（外部 SPI: 第 n 条总线（默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示消息）
     [-a <val>]  I2C 地址
                 默认: 48

   stop

   status        打印状态信息
```

## ist8308

源代码: [drivers/magnetometer/isentek/ist8308](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/isentek/ist8308)

### 使用方法 {#ist8308_usage}

```
ist8308 <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（es）
     [-X]        外部I2C总线（es）
     [-b <val>]  板载特定总线（默认为全部）（外部SPI：第n条总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示信息）
     [-a <val>]  I2C地址
                 默认: 12
     [-R <val>]  旋转角度
                 默认: 0

   stop

   status        打印状态信息
```

## ist8310

来源: [drivers/magnetometer/isentek/ist8310](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/isentek/ist8310)

### 使用方法 {#ist8310_usage}

```
ist8310 <command> [arguments...]
 命令:
   start
     [-I]        内部 I2C 总线(多个)
     [-X]        外部 I2C 总线(多个)
     [-b <val>]  板载专用总线(默认=全部) (外部 SPI: 第n个总线 (默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未发现设备时不显示提示)
     [-a <val>]  I2C 地址
                 默认: 14
     [-R <val>]  旋转方向
                 默认: 0

   stop

   status        打印状态信息
```

## lis3mdl

源代码：[drivers/magnetometer/lis3mdl](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/lis3mdl)

### 用法 {#lis3mdl_usage}

```
lis3mdl <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线（默认=全部）（外部SPI：第n个总线
                 （默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不提示）
     [-a <val>]  I2C地址
                 默认：30
     [-R <val>]  旋转
                 默认：0

   reset

   stop

   status        打印状态信息
```

## lsm9ds1_mag

Source: [drivers/magnetometer/lsm9ds1_mag](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/lsm9ds1_mag)

### 使用方法 {#lsm9ds1_mag_usage}

```
lsm9ds1_mag <command> [arguments...]
 Commands:
   start
     [-s]        内部SPI总线(es)
     [-S]        外部SPI总线(es)
     [-b <val>]  板级特有总线(默认=all)(外部SPI: n-th总线(默认=1))
     [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不显示信息)
     [-R <val>]  旋转
                 默认: 0

   stop

   status        打印状态信息
```

## mmc5983ma

源代码: [drivers/magnetometer/memsic/mmc5983ma](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/memsic/mmc5983ma)

### 用法 {#mmc5983ma_usage}

```
mmc5983ma <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-s]        内部SPI总线(es)
     [-S]        外部SPI总线(es)
     [-b <val>]  板级专用总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未找到设备时不显示信息）
     [-a <val>]  I2C地址
                 默认值：48
     [-R <val>]  旋转角度
                 默认值：0

   reset

   stop

   status        打印状态信息
```

## qmc5883l

Source: [drivers/magnetometer/qmc5883l](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/qmc5883l)

### 使用 {#qmc5883l_usage}

```
qmc5883l <command> [arguments...]
 命令：
   start
     [-I]        内部I2C总线(es)
     [-X]        外部I2C总线(es)
     [-b <val>]  板级专用总线（默认=所有）（外部SPI：第n个总线（默认=1））
     [-f <val>]  总线频率，单位kHz
     [-q]        静音启动（如果未找到设备则不显示消息）
     [-a <val>]  I2C地址
                 默认：13
     [-R <val>]  旋转角度
                 默认：0

   stop

   status        打印状态信息
```

## rm3100

源代码: [drivers/magnetometer/rm3100](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/rm3100)

### 用法 {#rm3100_usage}

```
rm3100 <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线（复数）
     [-X]        外部I2C总线（复数）
     [-s]        内部SPI总线（复数）
     [-S]        外部SPI总线（复数）
     [-b <值>]  板级专用总线（默认=全部）（外部SPI：第n个总线
                 （默认=1））
     [-c <值>]  芯片选择引脚（内部SPI）或索引（外部SPI）
     [-m <值>]  SPI模式
     [-f <值>]  总线频率（单位：kHz）
     [-q]        静默启动（未检测到设备时不显示信息）
     [-R <值>]  旋转
                 默认值：0

   stop

   status        打印状态信息
```

## vcm1193l

源码地址: [drivers/magnetometer/vtrantech/vcm1193l](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer/vtrantech/vcm1193l)

### 用法 {#vcm1193l_usage}

```
vcm1193l <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线(可多个)
     [-X]        外部I2C总线(可多个)
     [-b <val>]  板载特定总线(默认=全部) (外部SPI: 第n个总线
                 (默认=1))
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不显示消息)
     [-R <val>]  旋转
                 默认: 0

   stop

   status        显示状态信息
```