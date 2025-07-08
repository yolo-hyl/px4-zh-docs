# CUAV NEO 3X GPS

<Badge type="tip" text="PX4 v1.13" />

CUAV Neo 3X 是一款具有防水防尘设计的GNSS接收机。  
该产品具备IP66防护等级，集成了UBLOX M9N模块、RM3100磁力计、彩色LED灯和安全开关。

主要特性：

- 专业防水防尘设计  
- 支持DroneCAN协议  
- 四模卫星接收系统（Ublox M9N）  

![Neo3x GPS展示图](../../assets/hardware/gps/cuav_gps_neo3/neo_3x.jpg)

## 技术规格

| 硬件                  | 类型                                                                 |  
| :-------------------- | :------------------------------------------------------------------- |  
| MUC                   | STM32F412                                                            |  
| 协议                  | DroneCAN                                                             |  
| 磁力计                | RM3100                                                               |  
| 气压计                | ICP-20100                                                            |  
| GNSS接收机            | Ublox M9N                                                            |  
| 频段                  | GPS: L1C/A<br>GLONASS:L10F <br>Beidou:B1I<br>Galileo:E1B/C           |  
| 同时支持GNSS数量      | 4                                                                    |  
| 水平精度              | 1.5m                                                                 |  
| 最大卫星数量          | 32+                                                                  |  
| 接收时间              | 冷启动：24S<br>热启动：2S<br>辅助启动:2s                             |  
| 导航更新率            | 5Hz（默认），25Hz（最大）                                            |  
| 灵敏度                | 跟踪和导航:-167dBm<br>冷启动热启动:-148dBm<br>重获取: -160dBm        |  
| 防护等级              | IP66                                                                 |  
| 输入电压              | 4.7~5.2V                                                             |  
| 工作温度              | -10~70℃                                                              |  
| 尺寸                  | 67*67*21.2mm                                                         |  
| 重量                  | 46g（不含线缆）                                                      |  

## 购买渠道

- [CUAV](https://www.alibaba.com/product-detail/Free-shipping-CUAV-NEO-3X-GPS_1601004167114.html?spm=a2747.manage.0.0.6aa271d2urCPnP)  

## 接线与连接

NEO 3X 连接到飞控的CAN1/CAN2接口  

![NEO 3X 连接至飞控CAN1/CAN2接口](../../assets/hardware/gps/cuav_gps_neo3/neo_3x_connect.jpg)  

## PX4 配置

打开 **QGroundControl > 参数** 并修改以下参数：

- `UAVCAN_ENABLE` 设置为 `Sensors Automatic config`  
- `UAVCAN_SUB_GPS` 设置为 `Enable`  

![QGC 参数界面显示DroneCan（UAVCAN）相关参数](../../assets/hardware/gps/cuav_gps_neo3/px4_can.jpg)  

## 更多信息

- [CUAV 官方文档](https://doc.cuav.net/gps/neo-series-gnss/en/neo-3x.html)