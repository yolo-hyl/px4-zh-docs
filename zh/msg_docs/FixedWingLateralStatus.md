# 固定翼横向状态 (UORB消息)

固定翼横向状态消息  
由fw_lateral_longitudinal_control模块发布，用于报告最终的横向设定值  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FixedWingLateralStatus.msg)  

```c
# 固定翼横向状态消息  
# 由fw_lateral_longitudinal_control模块发布，用于报告最终的横向设定值  

uint64 timestamp                         # 系统启动后经过的时间（微秒）  

float32 lateral_acceleration_setpoint    # [m/s^2] [FRD] 最终横向加速度设定值  
float32 can_run_factor 	 	         # [norm] [@范围 0, 1] 评估npfg横滚设定值功能正确的置信度  
```