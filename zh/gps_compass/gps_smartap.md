# Sky-Drones SmartAP GPS

[SmartAP GPS](https://sky-drones.com/navigation/smartap-gnss.html) 是一款集成天线的GNSS导航模块，采用UBlox Neo-M8N芯片组，集成3个3轴磁力计（指南针）、1个MS-5611压力传感器和RGB LED驱动器。  
SmartAP GPS 支持并发接收最多3种GNSS系统（GPS、伽利略、GLONASS、北斗）。

![SmartAP GPS](../../assets/hardware/gps/gps_smartap_gps.jpg)

## 主要特性

- 最多支持3种GNSS系统并发接收（GPS、伽利略、GLONASS、北斗）  
- 3个内置磁力计：HMC5983、IST8310和LIS3MDL  
- 1个内置气压计：MS5611  
- RGB LED驱动器和状态指示灯  

## 购买渠道

- [Sky-Drones官方商城](https://sky-drones.com/navigation/smartap-gnss.html)

## 套件内容

SmartAP GPS套件包含：

- 1个GPS模块  
- 1条30厘米线缆  

## 配置说明

对于机体，您需要将参数 [SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD) 设置为 115200 8N1，以确保PX4使用正确的波特率。

## 引脚定义与连接

SmartAP GPS 采用10针JST-GH接插件，可插接到Pixhawk飞控（符合Pixhawk接插件标准）。

| 引脚编号 | 引脚名称     |
| -------- | ------------ |
| 1        | 5V           |
| 2        | USART1_RX    |
| 3        | USART1_TX    |
| 4        | I2C1_SCL     |
| 5        | I2C1_SDA     |
| 6        | SAFETY_BTN   |
| 7        | SAFETY_LED   |
| 8        | +3V3         |
| 9        | BUZZER       |
| 10       | GND          |

## 规格参数

- U-blox M8N GPS接收器  
- IST8310 磁力计  
- HMC5983 磁力计  
- LIS3MDL 磁力计  
- MS5611 压力传感器  
- 状态指示RGB LED  
  - NCP5623 I2C驱动器  
- 直径：75mm  
- 重量：34g  

## 扩展信息

- [购买SmartAP GPS](https://sky-drones.com/navigation/smartap-gnss.html)  
- [产品文档](https://docs.sky-drones.com/avionics/smartap-gnss)  
- [CAD模型](https://docs.sky-drones.com/avionics/smartap-gnss/cad-model)