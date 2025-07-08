# RcParameterMap (UORB message)  



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RcParameterMap.msg)

```c
uint64 timestamp		# 系统启动后经过的时间(微秒)
uint8 RC_PARAM_MAP_NCHAN = 3 # 此限制在rc_channels.h文件中的RC_CHANNELS_FUNCTION枚举中也进行了硬编码
uint8 PARAM_ID_LEN = 16 # 对应MAVLINK_MSG_PARAM_VALUE_FIELD_PARAM_ID_LEN字段长度

bool[3] valid		# 对应已映射到参数的RC-Param通道为true
int32[3] param_index	# 对应的参数索引，设置为-1时此字段被忽略，此时使用param_id
char[51] param_id	# MAP_NCHAN * (ID_LEN + 1) 字符，对应的参数ID，以空字符结尾
float32[3] scale		# 用于将RC输入[-1, 1]映射到参数值的缩放系数
float32[3] value0		# 参数值变化的基准初始值
float32[3] value_min	# 最小参数值
float32[3] value_max	# 最小参数值

```