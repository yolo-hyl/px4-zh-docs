# DebugKeyValue (UORB message)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DebugKeyValue.msg)  

```c
uint64 timestamp		# 系统启动后时间（微秒）  
char[10] key			# 最多10个字符作为键/名称  
float32 value			# 作为调试输出发送的值  
```