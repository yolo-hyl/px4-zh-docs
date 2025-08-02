

# VOXL 2 Starling PX4开发无人机

[Starling](https://modalai.com/starling) 是由[VOXL 2](../flight_controller/modalai_voxl_2.md)和PX4驱动的SLAM开发无人机，配备SWAP优化的传感器和有效载荷，适用于室内和室外自主导航。  
搭载Blue UAS Framework自动驾驶仪和VOXL 2，Starling仅重275克，具备长达30分钟的室内自主飞行时间。

![概述](../../assets/hardware/complete_vehicles/modalai_starling/starling_front_hero.jpg)

VOXL 2 Starling 是一款开箱即用的PX4开发无人机，内置[VOXL 2](../flight_controller/modalai_voxl_2.md)协处理器和PX4飞行控制器、图像传感器、GPS以及通信模块。  
Starling采用ModalAI的[开放SDK](https://docs.modalai.com/voxl-developer-bootcamp/)，预配置了计算机视觉辅助飞行的自主模型。  
这款开发无人机旨在帮助您加速产品上市进程，提升应用开发和原型设计效率。

本指南介绍了使无人机具备飞行能力所需的最小化额外设置，涵盖硬件概览、首次飞行、WiFi配置等内容。

::: info  
完整且持续更新的文档请访问 <https://docs.modalai.com/starling-v2>。  
:::

::: info  
如果您是VOXL新手，请务必通过[VOXL入门教程](https://docs.modalai.com/voxl-developer-bootcamp/)熟悉VOXL软硬件的核心功能。  
:::

## 购买地点

[modalai.com/starling](https://modalai.com/starling)

## 硬件概述

![Hardware Overview](../../assets/hardware/complete_vehicles/modalai_starling/mrb_d0005_4_v2_c6_m22__callouts_a.jpg)

| 标注 | 描述                           | MPN              |
| ------- | ------------------------------------- | ---------------- |
| A       | VOXL 2                                | MDK-M0054-1      |
| B       | VOXL 4-in-1 电调                       | MDK-M0117-1      |
| C       | 气压计防护盖                          | M10000533        |
| D       | ToF 图像传感器 (PMD)                | MDK-M0040        |
| E       | 跟踪图像传感器 (OV7251)        | M0014            |
| F       | 高分辨率图像传感器 (IMX214)           | M0025-2          |
| G       | AC600 WiFi 适配器                     | AWUS036EACS      |
| H       | GNSS GPS 模块 & 指南针             | M10-5883         |
| I       | 915MHz ELRS 接收器                  | BetaFPV Nano RX  |
| J       | VOXL 2 上的 USB C 接口 (未显示) |                  |
| K       | VOXL 电源模块                     | MCCA-M0041-5-B-T |
| L       | 4726FM 旋翼                      | M10000302        |
| M       | 电机 1504                            |                  |
| N       | XT30 电源接口                  |                  |

## 数据手册

### 规格参数

| 组件       | 规格参数                                                     |
|------------|-------------------------------------------------------------|
| 飞控       | VOXL2                                                         |
| 起飞重量   | 275g (172g 去除电池)                                        |
| 对角尺寸   | 211mm                                                         |
| 飞行时间   | >30分钟                                                       |
| 电机       | 1504                                                          |
| 螺旋桨     | 120mm                                                         |
| 机身       | 3mm 碳纤维                                                  |
| 电调       | ModalAI VOXL 4-in-1 ESC V2                                  |
| GPS        | UBlox M10                                                     |
| 遥控接收机 | 915mhz ELRS                                                   |
| 电源模块   | ModalAI Power Module v3 - 5V/6A                             |
| 电池       | Sony VTC6 3000mah 2S, 或任何带 XT30 接口的 2S 18650 电池    |
| 高度       | 83mm                                                          |
| 宽度       | 187mm (螺旋桨折叠)                                            |
| 长度       | 142mm (螺旋桨折叠)                                            |

### 硬件接线图

![硬件接线图](../../assets/hardware/complete_vehicles/modalai_starling/d0005_compute_wiring_d.jpg)

## 教程

### ELRS 设置

将您的 ELRS (ExpressLRS) 接收器与发射器配对是为基于 VOXL 2 的 PX4 Autonomy Developer Kit by ModalAI 准备飞行的关键步骤。该过程确保您的无人机与其控制系统之间建立安全且响应迅速的连接。

请按照本指南将您的 ELRS 接收器与发射器配对。

#### 设置接收机

1. **开启接收机电源**：当无人机通电后，您会注意到ELRS接收机的蓝灯开始闪烁。
   这表示接收机已上电但尚未与发射机建立连接。

   ![Starling 接收机](../../assets/hardware/complete_vehicles/modalai_starling/starling-photo.png)

2. **进入配对模式**：要启动配对过程，请打开终端并执行 `adb shell` 和 `voxl-elrs -bind` 命令。
   您会观察到接收机的LED开始以心跳模式闪烁，表明其已进入配对模式。

   ![启动屏幕截图](../../assets/hardware/complete_vehicles/modalai_starling/screenshot-boot.png)

#### 遥控器设置

1. **进入菜单**：在套件中包含的Commando 8遥控器上，按下左侧模式按钮以打开菜单系统。

   ![按遥控器上的菜单](../../assets/hardware/complete_vehicles/modalai_starling/radio-1.png)

2. **导航到ExpressLRS**：使用右侧按钮选择第一个菜单项（应为"ExpressLRS"）。

3. **查找绑定选项**：选中"ExpressLRS"选项后，向下滚动到底部菜单以找到"Bind"部分。可以通过持续按下右侧按钮直到到达"Bind"选项。

   ![按遥控器上的绑定](../../assets/hardware/complete_vehicles/modalai_starling/radio-2.png)

4. **启动绑定**：选择"Bind"将遥控器置于绑定模式。当遥控器发出蜂鸣声时，表示绑定成功。

#### 完成绑定过程

当遥控器设置为绑定模式时，无人机上的ELRS接收机LED指示灯将从闪烁变为常亮，表示接收机与遥控器已成功连接。

- **电源循环**：完成绑定后，务必在尝试飞行前对VOXL 2进行电源循环。
  这意味着关闭VOXL 2电源后再重新开启。
  此步骤可确保所有设置正确应用，系统识别新建立的连接。

现在你的ELRS接收机已成功与遥控器绑定，可配合ModalAI的PX4 Autonomy Kit使用。
安全连接对无人机的可靠运行至关重要，因此每次飞行前请务必确认绑定状态。

### 视频

- [VOXL 2 Starling 硬件概述](https://youtu.be/M9OiMpbEYOg)
- [VOXL 2 Starling 首飞教程](https://youtu.be/Cpbbye3Z6co)
- [VOXL 2 Starling ELRS 设置](https://youtu.be/7OwGS-kcFVg)