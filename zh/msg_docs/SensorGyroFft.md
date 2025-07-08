# SensorGyroFft (UORB message)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorGyroFft.msg)  

```c  
uint64 timestamp          # 系统启动后经过的时间（微秒）  
uint64 timestamp_sample  

uint32 device_id          # 唯一的设备ID，在断电重启后不会改变  

float32 sensor_sample_rate_hz  
float32 resolution_hz  

float32[3] peak_frequencies_x # x轴峰值频率  
float32[3] peak_frequencies_y # y轴峰值频率  
float32[3] peak_frequencies_z # z轴峰值频率  

float32[3] peak_snr_x # x轴峰值信噪比  
float32[3] peak_snr_y # y轴峰值信噪比  
float32[3] peak_snr_z # z轴峰值信噪比  
```