# SensorUwb（UORB消息）

UWB距离包含由超宽带定位系统（如Pozyx或NXP Rddrone）测量的距离信息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorUwb.msg)

```c
# UWB距离包含由超宽带定位系统测量的距离信息
# 例如Pozyx或NXP Rddrone

uint64 	timestamp		# 系统启动后时间（微秒）

uint32 	sessionid		# UWB会话ID
uint32 	time_offset		# 测距轮次之间的时间间隔（毫秒）
uint32 	counter			# 自上次测距开始的测距次数
uint16 	mac			# 发起者（控制器）的MAC地址

uint16 	mac_dest		# 响应者（被控端）的MAC地址
uint16 	status			# 状态反馈 #
uint8 	nlos			# 非视距条件（是/否）
float32 distance		# 到UWB接收器的距离（米）


# 到达角，角度范围-60..+60度；两个轴的视场角均为120度
float32 aoa_azimuth_dev	# 首条接收消息的方位角到达角
float32 aoa_elevation_dev	# 首条接收消息的仰角到达角
float32 aoa_azimuth_resp	# 响应者处首条接收消息的方位角到达角
float32 aoa_elevation_resp	# 响应者处首条接收消息的仰角到达角

# 角度测量的优度量
uint8 aoa_azimuth_fom		# AOA方位角优度量
uint8 aoa_elevation_fom		# AOA仰角优度量
uint8 aoa_dest_azimuth_fom	# AOA方位角优度量
uint8 aoa_dest_elevation_fom	# AOA仰角优度量

# 发起者物理配置
uint8 orientation		# 传感器朝向（来自MAV_SENSOR_ORIENTATION枚举）
				# 标准配置为天线朝下，方位角对齐前向方向
float32 offset_x		# UWB发起者在X轴的偏移量（NED无人机坐标系）
float32 offset_y		# UWB发起者在Y轴的偏移量（NED无人机坐标系）
float32 offset_z		# UWB发起者在Z轴的偏移量（NED无人机坐标系）

```