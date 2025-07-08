# DATAGNSS NANO HRTK接收器

NANO HRTK接收器是由[DATAGNSS](https://www.datagnss.com/)设计和制造的RTK接收器。

这是一款基于CYNOSURE系列芯片组的高性能双频段RTK接收器，内置指南针。  
支持全球民用导航系统，包括GPS、BDS、GLONASS、Galileo、QZSS和SBAS。

NANO HRTK接收器支持指南针。  
专为无人机、RTK和其他应用设计。

![DATAGNSS NANO HRTK接收器](../../assets/hardware/gps/datagnss_nano_hrtk/nano_hrtk_case.png)![DATAGNSS NANO HRTK天线](../../assets/hardware/gps/datagnss_nano_hrtk/nano_hrtk_antenna.png)

::: info
NANO HRTK接收器支持基准站和移动站模式。  
目前尚不支持移动基准站模式。
:::

## 购买渠道

- [NANO HRTK接收器](https://www.datagnss.com/collections/gnss-for-drone/products/nano-helix-rtk-receiver) (www.datagnss.com)

## 核心特性

- 全星座、多频段GNSS卫星接收器  
- 支持RTK输出速率达10Hz  
- 标准UART串行接口  
- 超轻型紧凑尺寸设计

## 频率支持

- GPS/QZSS: L1 C/A, L5C  
- GLONASS: L1OF  
- BEIDOU: B1I, B2a  
- GALILEO: E1, E5a  
- IRNSS: L5  

## GNSS性能

- 128硬件通道  
- 3D定位精度：**1.5m** CEP  
- RTK定位精度：**2cm** +1PPM(H), 3cm+1PPM(V)  

## 接口

- UART \*2 : 默认230400bps  
- SMA天线接口  
- 默认5Hz输出速率，最高10Hz  
- 主电源：4.7~5.2V  

## 通信协议

- NMEA-0183输出  
- RTCM3.x输入/输出  

## 环境要求

- 工作温度：-20~85°C  

## 尺寸与重量

- 35x30mm  
- 25g  

## 引脚分配

通过UART接口与飞控连接。

![NANO HRTK接收器](../../assets/hardware/gps/datagnss_nano_hrtk/nano_hrtk_rcv_line_drawing.png)

1.25mm间距的6P连接器支持GNSS的UART和指南针的I2C。

![PINOUT](../../assets/hardware/gps/datagnss_nano_hrtk/helix_rtk_pinout.png)

## 硬件配置

RTK需要地面站安装基准站RTK模块，机体安装移动站RTK模块。  
基准站数据需通过数传电台发送给机体的RTK接收器。

![RTK配置总览](../../assets/hardware/gps/datagnss_nano_hrtk/setup_overview.png)

基准站和移动站模块的配置/连接方式如下所示。

### 基准站配置（地面站）

基准站连接示意图如下，包含与数传电台的连接。

![基准站模块配置](../../assets/hardware/gps/datagnss_gem1305/base_gnss_setup.png)

注意：我们推荐使用[NANO RTK接收器](https://www.datagnss.com/collections/gnss-for-drone/products/multi-band-rtk-receiver-package)作为基准站，因其配置更简便。

![DATAGNSS NANO RTK接收器（带外壳）](../../assets/hardware/gps/datagnss_gem1305/nano_rtk_with_case.png)

详细配置请参阅[如何设置基准站](https://wiki.datagnss.com/index.php/GEM1305-autopilot#Base_station_setup)（不包含第6步及后续内容，这些步骤需使用QGroundControl而非Mission Planner）。

### 移动站配置（PX4）

移动站的GPS接口和数传电台连接示意图如下：

![移动站模块与Pixhawk连接示意图](../../assets/hardware/gps/datagnss_nano_hrtk/rover_gnss_setup.png)

下图显示了Pixhawk 6c飞控器上的`GPS2`接口接线，配套线缆已提供。

通过_QGroundControl_进行PX4的GPS和RTK配置可实现即插即用（详见[RTK GPS](../gps_compass/rtk_gps.md)获取更多信息）。

## 包装清单

- NANO HRTK接收器  
- DG-6P-Cxx、GH-1.25mm-GH 6P线缆  
- Helix L1/L2/L5天线（可选）  

## 资源

- [NANO RTK接收器2D图纸文件](https://wiki.datagnss.com/images/3/31/EVK-DG-1206_V.2.0.pdf)  
- [NANO HRTK接收器Wiki](https://docs.datagnss.com/gnss/rtk_receiver/NANO/nano-helix-rtk/) (DATAGNSS WiKi)  
- [H3系列RTK接收器](https://docs.datagnss.com/gnss_receiver/h3_series/)  

## 技术支持

- 官方网站：[www.datagnss.com](https://www.datagnss.com)  
- 技术文档：[docs.datagnss.com](https://docs.datagnss.com)  
- GitHub仓库：[github.com/datagnss](https://github.com/datagnss)