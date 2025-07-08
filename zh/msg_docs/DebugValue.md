# 调试值 (UORB 消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DebugValue.msg)  

```c
uint64 timestamp	# 系统启动后的时间（微秒）  
int8 ind                # 调试变量索引  
float32 value           # 要发送的调试输出值  
```