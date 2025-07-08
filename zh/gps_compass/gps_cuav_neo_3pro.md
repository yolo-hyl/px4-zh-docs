# CUAV NEO 3 Pro

<Badge type="tip" text="PX4 v1.13" />

NEO 3 Pro是CUAV生产的DroneCan GPS接收机。  
集成了UBLOX M9N、STM32F4 MCU、RM3100罗盘、三色LED灯和安全开关。

![Neo3 Pro GPS的主图](../../assets/hardware/gps/cuav_gps_neo3/neo_3pro.jpg)

## 技术规格

| 硬件                                          | 类型                                                                           |
| :-------------------------------------------- | :----------------------------------------------------------------------------- |
| 处理器                                        | STM32F412                                                                      |
| 罗盘                                          | RM3100                                                                         |
| 气压计                                        | MS5611                                                                         |
| GNSS接收机                                    | UBLOX NEO M9N                                                                  |
| RGB驱动                                       | NCP5623C                                                                       |
| 蜂鸣器                                        | 被动式蜂鸣器                                                                   |
| 安全开关                                      | 物理按钮                                                                       |
| GNSS                                          | 北斗、伽利略、GLONASS、GPS                                                   |
| GNSS增强系统                                  | SBAS:WAAS,EGNOS,MSAS<br>QZSS:L1s(SAIF)<br>其他：RTCM3.3                       |
| 同时GNSS数量                                  | 4                                                                              |
| 频段                                          | GPS:L1C/A<br>GLONASS:L10F<br>北斗:B1I<br>伽利略:E1B/C                       |
| 水平精度                                      | 2.0M                                                                           |
| 速度精度                                      | 0.05M/S                                                                        |
| 导航更新率                                    | 25Hz(最大)                                                                     |
| 捕获时间                                      | 冷启动：24S<br>热启动：2S<br>辅助启动:2s                                     |
| 卫星最大数量                                  | 32+                                                                            |
| 灵敏度                                        | 跟踪和导航-167dBm<br>冷启动热启动-148dBm<br>重获取-160dBm                   |
| 协议                                          | UAVCAN                                                                         |
| 接口类型                                      | GHR-04V-S                                                                      |
| 支持的飞控                                    | CUAV系列<br>Pixhawk系列                                                      |
| 波浪过滤                                      | SAW+LNA+SAW                                                                    |
| 抗电磁/射频干扰                               | EMI+RFI                                                                        |
| 固件升级                                      | 支持                                                                           |
| 输入电压                                      | 5V                                                                             |
| 工作温度                                      | -10~70℃                                                                        |
| 尺寸                                          | 60*60*16MM                                                                     |
| 重量                                          | 33g                                                                            |

## 购买渠道

- [CUAV](https://cuav.en.alibaba.com/product/1600165544920-820872629/Free_shipping_CUAV_Neo_3_pro_drone_UAVCAN_GNSS_processor_STM32F412_autopilot_ublox_M9N_positioning_RM3100_compass_uav_gps_module.html?spm=a2700.shop_oth.74.2.636e28725EvVHb)

## 接线与连接

NEO 3 Pro连接到飞控CAN1/CAN2接口

![NEO 3 Pro连接到飞控CAN1/CAN2接口](../../assets/hardware/gps/cuav_gps_neo3/neo_3pro_connect.png)

## PX4配置

打开**QGroundControl > 参数**并修改以下参数：

- `UAVCAN_ENABLE` 设置为 `Sensors Automatic config`  
- `UAVCAN_SUB_GPS` 设置为 `Enable`

![QGC参数界面显示DroneCan (UAVCAN)参数](../../assets/hardware/gps/cuav_gps_neo3/px4_can.jpg)

## 更多信息

- [CUAV文档](https://doc.cuav.net/gps/neo-series-gnss/en/neo-3-pro.html)