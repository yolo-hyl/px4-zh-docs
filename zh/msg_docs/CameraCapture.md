# CameraCapture (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/CameraCapture.msg)

```c
uint64 timestamp		# 系统启动后的时间（微秒）
uint64 timestamp_utc		# UTC/GPS时间
uint32 seq					# 图像序列号
float64 lat					# 纬度（WGS84）
float64 lon					# 经度（WGS84）
float32 alt					# 海拔高度（AMSL）
float32 ground_distance			# 地面高度（米）
float32[4] q					# 使用gimbal时相对于NED固定坐标系的相机姿态，否则为机体姿态
int8 result					# 1表示成功，0表示失败，-1表示相机未提供反馈

```