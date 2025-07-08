# 全向多旋翼

全向多旋翼是一种可以提供全方位推力的多旋翼飞行器（6自由度）。  
这使得它可以在不倾斜的情况下向任意方向移动，并且可以在任意倾斜角度下悬停。  
所有功能通过特定方式布置电机位置和推力轴实现：

![全向多旋翼](../../assets/airframes/multicopter/omnicopter/frame.jpg)

该设计基于原始方案来自 [Brescianini, Dario, and Raffaello D'Andrea](https://www.youtube.com/watch?v=sIi80LMLJSY)。

## 物料清单

该构建所需的组件：

- 电子元件：
  - 飞控：[Holybro KakuteH7](../flight_controller/kakuteh7.md)
  - 配套使用2个[Tekko32 F4 4in1 ESC](https://holybro.com/products/tekko32-f4-4in1-50a-esc)
   ::: info
   可以选择自己喜欢的飞控，但需要支持8个DShot输出。
   :::
  - GPS：[ZED-F9P](https://www.gnss.store/gnss-gps-modules/105-ublox-zed-f9p-rtk-gnss-receiver-board-with-sma-base-or-rover.html?search_query=ZED-F9P&results=11)
  - [GPS螺旋天线](https://www.gnss.store/rf-gps-antennas/28-high-performance-multi-band-gnss-active-quad-helix-antenna-for-rtk.html)
   ::: info
   其他GPS也可能工作，但螺旋天线在倒飞时预计性能更好。
   :::
  - 任意遥控接收机
  - 外部磁力计。我们使用了[RM-3100](https://store-drotek.com/893-professional-grade-magnetometer-rm3100.html)。
  - 通信链路，例如[WiFi](../telemetry/telemetry_wifi.md)
- 推进系统：
  - 电机：8个[BrotherHobby LPD 2306.5 2000KV/2450KV/2650KV](https://www.getfpv.com/brotherhobby-lpd-2306-5-2000kv-2450kv-2650kv-motor.html)
  - 3D桨叶：2个[HQProp 3D 5X3.5X3 3叶桨（4片装）](https://www.getfpv.com/hqprop-3d-5x3-5x3-3-blade-propeller-set-of-4.html) 或 2个[Gemfan 513D 3叶3D桨（4片装）](https://www.getfpv.com/gemfan-513d-durable-3-blade-propeller-set-of-4.html)
  - 电池：我们使用了6S 3300mAh LiPo。请确保尺寸合适以适应机架。
  - 电池绑带
- 机架：
  - 碳方管 R 8mm X 7mm X 1000mm，例如[此处](https://shop.swiss-composite.ch/pi/Halbfabrikate/Rohre/Vierkant-Rohre/CFK-Vierkantrohr-8x8-7x7mm.html)
  - 碳棒 R 3mm X 2mm X 1000mm，例如[此处](https://shop.swiss-composite.ch/pi/Halbfabrikate/Rohre/CFK-Rohre-pultrudiert-pullwinding/Carbon-Microtubes-100cm-x-20-3mm.html)
  - 所需长度：
    - 方管：8个248mm长度
    - 碳棒：12x328mm，6x465mm
  - 螺丝：
    - 电机和支架：40个M3x12mm
	- 飞控安装：4个M3x35mm，4个M3螺母
  - 支架：4个40mm
- [3D模型](https://cad.onshape.com/documents/eaff30985f1298dc6ce8ce13/w/2f662e604240c4082682e5e3/e/ad2b2245b73393cf369132f7)

![部件清单](../../assets/airframes/multicopter/omnicopter/parts_list.jpg)

## 组装

### 机架

- 打印3D部件
  ::: info
  角部部件的方向很重要。
  当杆件的角度不正确时会发现方向错误。
  :::
- 切割碳棒
- 通过连接机架部件测试是否可行：

  ![机架](../../assets/airframes/multicopter/omnicopter/frame_only.jpg)
- 将电机尽可能向外安装，确保桨叶不接触碳棒。

### 电子元件

将外设焊接至飞控。我们使用了以下分配：
- 电调：2个电调可以直接连接到KakuteH7的两个接口。
  为避免冲突，我们移除了其中一个接口的电源引脚（最右侧引脚）。
- 通信链路连接至UART1
- GPS连接至UART4
- 遥控接收机连接至UART6
![飞控特写](../../assets/airframes/multicopter/omnicopter/fc_closeup.jpg)

注意事项：

- 确保磁力计远离电源。
  我们最终将其安装在中心部件底部，用4cm的泡沫垫隔开。
- 在气压计上贴胶带（不要遮挡开口！）以防止灰尘进入。
- 使用Betaflight配置文件时，请确保启用"Advanced features"中的"Use external mag"。

## 软件配置

### 参数设置

- 飞行时间约为4-5分钟。通过使用更大的桨叶可能能稍作改善。
- 悬停油门大约30%。

```sh
make px4_sitl gazebo-classic_omnicopter
```
![Gazebo仿真](../../assets/airframes/multicopter/omnicopter/gazebo.png)

<lite-youtube videoid="nsPkQYugfzs" title="基于PX4的全向多旋翼使用v1.13中的新动态控制分配"/>

## 说明

一些通用说明：

- 该配置文件包含所有相关参数。
- 飞行时间约为4-5分钟。通过使用更大的桨叶可能能稍作改善。