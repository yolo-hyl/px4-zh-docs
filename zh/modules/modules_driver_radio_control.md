# 模块参考：无线电控制（驱动）

## crsf_rc

源代码：[drivers/rc/crsf_rc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/rc/crsf_rc)


### 描述
此模块解析CRSF RC上行协议并生成CRSF下行遥测数据


### 用法 {#crsf_rc_usage}

```
crsf_rc <command> [arguments...]
 命令:
   start
     [-d <val>]  遥控设备
                 值: <file:dev>, 默认: /dev/ttyS3

   stop

   status        打印状态信息
```

## dsm_rc

源代码：[drivers/rc/dsm_rc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/rc/dsm_rc)


### 描述
此模块执行Spektrum DSM遥控输入解析。


### 用法 {#dsm_rc_usage}

```
dsm_rc <command> [arguments...]
 命令:
   start
     [-d <val>]  遥控设备
                 值: <file:dev>, 默认: /dev/ttyS3

   bind          发送DSM绑定命令（模块必须运行）

   stop

   status        打印状态信息
```

## ghst_rc

源代码：[drivers/rc/ghst_rc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/rc/ghst_rc)


### 描述
此模块执行Ghost (GHST)遥控输入解析。


### 用法 {#ghst_rc_usage}

```
ghst_rc <command> [arguments...]
 命令:
   start
     [-d <val>]  遥控设备
                 值: <file:dev>, 默认: /dev/ttyS3

   stop

   status        打印状态信息
```

## rc_input

源代码：[drivers/rc_input](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/rc_input)


### 描述
此模块执行遥控输入解析并自动选择方法。支持的方法包括：
- PPM
- SBUS
- DSM
- SUMD
- ST24
- TBS Crossfire (CRSF)


### 用法 {#rc_input_usage}

```
rc_input <command> [arguments...]
 命令:
   start
     [-d <val>]  遥控设备
                 值: <file:dev>, 默认: /dev/ttyS3

   bind          发送DSM绑定命令（模块必须运行）

   stop

   status        打印状态信息
```

## sbus_rc

源代码：[drivers/rc/sbus_rc](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/rc/sbus_rc)


### 描述
此模块执行SBUS遥控输入解析。


### 用法 {#sbus_rc_usage}

```
sbus_rc <command> [arguments...]
 命令:
   start
     [-d <val>]  遥控设备
                 值: <file:dev>, 默认: /dev/ttyS3

   stop

   status        打印状态信息
```