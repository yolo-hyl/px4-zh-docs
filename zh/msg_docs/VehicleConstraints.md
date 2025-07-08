# 机体约束（UORB消息）

北东地坐标系下的本地设定点约束  
将某值设为NaN表示不设置限制  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleConstraints.msg)

```c
# 北东地坐标系下的本地设定点约束  
# 将某值设为NaN表示不设置限制  

uint64 timestamp # 系统启动后经过的时间（微秒）  

float32 speed_up # 单位为米/秒  
float32 speed_down # 单位为米/秒  

bool want_takeoff # 告知控制器在空闲时发起起飞（飞行过程中忽略）  
```