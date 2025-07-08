# Holybro H-RTK Unicore UM982 GPS

[Holybro H-RTK Unicore UM982 GPS](https://holybro.com/products/h-rtk-um982) 是 Holybro 推出的多频段高精度 [RTK GNSS系统](../gps_compass/rtk_gps.md)。

![HB-pmw3901-1](../../assets/hardware/gps/holybro-unicore-um982/holybro-unicore-um982-1.jpg)

该模块基于 [Unicore UM982芯片](https://en.unicorecomm.com/products/detail/24)，支持 RTK 定位和双天线航向计算。这意味着它可以通过单个 GPS 模块和双天线为飞控系统提供移动基线航向/偏航判定，无需磁力计。与需要 [两个 U-blox F9P 模块计算航向角](../gps_compass/u-blox_f9p_heading.md) 的 U-blox F9P 模块不同，使用 Unicore UM982 GPS 时仅需一个 GPS 模块即可！

通过 GPS 而非磁力计获取偏航信息，可避免磁场干扰导致飞控系统接收错误的偏航数据（磁力计常受机体电机、电气系统及其他环境干扰源如金属结构或设备的影响）。即使 GPS 未接收到固定 RTK 基站或 NTRIP 服务器的 RTCM 数据，该功能仍可运行。它支持厘米级精度的 RTK 定位校正，兼容 GPS/GLONASS/北斗/Galileo/QZSS 全球定位系统。

该模块还包含磁力计、LED 和安全开关按钮。它可作为 RTK 校正的机体 GPS（支持或不支持移动基线偏航判定），也可作为基准站 GPS 向地面控制站发送 RTCM 数据，通过遥测为机体提供 RTK 信号源。

更多技术信息请参考 [Holybro 技术文档页面](https://docs.holybro.com/gps-and-rtk-system/h-rtk-unicore-um982)

## 购买渠道

- [Holybro 官网](https://holybro.com/products/h-rtk-um982)

## 接线

模块配有 GH 10 针和 6 针线缆，兼容使用 [Pixhawk 接口标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 的飞控 GPS1/GPS2 接口，例如 [Pixhawk 6x](../flight_controller/pixhawk6x.md) 和 [Pixhawk 6c](../flight_controller/pixhawk6c.md)。

也可用于 Cubepilot 飞控。10Pin-6Pin 转接线缆允许用户将 UM982 连接到 Cubepilot 和 Holybro 自动驾驶仪的 `GPS2` 接口。

该模块可使用单天线或双天线。若仅使用单天线，必须连接右侧/主天线接口。

## PX4 配置

### 端口设置

Unicore 模块使用扩展了部分专有语句的 NMEA 协议。串口波特率为 230400。

需设置以下 PX4 参数 [详情](../advanced_config/parameters.md)：

- [SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD) -> 230400
- [GPS_1_PROTOCOL](../advanced_config/parameter_reference.md#GPS_1_PROTOCOL) -> 6: NMEA

注意：上述参数假设您连接的是 `GPS 1` 接口。若使用其他端口，需使用对应参数配置波特率和协议。

### 启用 GPS 偏航角

Unicore 模块配备主天线（右侧接口）和副天线（左侧接口），可用于从 GPS 获取偏航角。需设置以下参数：

- [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL): 设置位 3（8）以启用双天线航向角参与偏航估计。
- [GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET): 若主天线位于机体前方，将偏航角偏移设为 0。角度按顺时针方向增加，若主天线位于机体右侧（副天线在左侧），则偏移设为 90 度。

### RTK 校正

RTK 的工作方式与 uBlox F9P 模块相同。由 QGroundControl 从 RTK GPS 基准站发送的 RTCMv3 校正数据将被 Unicore 模块接收，随后固定类型应变为 `RTK float` 或 `RTK fixed`。