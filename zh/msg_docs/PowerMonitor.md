# PowerMonitor (UORB message)

电源监控消息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PowerMonitor.msg)

```c
# power monitor message

uint64 timestamp			# 系统启动后经过的时间（微秒）

float32 voltage_v			# 电压（伏特），未知时为0
float32 current_a		    # 电流（安培），未知时为-1
float32 power_w		        # 功率（瓦特），未知时为-1
int16 rconf
int16 rsv
int16 rbv
int16 rp
int16 rc
int16 rcal
int16 me
int16 al
```