# 位置设定点三元组 (UORB 消息)

在 WGS84 坐标系下的全局位置设定点三元组。这包含接下来的三个航点（或仅接下来的两个或一个）。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PositionSetpointTriplet.msg)

```c
# Global position setpoint triplet in WGS84 coordinates.
# This are the three next waypoints (or just the next two or one).

uint64 timestamp		# time since system start (microseconds)

PositionSetpoint previous
PositionSetpoint current
PositionSetpoint next
```