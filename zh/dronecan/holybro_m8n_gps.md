# Holybro DroneCAN M8N GPS

Holybro DroneCAN GPS 包含 UBLOX M8N 模块、BMM150 电子罗盘和三色 LED 指示灯。

GPS 模块使用 [DroneCAN](index.md) 协议进行通信。  
DroneCAN 连接相比串口连接对电磁干扰更具抗性，因此更可靠。  
此外，使用 DroneCAN 意味着 GPS 和电子罗盘不会占用任何飞控串口（可通过 CAN 分线板将不同的/额外的 CAN 设备连接到同一个 CAN 总线）。

<img src="../../assets/hardware/gps/hb_dronecan_m8n/hb_dronecan_m8n_gps.jpg" width="400px" title="Hero diagram for the GPS module" />## 购买渠道

从以下渠道订购此模块：

- [Holybro](https://holybro.com/products/dronecan-m8n-gps)

## 硬件规格

|                           | DroneCAN M8N                                                                                                                                       |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| GNSS接收器              | Ublox NEO M8N                                                                                                                                      |
| 并行GNSS数量            | 2 (默认GPS + GLONASS)                                                                                                                              |
| 处理器                  | STM32G4 (170MHz, 512K FLASH)                                                                                                                       |
| 磁力计                  | BMM150                                                                                                                                             |
| 频率频段                | <p>GPS: L1C/A<br>GLONASS: L10F<br>北斗: B1I<br>伽利略: E1B/C</p>                                                                                   |
| GNSS增强系统            | SBAS: WAAS, EGNOS, MSAS, QZSS                                                                                                                      |
| 导航更新频率            | 5Hz 默认(10Hz 最大)                                                                                                                                |
| 导航灵敏度              | –167 dBm                                                                                                                                           |
| 冷启动时间              | \~ 26s                                                                                                                                             |
| 定位精度                | 2.5m                                                                                                                                               |
| 速度精度                | 0.05 m/s                                                                                                                                           |
| 最大卫星数量            | 22+                                                                                                                                                |
| 默认CAN总线速率         | 1MHz                                                                                                                                               |
| 通信协议                | DroneCAN @ 1 Mbit/s                                                                                                                                |
| 支持自动驾驶仪固件      | PX4, Ardupilot                                                                                                                                     |
| 接口类型                | GHR-04V-S                                                                                                                                          |
| 天线                    | 25 x 25 x 4 mm 陶瓷贴片天线                                                                                                                        |
| 电压                    | 4.7-5.2V                                                                                                                                           |
| 功耗                    | 5V下小于200mA                                                                                                                                      |
| 温度范围                | -40\~80C                                                                                                                                           |
| 尺寸                    | <p>直径: 54mm<br>厚度: 14.5mm</p>                                                                                                                  |
| 重量                    | 36g                                                                                                                                                |
| 电缆长度                | 26cm                                                                                                                                               |
| 其他特性                | <ul><li>LNA MAX2659ELT+ 射频放大器</li><li>可充电Farah电容</li><li>低噪声3.3V稳压器</li><li>附带26cm电缆</li></ul> |## 硬件设置

### 安装

建议的安装方向是将GPS上的箭头指向机体前方。

传感器可以安装在机架的任何位置，但在[PX4配置](#px4-configuration)过程中需要指定其相对于机体重心的位置。

### 接线

Holybro DroneCAN GPS通过Pixhawk标准4针JST GH线缆连接到CAN总线。  
更多信息请参考[CAN接线](../can/index.md#wiring)说明。

### 引脚分配

![GPS引脚分配示意图](../../assets/hardware/gps/hb_dronecan_m8n/hb_dronecan_m8n_gps_pinout.jpg)

### 尺寸

![GPS尺寸示意图](../../assets/hardware/gps/hb_dronecan_m8n/hb_dronecan_m8n_gps_dimension.jpg)

## PX4配置

您需要设置必要的[DroneCAN](index.md)参数并定义偏移量，如果传感器未在机体中心位置。  
所需设置如下所示。

::: info  
如果飞行控制器上电时没有插入SD卡，GPS将不会启动。  
::: 

### 启用DroneCAN

为了使用ARK GPS板，将其连接到Pixhawk的CAN总线并通过设置参数[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)为`2`启用DroneCAN驱动（或设置为`3`如果使用[DroneCAN ESCs](../dronecan/escs.md)）以进行动态节点分配。

步骤如下：  
- 在_QGroundControl_中将参数[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)设置为`2`或`3`并重启（参见[Finding/Updating Parameters](../advanced_config/parameters.md)）。  
- 将GPS CAN连接到Pixhawk CAN。  

启用后，模块将在启动时被检测到。GPS数据将以5Hz频率接收。

PX4中的DroneCAN配置详情请参阅[DroneCAN > Enabling DroneCAN](../dronecan/index.md#enabling-dronecan)。

### 传感器位置配置

如果传感器未在机体中心位置，还需定义传感器偏移：  
- 通过设置[EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)的位3为true以启用GPS偏航融合。  
- 启用[UAVCAN_SUB_GPS](../advanced_config/parameter_reference.md#UAVCAN_SUB_GPS)、[UAVCAN_SUB_MAG](../advanced_config/parameter_reference.md#UAVCAN_SUB_MAG)和[UAVCAN_SUB_BARO](../advanced_config/parameter_reference.md#UAVCAN_SUB_BARO)。  
- 如果这是CAN总线上的最后一个节点，请将[CANNODE_TERM](../advanced_config/parameter_reference.md#CANNODE_TERM)设置为`1`。  
- 参数[EKF2_GPS_POS_X](../advanced_config/parameter_reference.md#EKF2_GPS_POS_X)、[EKF2_GPS_POS_Y](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Y)和[EKF2_GPS_POS_Z](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Z)可用于设置ARK GPS相对于机体重心的偏移量。