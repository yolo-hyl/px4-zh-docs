# 飞行阶段估计 (UORB消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FlightPhaseEstimation.msg)  

```c  
uint64 timestamp               # 系统启动后经过的时间（微秒）  

uint8 flight_phase               # 当前飞行阶段的估计值  

uint8 FLIGHT_PHASE_UNKNOWN = 0   # 机体飞行阶段未知  
uint8 FLIGHT_PHASE_LEVEL = 1     # 机体处于平飞阶段  
uint8 FLIGHT_PHASE_DESCEND = 2   # 机体处于下降阶段  
uint8 FLIGHT_PHASE_CLIMB = 3     # 机体处于爬升阶段  

```