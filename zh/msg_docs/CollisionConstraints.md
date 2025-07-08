# 碰撞约束 (UORB 消息)

NED坐标系下的局部设定点约束
将某个值设为NaN表示没有限制

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/CollisionConstraints.msg)

```c
# NED坐标系下的局部设定点约束
# 将某个值设为NaN表示没有限制

uint64 timestamp	# 系统启动后的时间 (微秒)

float32[2] original_setpoint   # 请求的速率
float32[2] adapted_setpoint    # 允许的速率

```