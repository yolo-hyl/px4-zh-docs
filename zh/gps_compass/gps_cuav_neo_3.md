# CUAV NEO 3 GPS

<Badge type="tip" text="PX4 v1.13" />

该NEO 3 GPS由CUAV制造。  
它集成了Ublox M9N、IST8310、三色LED灯和安全开关，并兼容CUAV和Pixhawk标准控制器。

![Neo3 GPS的主图](../../assets/hardware/gps/cuav_gps_neo3/neo_3.jpg)

## 技术规格

| 硬件                                          | 类型                                                                           |
| :-------------------------------------------- | :----------------------------------------------------------------------------- |
| 指南针                                        | IST8310                                                                        |
| GNSS接收器                                    | UBLOX M9N                                                                      |
| RGB驱动                                       | NC5623C                                                                        |
| 蜂鸣器                                        | 无源蜂鸣器                                                                     |
| 安全开关                                      | 物理按钮                                                                       |
| GNSS系统                                      | 北斗、伽利略、GLONASS、GPS                                                   |
| GNSS增强系统                                  | SBAS:WAAS、EGNOS、MSAS<br>QZSS:L1s(SAIF)<br>其他：RTCM3.3                    |
| 同时支持的GNSS数量                            | 4                                                                              |
| 频段                                          | GPS:L1C/A<br>GLONASS:L10F<br>北斗:B1I<br>伽利略:E1B/C                         |
| 水平精度                                      | 2.0米                                                                          |
| 速度精度                                      | 0.05米/秒                                                                      |
| 导航更新率                                    | 25Hz(最大)                                                                     |
| 起始定位时间                                  | 冷启动：24秒<br>热启动：2秒<br>辅助启动：2秒                                 |
| 最大卫星数量                                  | 32+                                                                            |
| 灵敏度                                        | 跟踪与导航-167dBm<br>冷启动/热启动-148dBm<br>重获取-160dBm                  |
| 通信协议                                      | UART+IO+I2C                                                                    |
| 接口类型                                      | GHR-10V-S                                                                      |
| 支持的飞控器                                  | CUAV系列<br>Pixahwk系列                                                      |
| 波形滤波                                      | SAW+LNA+SAW                                                                    |
| 抗电磁/射频干扰能力                           | EMI+RFI                                                                        |
| 固件升级支持                                  | 支持                                                                           |
| 输入电压                                      | 5V                                                                             |
| 工作温度                                      | -10~70℃                                                                        |
| 尺寸                                          | 60\*60\*16毫米                                                                 |
| 重量                                          | 33克                                                                           |

## 尺寸

![Neo 3尺寸](../../assets/hardware/gps/cuav_gps_neo3/neo_3_size.png)

## 引脚定义

![Neo 3引脚定义](../../assets/hardware/gps/cuav_gps_neo3/neo_3_pinouts.png)

## 购买渠道

- [CUAV](https://cuav.en.alibaba.com/product/1600217379204-820872629/CUAV_NEO_3_M9N_GPS_Module_for_Pixhawk_Compass_gps_tracker_navigation_gps.html?spm=a2700.shop_oth.74.1.636e28725EvVHb)

## 接线与连接

Neo3接线与连接示意图

![Neo3接线与连接示意图](../../assets/hardware/gps/cuav_gps_neo3/neo_3_connect.png)

## 更多信息

- [CUAV文档](https://doc.cuav.net/gps/neo-series-gnss/zh-hans/neo-3.html)