# mRo-X2.1 自动驾驶仪

:::warning
PX4 并不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.mrobotics.io/)
:::

[mRo-X2.1 自动驾驶仪](http://www.mRobotics.io/)基于[Pixhawk<sup>&reg;</sup>-project](https://pixhawk.org/) **FMUv2** 开源硬件设计  
它在[NuttX](https://nuttx.apache.org/)系统上运行PX4

![mRo X2.1](../../assets/flight_controller/mro/mro_x2.1.jpg)

::: info
此飞控设备[获得制造商支持](../flight_controller/autopilot_manufacturer_supported.md)
:::

## 快速概览

- 主系统芯片：[STM32F427](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN1789)  
  - CPU: STM32F427VIT6 ARM<sup>&reg;</sup> 微控制器 - 第3版  
  - IO: STM32F100C8T6 ARM<sup>&reg;</sup> 微控制器  
- 传感器：  
  - Invensense<sup>&reg;</sup> MPU9250 9轴传感器  
  - Invensense ICM-20602 6轴传感器  
  - MEAS MS5611 气压计  
- 尺寸/重量  
  - 尺寸：36mm x 50mm  
    （可选择竖直、水平或无接口安装）  
  - 安装孔：30.5mm x 30.5mm 3.2mm直径  
  - 重量：10.9g

下图与Pixhawk 1进行并列对比。mRo具有几乎相同的硬件和连接性，但尺寸显著更小。主要差异包括更新的传感器和第3版FMU。

![Mro Pixhawk 1 vs X2.1 对比](../../assets/flight_controller/mro/px1_x21.jpg)

## 连接性

- 2.54mm 接口：
- GPS (UART4) 支持I2C
- CAN总线
- 遥控输入
- PPM输入
- Spektrum输入
- RSSI输入
- sBus输入
- sBus输出
- 电源输入
- 蜂鸣器输出
- LED输出
- 8个舵机输出
- 6个辅助输出
- 机载microUSB接口
- 关断引脚输出 _(固件目前不支持)_
- 空速传感器
- USART2 (遥测1)
- USART3 (遥测2)
- UART7 (控制台)
- UART8 (OSD)

## PX4 BootLoader 问题

mRo X2.1默认可能预配置为ArduPilot<sup>&reg;</sup>而非PX4。这在通过固件更新时可通过板载识别为FMUv2而非X2.1来发现。

此时您必须使用[BL_Update_X21.zip](https://github.com/PX4/PX4-user_guide/raw/main/assets/hardware/BL_Update_X21.zip)更新BootLoader。  
若不执行此修正，指南针方向将错误，且次级IMU将无法被检测。

更新步骤如下：

1. 下载并解压[BL_Update_X21.zip](https://github.com/PX4/PX4-user_guide/raw/main/assets/hardware/BL_Update_X21.zip)
2. 找到文件夹_BL_Update_X21_。其中包含一个**bin**文件和一个名为**/etc**的子文件夹，包含**rc.txt**文件
3. 将这些文件复制到micro SD卡的根目录并插入mRO x2.1
4. 开启mRO x2.1。等待启动后重启1次

## 可用性

此产品可在[mRobotics<sup>&reg;</sup> Store](https://store.mrobotics.io/mRo-X2-1-Rev-2-p/m10021a.htm)订购

## 接线指南

![mRo_X2.1 接线图](../../assets/flight_controller/mro/mro_x21_wiring.png)

## 固件构建

:::tip
大多数用户无需构建此固件！  
当连接适当硬件时，QGroundControl会自动预构建并安装。
:::

要为此目标[构建PX4](../dev_setup/building_px4.md)：

```
make mro_x21_default
```

## 电路图

该板载硬件在mRo硬件仓库中记录：[x21_V2_schematic.pdf](https://github.com/mRoboticsIO/Hardware/blob/master/X2.1/Docs/x21_V2_schematic.pdf)

## 串口映射

| UART   | 设备         | 端口            |
| ------ | ------------ | --------------- |
| USART1 | /dev/ttyS0   | IO调试          |
| USART2 | /dev/ttyS1   | SERIAL1         |
| USART3 | /dev/ttyS2   | TELEM2          |
| UART4  | /dev/ttyS3   | GPS/I2C         |
| USART6 | /dev/ttyS4   | PX4IO           |
| UART7  | /dev/ttyS5   | SERIAL5 CONSOLE |
| UART8  | /dev/ttyS6   | SERIAL4         |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口信息 -->