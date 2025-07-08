# 转速 (UORB消息)


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Rpm.msg)

```c
uint64 timestamp # 系统启动后经过的时间(微秒)

# 0.0 的 rpm 值表示在超时范围内未检测到运动
float32 rpm_estimate # 过滤后的转速(每分钟转数)
float32 rpm_raw

```