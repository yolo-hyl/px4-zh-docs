# InternalCombustionEngineControl (UORB message)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/InternalCombustionEngineControl.msg)  

```c
uint64 timestamp        		# 系统启动后经过的时间（微秒）  

bool ignition_on          		# 激活/关闭点火（火花塞）  
float32 throttle_control		# [0,1] - 0表示怠速。若启用则包含斜率限制（slew rate）。  
float32 choke_control			# [0,1] - 1表示完全关闭进气口。  
float32 starter_engine_control		# [0,1] - 电动启动马达的控制值。  

uint8 user_request			# 用户对ICE启停的意图请求  
```