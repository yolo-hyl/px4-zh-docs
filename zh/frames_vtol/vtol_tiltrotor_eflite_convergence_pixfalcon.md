# E-flite Convergence 倾斜旋翼机 VTOL（Pixfalcon）

[E-Flite Convergence](https://youtu.be/HNedXQ_jhYo) 可轻松通过 PX4 改装为全自主 VTOL。  
虽然空间有限，但足以容纳 [Pixfalcon](../flight_controller/pixfalcon.md) 飞行控制器、GPS 和遥测设备。

::: info
原始 Horizon Hobby *E-Flite Convergence* 机身和 [Pixfalcon](../flight_controller/pixfalcon.md) 已停产。  
替代方案见 [购买](#购买渠道) 部分。
:::

<lite-youtube videoid="E61P2f2WPNU" title="E-flite Convergence 自主任务飞行"/>

## 购买渠道

机体框架选项：
- **WL Tech XK X450** - [AliExpress](https://www.aliexpress.com/item/1005001946025611.html)
- **JJRC M02** - [Banggood (AU)](https://au.banggood.com/JJRC-M02-2_4G-6CH-450mm-Wingspan-EPO-Brushless-6-axis-Gyro-Aerobatic-RC-Airplane-RTF-3D-or-6G-Mode-Aircraft-p-1588201.html), [AliExpress](https://www.aliexpress.com/item/4001031497018.html)

飞行控制器选项：
- [Pixhawk 4 Mini](../flight_controller/pixhawk4_mini.md)
- [Holybro Pixhawk Mini](../flight_controller/pixhawk_mini.md)
- 任何其他兼容的小型飞行控制器

## 硬件配置

该机体需要7个PWM信号控制电机和舵面：
- 电机（左/右/后）
- 倾斜舵机（右/左）
- 升降舵（左/右）

这些信号可按任意方式连接至飞控输出（尽管电机输出应集中分组等）。

输出配置通过 [执行器配置](../config/actuators.md) 按照 VTOL 倾斜旋翼机几何结构和输出配置说明进行。  
注意需从 [通用倾斜旋翼机 VTOL](../airframes/airframe_reference.md#vtol_vtol_tiltrotor_generic_tiltrotor_vtol) 机体开始配置。

注意配置界面和机体参考中左右方向的定义是基于人类飞行员在真实飞机内部的视角（或从上方观察，如下图所示）：

<img src="../../assets/airframes/types/VTOLTiltRotor_eflite_convergence.svg" width="300px" />

### 飞行控制器

飞控可安装在原自动驾驶仪相同位置。

![安装Pixfalcon](../../assets/airframes/vtol/eflite_convergence_pixfalcon/eflight_convergence_pixfalcon_mounting.jpg)

### 遥测模块

遥测模块可安装在原本用于FPV发射设备的舱室内。

![安装遥测模块](../../assets/airframes/vtol/eflite_convergence_pixfalcon/eflight_convergence_telemetry_module.jpg)

### GPS

GPS安装时我们切除了座舱内部分泡沫材料。  
这样GPS可隐藏在机体内部，外观不受影响。

![安装GPS](../../assets/airframes/vtol/eflite_convergence_pixfalcon/eflight_convergence_gps_mounting.jpg)

## PX4 配置

在 *QGroundControl* 中按照 [标准配置](../config/index.md)（遥控器、传感器、飞行模式等）操作。

与本机体相关的特定设置包括：
- [机体型号](../config/airframe.md)
  - 在 **VTOL Tiltrotor** 下选择 **E-flite Convergence** 机体配置并重启 *QGroundControl*。  
    ![QGroundControl 机体设置 - E-Flight 机体选择](../../assets/airframes/vtol/eflite_convergence_pixfalcon/qgc_setup_airframe.jpg)
- [飞行模式/切换](../config/flight_mode.md)
  - 由于这是 VTOL 机体，必须[分配一个遥控器切换开关](../config/flight_mode.md#what-flight-modes-and-switches-should-i-set)用于多旋翼与固定翼模式的切换。