

# CUAV v5（已停产）

<Badge type="info" text="已停产" />

:::warning
该飞控已[停产](../flight_controller/autopilot_experimental.md)，目前不再商业发售。
:::

:::warning
PX4 不生产该（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://store.cuav.net/)。
:::

_CUAV v5_<sup>&reg;</sup>（此前称为“Pixhack v5”）是一款由 CUAV<sup>&reg;</sup> 设计制造的先进自动驾驶仪。该板基于 [Pixhawk-project](https://pixhawk.org/) **FMUv5** 开源硬件设计。它在 [NuttX](https://nuttx.apache.org/) 操作系统上运行 PX4 固件，并与 PX4 固件完全兼容。该产品主要面向学术和商业开发者。

![CUAV v5](../../assets/flight_controller/cuav_v5/pixhack_v5.jpg)

## 快速概览

- 主FMU处理器: STM32F765  
  - 32位Arm® Cortex®-M7，216MHz，2MB内存，512KB RAM  
- IO处理器: STM32F100  
  - 32位Arm® Cortex®-M3，24MHz，8KB SRAM  
- 机载传感器:  

  - 加速度计/陀螺仪: ICM-20689  
  - 加速度计/陀螺仪: BMI055  
  - 磁力计: IST8310  
  - 气压计: MS5611  

- 接口:  
  - 8-14个PWM输出（IO提供6个，FMU提供8个）  
  - FMU上3个专用PWM/Capture输入  
  - 专用R/C输入用于CPPM  
  - 专用R/C输入用于PPM和S.Bus  
  - 模拟/PWM RSSI输入  
  - S.Bus舵机输出  
  - 5个通用串口  
  - 4个I2C端口  
  - 4个SPI总线  
  - 2个带串口电调的CAN总线  
  - 2个电池电压/电流的模拟输入  
- 电源系统:  
  - 供电电压: 4.3~5.4V  
  - USB输入电压: 4.75~5.25V  
  - 舵机电源输入: 0~36V  
- 重量与尺寸:  
  - 重量: 90g  
  - 尺寸: 44x84x12mm  
- 其他特性:  
  - 工作温度: -20 ~ 80°C（实测值）

## 在哪里购买

从 [CUAV](https://cuav.taobao.com/index.htm?spm=2013.1.w5002-16371268426.2.411f26d9E18eAz) 购买

## 连接

![CUAV v5](../../assets/flight_controller/cuav_v5/pixhack_v5_connector.jpg)

:::warning
RCIN接口仅限于为接收机供电，不能连接任何电源/负载。
:::

## 电压规格

_CUAV v5_ 在提供三个电源时可实现三重冗余供电。三个电源通道分别为：**POWER1**、**POWER2** 和 **USB**。

::: info
输出电源通道 **FMU PWM OUT** 和 **I/O PWM OUT** (0V 到 36V) 不为飞控板供电（也不会被其供电）
必须向 **POWER1**、**POWER2** 或 **USB** 中的一个供电，否则电路板将无法获得电力
:::

**正常运行最大规格**

在以下条件下，系统将按此顺序使用所有电源供电：

1. **POWER1** 和 **POWER2** 输入（4.3V 到 5.4V）
1. **USB** 输入（4.75V 到 5.25V）

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接合适硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要[构建 PX4](../dev_setup/building_px4.md)：

```
make px4_fmu-v5_default
```

## 调试端口

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)在**FMU调试**端口上运行。  
只需将FTDI电缆连接到Debug & F7 SWD连接器。  
要访问I/O调试端口，用户必须移除CUAV v5外壳。  
两个端口均具有标准串行引脚，可连接到标准FTDI电缆（3.3V，但具有5V容限）。

引脚定义如下所示。

![CUAV v5调试](../../assets/flight_controller/cuav_v5/pixhack_v5_debug.jpg)

| 引脚 | CUAV v5调试 |
| --- | ------------- |
| 1   | GND           |
| 2   | FMU-SWCLK     |
| 3   | FMU-SWDIO     |
| 4   | UART7_RX      |
| 5   | UART7_TX      |
| 6   | VCC           |

## 串口映射

| UART   | 设备       | 端口                                  |
| ------ | ---------- | ------------------------------------- |
| UART1  | /dev/ttyS0 | GPS                                   |
| USART2 | /dev/ttyS1 | TELEM1（流控制）                      |
| USART3 | /dev/ttyS2 | TELEM2（流控制）                      |
| UART4  | /dev/ttyS3 | TELEM4                                |
| USART6 | /dev/ttyS4 | TX 是来自 SBUS_RC 接口的遥控输入      |
| UART7  | /dev/ttyS5 | 调试控制台                            |
| UART8  | /dev/ttyS6 | PX4IO                                 |

<!-- 注：获取端口使用 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->

## 外设

- [数字空速传感器](https://item.taobao.com/item.htm?spm=a1z10.3-c-s.w4002-16371268452.37.6d9f48afsFgGZI&id=9512463037)
- [遥测无线电模块](https://cuav.taobao.com/category-158480951.htm?spm=2013.1.w5002-16371268426.4.410b7a821qYbBq&search=y&catName=%CA%FD%B4%AB%B5%E7%CC%A8)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机架

任何可通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/地面车或船只。所有支持的配置可在[机架参考](../airframes/airframe_reference.md)中查看。

## 更多信息

- [FMUv5参考设计引脚分配](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165).
- [CUAV v5文档](http://doc.cuav.net/flight-controller/v5-autopilot/en/v5.html)
- [CUAV GitHub](https://github.com/cuav)