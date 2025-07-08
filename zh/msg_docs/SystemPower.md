# 电源系统（UORB 消息）  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SystemPower.msg)  

```c  
uint64 timestamp		# 系统启动后经过的时间（微秒）  
float32 voltage5v_v		# 外设5V电源电压  
float32 voltage_payload_v	# 负载电源电压  
float32[4] sensors3v3		# 传感器3V3电源电压  
uint8 sensors3v3_valid		# 传感器3V3电源电压是否已读取（位域）  
uint8 usb_connected		# 为1时表示USB已连接  
uint8 brick_valid		# 位1为1时表示电池组电源正常  
uint8 usb_valid 		# 为1时表示USB电源有效  
uint8 servo_valid		# 为1时表示舵机电源正常  
uint8 periph_5v_oc		# 为1时表示外设过流  
uint8 hipower_5v_oc		# 为1时表示高功率外设过流  
uint8 comp_5v_valid		# 为1时表示5V配套电源有效  
uint8 can1_gps1_5v_valid	# 为1时表示CAN1/GPS1的5V电源有效  
uint8 payload_v_valid		# 为1时表示负载电源电压有效  

uint8 BRICK1_VALID_SHIFTS=0  
uint8 BRICK1_VALID_MASK=1  
uint8 BRICK2_VALID_SHIFTS=1  
uint8 BRICK2_VALID_MASK=2  
uint8 BRICK3_VALID_SHIFTS=2  
uint8 BRICK3_VALID_MASK=4  
uint8 BRICK4_VALID_SHIFTS=3  
uint8 BRICK4_VALID_MASK=8  
```