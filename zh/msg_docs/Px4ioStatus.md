# Px4ioStatus（UORB消息）  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Px4ioStatus.msg)  

```c  
uint64 timestamp		# 系统启动后经过的时间（微秒）  

uint16 free_memory_bytes  

float32 voltage_v		# 伺服电压（伏特）  
float32 rssi_v			# RSSI引脚电压（伏特）  

# PX4IO状态标志 (PX4IO_P_STATUS_FLAGS)  
bool status_arm_sync  
bool status_failsafe  
bool status_fmu_initialized  
bool status_fmu_ok  
bool status_init_ok  
bool status_outputs_armed  
bool status_raw_pwm  
bool status_rc_ok  
bool status_rc_dsm  
bool status_rc_ppm  
bool status_rc_sbus  
bool status_rc_st24  
bool status_rc_sumd  
bool status_safety_button_event # px4io安全按钮被按下超过1秒  

# PX4IO告警 (PX4IO_P_STATUS_ALARMS)  
bool alarm_pwm_error  
bool alarm_rc_lost  

# PX4IO上锁 (PX4IO_P_SETUP_ARMING)  
bool arming_failsafe_custom  
bool arming_fmu_armed  
bool arming_fmu_prearmed  
bool arming_force_failsafe  
bool arming_io_arm_ok  
bool arming_lockdown  
bool arming_termination_failsafe  

uint16[8] pwm  
uint16[8] pwm_disarmed  
uint16[8] pwm_failsafe  

uint16[8] pwm_rate_hz  

uint16[18] raw_inputs  
```