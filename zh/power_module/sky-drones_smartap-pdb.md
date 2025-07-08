# Sky-Drones SmartAP PDB

[SmartAP PDB](https://sky-drones.com/power/smartap-pdb.html)（配电板）用于简化从一个或多个电池向电调（电机）、飞控和其他外设的供电分配。  
它同时作为[电源模块](../power_module/index.md)，支持电池电压和电流测量。  
SmartAP PDB使高功率线路的连接更便捷且更可靠。

![SmartAP PDB](../../assets/hardware/power_module/sky-drones_smartap-pdb/smartap-pdb-top-side.jpg)

## 规格参数

- 尺寸：65x65 mm，4个M3安装孔  
- 输入电压最高可达60伏（14S）  
- 支持极高电流（峰值电流最高400A）  
- 电源输入来自主电池，支持连接最多2个独立电池  
- 12对焊盘（顶部6对，底部6对）用于为最多12个电机供电  
- 集成电压和电流传感器，带L/C滤波器  
- 基于霍尔效应的精确电流测量  
- 集成DC-DC转换器，输入10-60 V（最高14S电池）输出5V / 5A，用于为外设供电  
- 集成DC-DC转换器，输入10-60 V（最高14S电池）输出12V / 5A，用于为外设供电  
- 5V和12V电源输出端子（标准2.54mm/0.1"连接器）  
- 集成电磁蜂鸣器（蜂鸣器）  
- 飞控供电输出（5V稳压输出和电池电压输出）  

## 尺寸与重量  

- 长度：65mm  
- 宽度：65mm  
- 高度：14mm  
- 重量：16g  

## PX4配置  

[Battery Estimation Tuning](../config/battery.md) 描述了如何为电源模块配置电池设置。  

关键配置参数：  

- 电压分压器：15.51  
- 安每伏：36.00  

## 购买渠道  

[SmartAP PDB](https://sky-drones.com/parts/smartap-pdb.html)  

## 接线/引脚定义  

SmartAP配电板的引脚定义图如下所示。  

![SmartAP PDB](../../assets/hardware/power_module/sky-drones_smartap-pdb/smartap-pdb-pinout.png)  

两个大焊盘用于主电池连接。  
通过粗线缆（例如8-10 AWG）可连接最多2个独立电池，以应对高电流负载并提高总容量。  
最多可通过顶部、底部、左侧和右侧的6个小焊盘连接12个电调。  

## 电压和电流传感器  

SmartAP PDB集成了电压和电流传感器。  
电流传感器位于配电板底部。  

![SmartAP PDB](../../assets/hardware/power_module/sky-drones_smartap-pdb/smartap-pdb-current-sensor.png)  

## 更多信息  

- [购买SmartAP PDB](https://sky-drones.com/power/smartap-pdb.html)  
- [文档](https://docs.sky-drones.com/avionics/smartap-pdb)