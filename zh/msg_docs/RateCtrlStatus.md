# RateCtrlStatus（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RateCtrlStatus.msg)

```c
uint64 timestamp		# 系统启动后经过的时间（微秒）

# 速率控制器积分器状态
float32 rollspeed_integ
float32 pitchspeed_integ
float32 yawspeed_integ

```