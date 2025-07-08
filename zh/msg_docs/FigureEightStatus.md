# FigureEightStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FigureEightStatus.msg)

```c
uint64 timestamp # 系统启动后时间戳（微秒）
float32 major_radius 	# 8字轨迹主轴半径[m]。正数表示顺时针环绕，负数表示逆时针环绕。
float32 minor_radius 	# 8字轨迹次轴半径[m]。
float32 orientation 	# 8字轨迹主轴方向[rad]。
uint8 frame      # 坐标系类型：x, y, z。
int32 x          # 中心点X坐标。坐标系依赖frame字段：local = x位置（米*1e4），global = 纬度（度*1e7）。
int32 y        	 # 中心点Y坐标。坐标系依赖frame字段：local = y位置（米*1e4），global = 纬度（度*1e7）。
float32 z        # 中心点高度。坐标系依赖frame字段。
```