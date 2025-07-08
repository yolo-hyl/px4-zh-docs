# 模块参考：应答机（驱动）

## sagetech_mxs

源码路径: [drivers/transponder/sagetech_mxs](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/transponder/sagetech_mxs)


	### 描述

	此驱动集成了Sagetech MXS认证应答机，用于发送和接收ADSB消息及交通信息。

	### 示例

	尝试在指定串口设备上启动驱动
	$ sagetech_mxs start -d /dev/ttyS1
	停止驱动
	$ sagetech_mxs stop
	设置飞行ID（最多8字符）
	$ sagetech_mxs flight_id MXS12345
	设置MXS运行模式
	$ sagetech_mxs opmode off/on/stby/alt
	$ sagetech_mxs opmode 0/1/2/3
	设置应答码
	$ sagetech_mxs squawk 1200
	
### 使用说明 {#sagetech_mxs_usage}

```
sagetech_mxs <command> [arguments...]
 命令：
   start         启动驱动
     -d <val>    串口设备

   stop          停止驱动

   flightid      设置飞行ID（最多8字符）

   ident         在ADSB-Out消息中设置IDENT位

   opmode        设置MXS运行模式。('off', 'on', 'stby', 'alt'，或
                 数值模式 [0-3])

   squawk        设置应答码。[0-7777] 八进制（数字不超过7）
```