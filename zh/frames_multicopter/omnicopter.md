

# 全向多旋翼

全向多旋翼是一种多旋翼飞行器，能够在所有方向上提供推力（六自由度）。
这使其可以在任何方向移动而无需倾斜，并且可以在任意倾斜角度悬停。
所有这些功能都是通过以特定方式安排电机位置和推力轴实现的：

![全向多旋翼](../../assets/airframes/multicopter/omnicopter/frame.jpg)

该设计遵循[Brescianini, Dario, 和 Raffaello D'Andrea](https://www.youtube.com/watch?v=sIi80LMLJSY)的原始设计。

## 物料清单

本构建所需的组件包括：

- 电子元件：
  - 飞控器：[Holybro KakuteH7](../flight_controller/kakuteh7.md)
  - 搭配2个[Tekko32 F4 4in1电调](https://holybro.com/products/tekko32-f4-4in1-50a-esc)
   ::: info
   您可以选择自己喜欢的飞控器，只要它支持8路DShot输出即可。
   :::
  - GPS：[ZED-F9P](https://www.gnss.store/gnss-gps-modules/105-ublox-zed-f9p-rtk-gnss-receiver-board-with-sma-base-or-rover.html?search_query=ZED-F9P&results=11)
  - [GPS螺旋天线](https://www.gnss.store/rf-gps-antennas/28-high-performance-multi-band-gnss-active-quad-helix-antenna-for-rtk.html)
   ::: info
   其他GPS设备可能同样适用，但螺旋天线在倒飞时性能可能更佳。
   :::
  - 任意遥控接收机
  - 外部磁力计。我们使用了[RM-3100](https://store-drotek.com/893-professional-grade-magnetometer-rm3100.html)。
  - 通信链路，例如[WiFi](../telemetry/telemetry_wifi.md)
- 动力系统：
  - 电机：8个[BrotherHobby LPD 2306.5 2000KV/2450KV/2650KV](https://www.getfpv.com/brotherhobby-lpd-2306-5-2000kv-2450kv-2650kv-motor.html)
  - 3D桨叶：2个[HQProp 3D 5X3.5X3 三叶桨（4片装）](https://www.getfpv.com/hqprop-3d-5x3-5x3-3-blade-propeller-set-of-4.html) 或 2个[Gemfan 513D 三叶3D桨（4片装）](https://www.getfpv.com/gemfan-513d-durable-3-blade-propeller-set-of-4.html)
  - 电池：我们使用了6S 3300mAh LiPo。请确保尺寸合适以适配机架。
  - 电池绑带
- 机架：
  - 8mm×7mm×1000mm碳纤维方管，例如[此处](https://shop.swiss-composite.ch/pi/Halbfabrikate/Rohre/Vierkant-Rohre/CFK-Vierkantrohr-8x8-7x7mm.html)
  - 3mm×2mm×1000mm碳纤维棒材，例如[此处](https://shop.swiss-composite.ch/pi/Halbfabrikate/Rohre/CFK-Rohre-pultrudiert-pullwinding/Carbon-Microtubes-100cm-x-20-3mm.html)
  - 需要的长度：
    - 方管：8根，每根248mm
    - 棒材：12根328mm，6根465mm
  - 螺丝：
    - 电机和垫片：40个M3×12mm
	- 飞控安装：4个M3×35mm，4个M3螺母
  - 垫片：4个40mm
- [3D模型](https://cad.onshape.com/documents/eaff30985f1298dc6ce8ce13/w/2f662e604240c4082682e5e3/e/ad2b2245b73393cf369132f7)

![部件清单](../../assets/airframes/multicopter/omnicopter/parts_list.jpg)

## 组装

### 框架

- 打印3D部件  
  ::: info  
  角落部件的方向很重要。  
  当杆件的角度不正确时，你会注意到这一点。  
  :::  
- 裁剪杆件  
- 通过连接框架部件测试是否正常工作：  

  ![Frame](../../assets/airframes/multicopter/omnicopter/frame_only.jpg)  
- 将电机尽可能向外放置，避免螺旋桨接触杆件。

### 电子设备

将外围设备焊接到飞控上。我们采用了以下分配方式：
- 电调：两个电调可直接连接到 KakuteH7 的两个连接器。
  为避免冲突，我们移除了其中一个连接器的最右侧电源引脚。
- 通信链路连接至 UART1
- GPS 连接至 UART4
- 遥控器连接至 UART6
![飞控特写](../../assets/airframes/multicopter/omnicopter/fc_closeup.jpg)

注意事项：

- 确保磁力计远离电源。
  我们最终将其安装在中心结构底部，并用4cm厚的泡沫塑料进行隔离。
- 在气压计表面贴胶带（注意不要封住开口！）以避免光线干扰。
- 我们没有使用胶水固定机架。
  在完成初步试飞后建议进行粘接固定，但也可以不粘接直接使用。

## 软件配置

### 电调

首先，将电调配置为3D模式（双向）。  
我们发现3D模式下默认电调设置存在问题：当尝试切换方向时，电机有时会无法启动，必须重启电调才能恢复。因此我们需要修改电调参数。

为此，可以通过飞控上的Betaflight使用直通模式和BL Heli套件（确保Betaflight中配置了8电机的机架）。以下是具体设置：

![电调设置](../../assets/airframes/multicopter/omnicopter/esc_settings.png)

特别注意：
- 电机方向设置为**Bidirectional Soft**
- 将加速功率提升至**100%**（该设置较为保守，可能会降低效率）

::: info  
确保更改参数后电机不会过热。  
:::

### PX4

- 选择通用多旋翼机架  
- 使用[上锁开关](../advanced_config/prearm_arm_disarm.md#arming-button-switch)，不要使用摇杆上锁  
- [选择DShot](../config/actuators.md)作为所有八个输出的输出协议  
- 根据以下方式配置电机：  
  ![电机配置](../../assets/airframes/multicopter/omnicopter/motors_configuration.png)  
  我们采用的约定是：电机面向轴指向的方向。旋转方向与正向推力方向一致（向上移动电机滑块）。  
  确保使用正确的螺旋桨，因为有CCW和CW两种版本。  
- 参数：  
  - 修改反饱和逻辑以改善姿态跟踪：将[CA_METHOD](../advanced_config/parameter_reference.md#CA_METHOD)设为0。  
  - 禁用故障检测：将[FD_FAIL_P](../advanced_config/parameter_reference.md#FD_FAIL_P)和[FD_FAIL_R](../advanced_config/parameter_reference.md#FD_FAIL_R)设为0。  
- [该文件](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/multicopter/omnicopter/omnicopter.params)包含所有相关参数。

## 视频

<lite-youtube videoid="nsPkQYugfzs" title="PX4 Based Omnicopter Using the New Dynamic Control Allocation in v1.13"/>

## 模拟

Gazebo Classic 中有一个 omnicopter 模拟目标：

```sh
make px4_sitl gazebo-classic_omnicopter
```
![Gazebo Sim](../../assets/airframes/multicopter/omnicopter/gazebo.png)

## 注意事项

一些通用注意事项：

- 悬停油门约为30%。
- 飞行时间大约为4-5分钟。使用更大的螺旋桨可能可以稍微提高一下。