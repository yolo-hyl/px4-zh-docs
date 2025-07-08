# CUAV C-RTK2 GNSS模块 (RTK/PPK)

[CUAV C-RTK2接收器](https://www.cuav.net/en/c_rtk_9ps/) 是CUAV为无人机航测航拍等专业应用开发的高性能PPK/RTK定位模块。该模块集成高精度IMU和定位模块，可将[控制点](https://www.youtube.com/watch?v=3k7v5aXyuKQ)数量减少80%以上。除测绘应用外，还适用于农业植保、无人机蜂群等场景。

![C-RTK2](../../assets/hardware/gps/cuav_rtk2/c-rtk2.png)

## 其他特性

- 高性能H7处理器
- 工业级高精度IMU
- 同时支持RTK和RAW数据保存(PPK)
- 多卫星多频段接收器
- UAVCAN/Dronecan协议
- 支持热靴和快门触发
- HS_USB和U盘模式

## 购买渠道

- [CUAV官方商城](https://store.cuav.net/shop/c-rtk-2/)
- [CUAV阿里速卖通](https://pt.aliexpress.com/item/1005003754165772.html?spm=a2g0o.store_pc_groupList.8148356.13.2f893550i0NE4o)

# 快速概览

- RTK接收器
  - ZED-F9P
- 接收通道
  - 184
- 主FMU处理器
  - STM32H743VIH6(2M闪存、1M RAM）
- 机载传感器：
  - 加速度计/陀螺仪：ICM20689
  - 磁力计：RM3100
  - 气压计：ICP10111
- TF卡扩展
  - 32G(MAX)
- PPK(后处理动态定位)
  - 支持
- RTK(实时动态定位)
  - 支持
- GNSS频段
  - GPS:L1C/A,L2C
  - GLONASS:L1OF,L2OF
  - GALILEO: E1B/C E5b
  - 北斗:B1I B2I
- 增强系统
  - QZSS:L1C/A,L2C,L1S
  - SBAS:L1C/A
- 同时支持GNSS数量
  - 4(GPS、GLONASS、GALILEO、北斗）
- 导航更新率
  - RTK 最高20Hz
  - RAW 最高25Hz
  - 默认：5Hz
- 会聚时间
  - RTK <10秒
- 定位精度（RMS)
  - RTK:0.01m+1ppm(水平)；0.02m+1ppm(垂直)
  - GPS:1.5m(水平)
- 捕获能力
  - 冷启动24秒
  - 辅助启动2秒
  - 重捕获2秒
- 灵敏度
  - 跟踪与导航 -167dBm
  - 冷启动 -148dBm
  - 热启动 -157dBm
  - 重捕获 -160dBm
- 抗欺骗
  - 高级抗欺骗算法
- 协议
  - NMEA
  - UBX二进制
  - RTCM 3.x版本
- 时间脉冲
  - 0.25Hz~10Hz(可配置)
- 抗干扰
  - 主动CW检测和移除，板载带通滤波器
- 支持的飞控类型
  - 兼容运行PX4固件的飞控
- 接口
  - 1个热靴
  - 1个快门输入
  - 1个快门输出
  - 1个Type(HS_USB)
  - 1个F9P USB
  - 1个F9P UART
  - 1个天线(mmcx)
- 供电电压
  - 4.5~6V
- 工作温度
  - -20~85℃
- 尺寸
  - 56x33x16.5mm
- 重量
  - 39g

## 配置

[CUAV 文档](https://doc.cuav.net/gps/c-rtk2/en/quick-start-c-rtk2.html)

## 引脚定义

![C-RTK2](../../assets/hardware/gps/cuav_rtk2/c-rtk2_pinouts1.jpg)

![C-RTK2](../../assets/hardware/gps/cuav_rtk2/c-rtk2_pinouts0.jpg)

![C-RTK2](../../assets/hardware/gps/cuav_rtk2/c-rtk2_pinouts2.jpg)

## 更多信息

[CUAV 文档](https://doc.cuav.net/gps/c-rtk-series/en/c-rtk-9ps/)