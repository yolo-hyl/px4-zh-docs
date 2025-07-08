# GpioConfig (UORB消息)

GPIO配置

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GpioConfig.msg)

```c
# GPIO配置

uint64 timestamp			# 系统启动后经过的时间（微秒）
uint32 device_id			# 设备ID

uint32 mask				# 引脚掩码
uint32 state				# 初始引脚输出状态

# 配置掩码
# 位0-3: 方向：0=输入，1=输出
# 位4-7: 输入配置：0=浮空，1=上拉，2=下拉
# 位8-12: 输出配置：0=推挽，1=开漏
# 位13-31: 保留
uint32 INPUT			= 0	# 0x0000
uint32 OUTPUT			= 1	# 0x0001
uint32 PULLUP			= 16	# 0x0010
uint32 PULLDOWN			= 32	# 0x0020
uint32 OPENDRAIN		= 256	# 0x0100

uint32 INPUT_FLOATING		= 0	# 0x0000
uint32 INPUT_PULLUP		= 16	# 0x0010
uint32 INPUT_PULLDOWN		= 32	# 0x0020

uint32 OUTPUT_PUSHPULL		= 0	# 0x0000
uint32 OUTPUT_OPENDRAIN		= 256	# 0x0100
uint32 OUTPUT_OPENDRAIN_PULLUP	= 272	# 0x0110

uint32 config
```