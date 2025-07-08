# 自动调参姿态控制状态 (UORB消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/AutotuneAttitudeControlStatus.msg)  

```c
uint64 timestamp                # 系统启动后的时间（微秒）  

float32[5] coeff                # 已识别离散时间模型的系数  
float32[5] coeff_var            # 已识别离散时间模型的系数方差  
float32 fitness                 # 参数估计的适应性  
float32 innov  
float32 dt_model  

float32 kc  
float32 ki  
float32 kd  
float32 kff  
float32 att_p  

float32[3] rate_sp  

float32 u_filt  
float32 y_filt  

uint8 状态_IDLE = 0  
uint8 状态_INIT = 1  
uint8 状态_ROLL = 2  
uint8 状态_ROLL_PAUSE = 3  
uint8 状态_PITCH = 4  
uint8 状态_PITCH_PAUSE = 5  
uint8 状态_YAW = 6  
uint8 状态_YAW_PAUSE = 7  
uint8 状态_VERIFICATION = 8  
uint8 状态_APPLY = 9  
uint8 状态_TEST = 10  
uint8 状态_COMPLETE = 11  
uint8 状态_FAIL = 12  
uint8 状态_WAIT_FOR_DISARM = 13  

uint8 状态  
```