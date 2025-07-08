# 执行器上锁状态 (UORB 消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActuatorArmed.msg)  

```c
uint64 timestamp	# 系统启动后经过的时间（微秒）

bool armed		# 系统上锁时设为true
bool prearmed		# 如果执行器安全模式被禁用但电机未上锁时设为true
bool ready_to_arm	# 系统准备好上锁时设为true
bool lockdown		# 执行器被强制禁用时设为true（因紧急情况或HIL）
bool manual_lockdown    # 如果手动油门禁用开关被触发时设为true
bool force_failsafe	# 执行器被强制进入失效保护位置时设为true
bool in_esc_calibration_mode # IO/FMU 应该忽略来自执行器控制主题的消息
```