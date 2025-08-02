# 模块参考：IMU（驱动）

## adis16448

源代码: [drivers/imu/analog_devices/adis16448](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/analog_devices/adis16448)

### 用法 {#adis16448_usage}

```
adis16448 <命令> [参数...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级特有总线 (默认=全部) (外部SPI: 第n条总线 (默认=1))
     [-c <val>]  片选引脚 (内部SPI) 或索引 (外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率 (单位kHz)
     [-q]        静默启动 (未发现设备时不显示信息)
     [-R <val>]  旋转角度
                 默认值: 0

   stop

   status        打印状态信息
```

## adis16470

源码地址: [drivers/imu/analog_devices/adis16470](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/analog_devices/adis16470)

### 用法 {#adis16470_usage}

```
adis16470 <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线(多个)
     [-S]        外部SPI总线(多个)
     [-b <val>]  板级特定总线(默认=all)(外部SPI: 第n个总线(默认=1))
     [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不输出信息)
     [-R <val>]  旋转
                 默认值: 0

   stop

   status        输出状态信息
```

## adis16477

源代码: [drivers/imu/analog_devices/adis16477](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/analog_devices/adis16477)

### 用法 {#adis16477_usage}

```
adis16477 <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线（es）
     [-S]        外部SPI总线（es）
     [-b <val>]  板级特定总线（默认：全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        安静启动（未找到设备时不提示）
     [-R <val>]  旋转
                 默认：0

   stop

   status        打印状态信息
```

## adis16497

源代码: [drivers/imu/analog_devices/adis16497](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/analog_devices/adis16497)

### 用法 {#adis16497_usage}

```
adis16497 <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线(多条)
     [-S]        外部SPI总线(多条)
     [-b <val>]  板载特定总线（默认：全部）（外部SPI：第n条总线（默认=1））
     [-c <val>]  片选引脚（内部SPI用）或索引（外部SPI用）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率，单位kHz
     [-q]        静默启动（未找到设备时不显示提示）
     [-R <val>]  旋转角度
                 默认：0

   stop

   status        显示状态信息
```

## adis16507

源代码: [drivers/imu/analog_devices/adis16507](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/analog_devices/adis16507)

### 使用方法 {#adis16507_usage}

```
adis16507 <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线(多个)
     [-S]        外部SPI总线(多个)
     [-b <val>]  板载特定总线(默认=all) (外部SPI: 第n个总线(默认=1))
     [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不显示提示)
     [-R <val>]  旋转角度
                 默认: 0

   stop

   status        打印状态信息
```

## bmi055

源代码： [drivers/imu/bosch/bmi055](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/bosch/bmi055)

### 使用方法 {#bmi055_usage}

```
bmi055 <命令> [参数...]
 命令:
   start
     [-A]        加速度计
     [-G]        陀螺仪
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线（默认=所有）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（无设备时不提示）
     [-R <val>]  旋转方向
                 默认：0

   stop

   status        显示状态信息
```

## bmi085

来源: [drivers/imu/bosch/bmi085](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/bosch/bmi085)

### 用法 {#bmi085_usage}

```
bmi085 <命令> [参数...]
 命令:
   start
     [-A]        加速度计
     [-G]        陀螺仪
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板载特定总线（默认=全部）（外部SPI：第n条总线（默认=1））
     [-c <val>]  片选引脚（用于内部SPI）或索引（用于外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示信息）
     [-R <val>]  旋转
                 默认：0

   stop

   status        打印状态信息
```

## bmi088

源代码位置: [drivers/imu/bosch/bmi088](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/bosch/bmi088)

### 用法 {#bmi088_usage}

```
bmi088 <命令> [参数...]
 命令:
   start
     [-A]        加速度计
     [-G]        陀螺仪
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级特定总线 (默认=全部) (外部SPI: 第n个总线 (默认=1))
     [-c <val>]  芯片选择引脚 (内部SPI) 或索引 (外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率 (kHz)
     [-q]        静默启动 (未发现设备时不提示)
     [-R <val>]  旋转角度
                 默认: 0

   stop

   status        显示状态信息
```

## bmi088_i2c

源代码: [drivers/imu/bosch/bmi088_i2c](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/bosch/bmi088_i2c)

### 使用方式 {#bmi088_i2c_usage}

```
bmi088_i2c <command> [arguments...]
 命令:
   start
     [-A]        加速度计
     [-G]        陀螺仪
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板载特定总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C地址
                 默认：24
     [-R <val>]  旋转方向
                 默认：0

   stop

   status        打印状态信息
```

## bmi270

源代码：[drivers/imu/bosch/bmi270](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/bosch/bmi270)

### 使用方法 {#bmi270_usage}

```
bmi270 <命令> [参数...]
 命令：
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <值>]  板级专用总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <值>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <值>]  SPI模式
     [-f <值>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示提示）
     [-R <值>]  旋转方向
                 默认：0

   stop

   status        显示状态信息
```

## fxas21002c

源代码位置: [drivers/imu/nxp/fxas21002c](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/nxp/fxas21002c)

### 使用方法 {#fxas21002c_usage}

```
fxas21002c <command> [arguments...]
 Commands:
   start
     [-I]        内部I2C总线(可选多条)
     [-X]        外部I2C总线(可选多条)
     [-s]        内部SPI总线(可选多条)
     [-S]        外部SPI总线(可选多条)
     [-b <val>]  板载特定总线 (默认=all) (外部SPI: 第n条总线
                 (默认=1))
     [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不提示)
     [-a <val>]  I2C地址
                 默认: 32
     [-R <val>]  旋转角度
                 默认: 0

   regdump

   testerror

   stop

   status        打印状态信息
```

## fxos8701cq

源码位置：[drivers/imu/nxp/fxos8701cq](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/nxp/fxos8701cq)

### 使用方法 {#fxos8701cq_usage}

```
fxos8701cq <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线(默认=全部)(外部SPI:n-th总线(默认=1))
     [-c <val>]  SPI片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未发现设备时不输出信息)
     [-a <val>]  I2C地址
                 默认值: 30
     [-R <val>]  旋转角度
                 默认值: 0

   regdump

   testerror

   stop

   status        打印状态信息
```

## iam20680hp

源码地址: [drivers/imu/invensense/iam20680hp](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/iam20680hp)

### 使用说明 {#iam20680hp_usage}

```
iam20680hp <command> [arguments...]
 Commands:
   start
     [-s]        内部SPI总线(多个)
     [-S]        外部SPI总线(多个)
     [-b <val>]  板级特定总线(默认=所有) (外部SPI: 第n个总线
                 (默认=1))
     [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未找到设备时不提示)
     [-R <val>]  旋转
                 默认: 0

   stop

   status        打印状态信息
```

## icm20602

源代码：[drivers/imu/invensense/icm20602](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm20602)

### 用法 {#icm20602_usage}

```
icm20602 <command> [arguments...]
 命令：
   start
     [-s]        内部SPI总线（复数）
     [-S]        外部SPI总线（复数）
     [-b <val>]  板级特定总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-c <val>]  芯片选择引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示消息）
     [-R <val>]  旋转
                 默认：0

   stop

   status        显示状态信息
```

## icm20608g

Source: [drivers/imu/invensense/icm20608g](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm20608g)

### 用法 {#icm20608g_usage}

```
icm20608g <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  特定于板的总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位kHz）
     [-q]        静默启动（未找到设备时不显示消息）
     [-R <val>]  旋转
                 默认值：0

   stop

   status        打印状态信息
```

## icm20649

Source: [drivers/imu/invensense/icm20649](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm20649)

### 用法 {#icm20649_usage}

```
icm20649 <命令> [参数...]
 命令:
   start
     [-s]        内部SPI总线(es)
     [-S]        外部SPI总线(es)
     [-b <val>]  板载专用总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示消息）
     [-R <val>]  旋转角度
                 默认值：0

   stop

   status        打印状态信息
```

## icm20689

来源: [drivers/imu/invensense/icm20689](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm20689)

### 使用方式 {#icm20689_usage}

```
icm20689 <命令> [参数...]
 命令:
   start
     [-s]        内部SPI总线（多个）
     [-S]        外部SPI总线（多个）
     [-b <val>]  板级特定总线（默认：全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  芯片选择引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动（未找到设备时不提示）
     [-R <val>]  旋转角度
                 默认：0

   stop

   status        打印状态信息
```

## icm20948

源代码: [drivers/imu/invensense/icm20948](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm20948)

### 使用说明 {#icm20948_usage}

```
icm20948 <命令> [参数...]
 命令:
   start
     [-s]        内部SPI总线(es)
     [-S]        外部SPI总线(es)
     [-b <val>]  板级特定总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  芯片选择引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率，单位kHz
     [-q]        静默启动（如果未找到设备则不显示消息）
     [-M]        启用磁力计（AK8963）
     [-R <val>]  旋转
                 默认值：0

   stop

   status        打印状态信息
```

## icm20948_i2c_passthrough

源代码位置：[drivers/imu/invensense/icm20948](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm20948)

### 用法 {#icm20948_i2c_passthrough_usage}

```
icm20948_i2c_passthrough <命令> [参数...]
 命令:
   start
     [-I]        内部I2C总线
     [-X]        外部I2C总线
     [-b <val>]  板级专用总线（默认=all）（外部SPI：第n条总线
                 （默认=1））
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不提示）
     [-a <val>]  I2C地址
                 默认：105

   stop

   status        打印状态信息
```

## icm40609d

源代码: [drivers/imu/invensense/icm40609d](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm40609d)

### 用法 {#icm40609d_usage}

```
icm40609d <command> [arguments...]
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
     [-R <val>]  Rotation
                 default: 0

   stop

   status        print status info
```

## icm42605

源代码: [drivers/imu/invensense/icm42605](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm42605)

### 使用说明 {#icm42605_usage}

```
icm42605 <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线(es)
     [-S]        外部SPI总线(es)
     [-b <val>]  板载专用总线 (默认=all) (外部SPI: 第n条总线
                 (默认=1))
     [-c <val>]  芯片选择引脚 (用于内部SPI) 或索引 (用于外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率，单位kHz
     [-q]        静默启动 (未发现设备时不输出信息)
     [-R <val>]  旋转
                 默认值: 0

   stop

   status        打印状态信息
```

## icm42670p

源码地址: [drivers/imu/invensense/icm42670p](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm42670p)

### 用法 {#icm42670p_usage}

```
icm42670p <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板载特有总线 (默认=all) (外部SPI: 第n个总线
                 (默认=1))
     [-c <val>]  片选引脚 (内部SPI使用) 或索引 (外部SPI使用)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率 kHz
     [-q]        静默启动 (未发现设备时不提示)
     [-R <val>]  旋转
                 默认: 0

   stop

   status        显示状态信息
```

## icm42688p

源代码: [drivers/imu/invensense/icm42688p](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm42688p)

### 用法 {#icm42688p_usage}

```
icm42688p <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板载特定总线（默认=所有）（外部SPI：第n个总线
                 （默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不提示）
     [-R <val>]  旋转
                 默认: 0
     [-C <val>]  输入时钟频率（Hz）
                 默认: 0
     [-6]        驱动ICM-42686

   stop

   status        打印状态信息
```

## icm45686

源代码: [drivers/imu/invensense/icm45686](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/icm45686)

### 使用 {#icm45686_usage}

```
icm45686 <command> [arguments...]
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
     [-R <val>]  Rotation
                 default: 0
     [-C <val>]  Input clock frequency (Hz)
                 default: 0

   stop

   status        print status info
```

## iim42652

源代码: [drivers/imu/invensense/iim42652](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/iim42652)

### 用法 {#iim42652_usage}

```
iim42652 <command> [arguments...]
 命令：
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级特有总线（默认=全部）（外部SPI：第n条总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未检测到设备时不提示）
     [-R <val>]  旋转角度
                 默认：0
     [-C <val>]  输入时钟频率（Hz）
                 默认：0

   stop

   status        显示状态信息
```

## iim42653

源码: [drivers/imu/invensense/iim42653](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/iim42653)

### 使用方法 {#iim42653_usage}

```
iim42653 <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  特定于板的总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未找到设备时不显示信息）
     [-R <val>]  旋转
                 默认值: 0
     [-C <val>]  输入时钟频率（Hz）
                 默认值: 0

   stop

   status        显示状态信息
```

## l3gd20  

源代码: [drivers/imu/st/l3gd20](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/st/l3gd20)  

### 用法 {#l3gd20_usage}  

```  
l3gd20 <command> [arguments...]  
命令:  
  start  
    [-s]        内部SPI总线(多个)  
    [-S]        外部SPI总线(多个)  
    [-b <val>]  板载特定总线(默认=all) (外部SPI: 第n个总线 (默认=1))  
    [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)  
    [-m <val>]  SPI模式  
    [-f <val>]  总线频率(kHz)  
    [-q]        静默启动(未找到设备时不提示)  
    [-R <val>]  旋转  
                默认值: 0  

  regdump  

  testerror  

  stop  

  status        打印状态信息  
```

## lsm303d

源代码位置：[drivers/imu/st/lsm303d](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/st/lsm303d)

### 使用方法 {#lsm303d_usage}

```
lsm303d <command> [arguments...]
 命令：
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级总线（默认=全部）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-R <val>]  旋转角度
                 默认：0

   stop

   status        显示状态信息
```

## lsm9ds1

源代码地址: [drivers/imu/st/lsm9ds1](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/st/lsm9ds1)

### 使用说明 {#lsm9ds1_usage}

```
lsm9ds1 <command> [arguments...]
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
     [-R <val>]  Rotation
                 default: 0

   stop

   status        print status info
```

## mpu6000

源代码: [drivers/imu/invensense/mpu6000](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/mpu6000)

### 用法 {#mpu6000_usage}

```
mpu6000 <命令> [参数...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板载专用总线（默认=全部）（外部SPI：第n个总线
                 （默认=1））
     [-c <val>]  芯片选择引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位kHz）
     [-q]        静默启动（未检测到设备时不提示）
     [-R <val>]  旋转角度
                 默认值：0

   stop

   status        显示状态信息
```

## mpu9250

源代码: [drivers/imu/invensense/mpu9250](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/mpu9250)

### 用法 {#mpu9250_usage}

```
mpu9250 <command> [arguments...]
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
     [-M]        Enable Magnetometer (AK8963)
     [-R <val>]  Rotation
                 default: 0

   stop

   status        print status info
```

## mpu9250_i2c

源代码: [drivers/imu/invensense/mpu9250](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/mpu9250)

### 使用说明 {#mpu9250_i2c_usage}

```
mpu9250_i2c <command> [arguments...]
 命令:
   start
     [-I]        内部I2C总线（es）
     [-X]        外部I2C总线（es）
     [-b <val>]  板级专用总线（默认=全部）（外部SPI：第n个总线
                 （默认=1））
     [-f <val>]  总线频率，单位kHz
     [-q]        静默启动（未检测到设备时不显示提示）
     [-a <val>]  I2C地址
                 默认：104
     [-R <val>]  旋转角度
                 默认：0

   stop

   status        显示状态信息
```

## mpu9520

源码位置: [drivers/imu/invensense/mpu6500](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/invensense/mpu6500)

### 使用方式 {#mpu9520_usage}

```
mpu9520 <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线（复数）
     [-S]        外部SPI总线（复数）
     [-b <val>]  板级特有总线（默认=all）（外部SPI：第n个总线（默认=1））
     [-c <val>]  片选引脚（内部SPI）或索引（外部SPI）
     [-m <val>]  SPI模式
     [-f <val>]  总线频率（单位：kHz）
     [-q]        安静启动（未找到设备时不显示信息）
     [-R <val>]  旋转
                 默认值: 0

   stop

   status        打印状态信息
```

## sch16t

源码位置: [drivers/imu/murata/sch16t](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/imu/murata/sch16t)

### 使用方法 {#sch16t_usage}

```
sch16t <command> [arguments...]
 命令:
   start
     [-s]        内部SPI总线
     [-S]        外部SPI总线
     [-b <val>]  板级专用总线(默认=全部)(外部SPI: 第n个总线(默认=1))
     [-c <val>]  片选引脚(内部SPI)或索引(外部SPI)
     [-m <val>]  SPI模式
     [-f <val>]  总线频率(kHz)
     [-q]        静默启动(未发现设备时不显示提示)
     [-R <val>]  旋转
                 默认: 0

   stop

   status        打印状态信息
```