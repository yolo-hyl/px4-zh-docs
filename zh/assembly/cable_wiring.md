

# 电缆布线基础

电缆是[电磁干扰（EMI）](https://en.wikipedia.org/wiki/Electromagnetic_interference)的常见来源，可能导致航向异常、"旋转下坠"等飞行问题，以及整体飞行性能下降。
这些问题可通过在无人机中使用适当的布线方式来避免。

设计无人机电缆时应牢记以下基本原则：

- 高功率线缆和信号线缆应在可行范围内尽可能分离。
- 电缆长度应尽可能缩短以方便线缆组件的操作处理。
  线材张力需足够承受机体变形（包括迫降时的变形），
  （线材不应是第一个断裂的部件）。
- 应避免通过电缆环路减少冗余长度-直接使用更短的线缆！
- 对于数字信号可通过降低波特率来减少辐射能量并提升数据传输稳定性。
  这意味着在不需要高数据速率的情况下可以使用更长的电缆。

## 信号接线

信号协议具有不同的特性，因此在每种情况下的电缆需要稍有不同的规格要求。

本文档提供了不同信号协议的布线具体指导，以及多个无人机硬件厂商使用的[颜色编码](#电缆颜色编码)。

### I2C线缆

[I2C总线](https://en.wikipedia.org/wiki/I%C2%B2C) 广泛用于连接传感器。  
多个厂商的线缆颜色标准如下表所示。

| 信号 | Pixhawk颜色               | ThunderFly颜色            | CUAV颜色 (I2C/CAN)       |
| ---- | ------------------------- | ------------------------- | ------------------------- |
| +5V  | ![红色][rcircle] 红色     | ![红色][rcircle] 红色     | ![红色][rcircle] 红色     |
| SCL  | ![黑色][blkcircle] 黑色   | ![黄色][ycircle] 黄色     | ![白色][wcircle] 白色     |
| SDA  | ![黑色][blkcircle] 黑色   | ![绿色][gcircle] 绿色     | ![黄色][ycircle] 黄色     |
| GND  | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   |

[Dronecode标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 假设自动驾驶仪的SDA和SCL信号上使用1.5k欧姆的上拉电阻。

#### 电缆绞合

通过适当绞合电缆导线，可以显著改善I²C总线信号串扰和电磁兼容性。  
[Twisted pairs](https://en.wikipedia.org/wiki/Twisted_pair) 对传感器布线尤其重要。  

- 每30厘米电缆长度，每对SCL/+5V和SDA/GND各绞合10圈。  
  ![I²C JST-GH cable](../../assets/hardware/cables/i2c_jst-gh_cable.jpg)  
- 每30厘米电缆长度，两对导线共同绞合4圈。  
  ![I²C JST-GH connector detail](../../assets/hardware/cables/i2c_jst-gh_connector.jpg)  

当使用适当的绞合对电缆时，I²C总线通常适用于亚米级机体。  
对于大型机体，通常更可靠的是使用CAN或其他基于差分信号的接口。  

::: info  
此绞合/电缆长度建议已成功用于I2C传感器，包括 [ThunderFly TFSLOT 空速传感器](../sensor/airspeed_tfslot.md) 和 [TFRPM01 转速计](../sensor/thunderfly_tachometer.md)。  
:::

#### 上拉电阻

I2C总线的所有末端都需要上拉电阻。
它同时起到[信号端接](https://en.wikipedia.org/wiki/Electrical_termination)的作用以及生成总线空闲信号。

有时需要使用示波器测量来检查上拉电阻的正确值。
I2C总线上的信号应具有清晰锐利的矩形边缘，幅度为几伏。
如果信号幅度较低，说明上拉电阻值过小，应减小阻值。
如果信号边缘呈圆弧状，说明上拉电阻值过大。

### UAVCAN 电缆

| 信号 | Pixhawk                   | ThunderFly                | Zubax                     | CUAV (I2C/CAN)            |
|------|-------------------------|-------------------------|-------------------------|-------------------------|
| +5V  | ![red][rcircle] 红色      | ![red][rcircle] 红色      | ![red][rcircle] 红色      | ![red][rcircle] 红色      |
| CAN_H | ![black][blkcircle] 黑色 | ![white][wcircle] 白色    | ![white][wcircle] 白色    | ![white][wcircle] 白色    |
| CAN_L | ![black][blkcircle] 黑色 | ![yellow][ycircle] 黄色   | ![yellow][ycircle] 黄色   | ![yellow][ycircle] 黄色   |
| GND  | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色  | ![black][blkcircle] 黑色  | ![black][blkcircle] 黑色  |

#### 电缆扭绞

CAN电缆也应进行扭绞，原因与I2C电缆相同。  
对于CAN电缆，推荐的扭绞方式为：

- 每30厘米电缆长度，每对GND/+5V和CAN_L/CAN_H各扭绞10次。  
  ![CAN JST-GH电缆](../../assets/hardware/cables/can_jst-gh_cable.jpg)  
- 每30厘米电缆长度，两对电缆一起扭绞4次。

### SPI

[SPI](https://en.wikipedia.org/wiki/Serial_Peripheral_Interface) 是一种同步串行通信接口，用于连接速度更快的传感器和设备。  
此协议通常用于连接 [光流](../sensor/optical_flow.md) 传感器或特殊遥测调制解调器。

| 信号   | Pixhawk 颜色             | ThunderFly 颜色          |
| ------ | ------------------------- | ------------------------- |
| +5V    | ![红色][rcircle] 红色     | ![红色][rcircle] 红色     |
| SCK    | ![黑色][blkcircle] 黑色   | ![黄色][ycircle] 黄色     |
| MISO   | ![黑色][blkcircle] 黑色   | ![蓝色][bluecircle] 蓝色  |
| MOSI   | ![黑色][blkcircle] 黑色   | ![绿色][gcircle] 绿色     |
| CS1    | ![黑色][blkcircle] 黑色   | ![白色][wcircle] 白色     |
| CS2    | ![黑色][blkcircle] 黑色   | ![蓝色][bluecircle] 蓝色  |
| GND    | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   |

### UART

UART用于将外设连接到自动驾驶仪。默认情况下，UART不支持网络功能，因此它直接连接两个设备。它通常用于连接自动驾驶仪和[无线电调制解调器](../telemetry/index.md)。

CTS和RTS是用于指示TX/RX引脚正在传输数据的信号。这种握手机制提高了数据传输的可靠性。当设备未使用该功能时，CTS和RTS信号引脚可能保持未连接状态。

连接电缆是非交叉的。因此，只需要用直连电缆将自动驾驶仪和外设连接起来。设备必须通过内部交换RX/TX和RTS/CTS引脚来实现交叉接线。

| 信号   | Pixhawk颜色             | ThunderFly颜色          |
| ------ | ------------------------- | ------------------------- |
| +5V    | ![red][rcircle] 红色       | ![red][rcircle] 红色       |
| TX     | ![black][blkcircle] 黑色 | ![white][wcircle] 白色   |
| RX     | ![black][blkcircle] 黑色 | ![green][gcircle] 绿色   |
| CTS    | ![black][blkcircle] 黑色 | ![blue][bluecircle] 蓝色  |
| RTS    | ![black][blkcircle] 黑色 | ![yellow][ycircle] 黄色 |
| GND    | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 |

UART信号是低频电磁干扰的常见来源，因此电缆长度应尽可能缩短。UART电缆不需要进行绞线处理。

### GPS（UART）与安全开关

[GPS接收机和磁力计](../gps_compass/index.md) 对电磁干扰（EMI）非常敏感。
因此它们应远离射频源（高压电缆、电调、无线电模块及其天线）安装。
如果布线设计不当，这种措施可能仍然不足。

| 信号        | Pixhawk 接线颜色             | ThunderFly 接线颜色          |
| ------------- | ------------------------- | ------------------------- |
| +5V           | ![红色][rcircle] 红色       | ![红色][rcircle] 红色       |
| TX            | ![黑色][blkcircle] 黑色   | ![白色][wcircle] 白色     |
| RX            | ![黑色][blkcircle] 黑色   | ![绿色][gcircle] 绿色     |
| SCL           | ![黑色][blkcircle] 黑色   | ![黄色][ycircle] 黄色     |
| SDA           | ![黑色][blkcircle] 黑色   | ![绿色][gcircle] 绿色     |
| SAFETY_SW     | ![黑色][blkcircle] 黑色   | ![白色][wcircle] 白色     |
| SAFETY_SW_LED | ![黑色][blkcircle] 黑色   | ![蓝色][bluecircle] 蓝色  |
| +3v3          | ![黑色][blkcircle] 黑色   | ![红色][rcircle] 红色     |
| BUZZER        | ![黑色][blkcircle] 黑色   | ![蓝色][bluecircle] 蓝色  |
| GND           | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   |

### GPS

| 信号 | Pixhawk颜色             | ThunderFly颜色          |
| ---- | ------------------------ | ------------------------ |
| +5V  | ![red][rcircle] 红       | ![red][rcircle] 红       |
| TX   | ![black][blkcircle] 黑   | ![white][wcircle] 白     |
| RX   | ![black][blkcircle] 黑   | ![green][gcircle] 绿     |
| SCL  | ![black][blkcircle] 黑   | ![yellow][ycircle] 黄    |
| SDA  | ![black][blkcircle] 黑   | ![green][gcircle] 绿     |
| GND  | ![black][blkcircle] 黑   | ![black][blkcircle] 黑   |

GPS线缆连接到UART和I2C总线。由于UART无法绞线，因此应尽可能缩短线缆长度。

### 模拟信号（电源模块）

| 信号       | Pixhawk 颜色             | ThunderFly 颜色          | CUAV 颜色                |
| ---------- | ------------------------- | ------------------------- | ------------------------- |
| VCC        | ![红色][rcircle] 红色     | ![红色][rcircle] 红色     | ![红色][rcircle] 红色     |
| VCC        | ![黑色][blkcircle] 黑色   | ![红色][rcircle] 红色     | ![红色][rcircle] 红色     |
| CURRENT    | ![黑色][blkcircle] 黑色   | ![白色][wcircle] 白色     | ![白色][wcircle] 白色     |
| VOLTAGE    | ![黑色][blkcircle] 黑色   | ![黄色][ycircle] 黄色     | ![黄色][ycircle] 黄色     |
| GND        | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   |
| GND        | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   | ![黑色][blkcircle] 黑色   |

此接头是高功率和低电压信号混合使用的示例。  
不幸的是，绞线技术仅适用于高功率GND和VCC线路。  
这对自动驾驶仪读取模拟信号时的噪声干扰帮助不大。

### 安全

| 信号          | Pixhawk 颜色             | ThunderFly 颜色          |
| ------------- | ------------------------- | ------------------------- |
| SAFE_VCC      | ![红][rcircle] 红         | ![红][rcircle] 红         |
| SAFETY_SW_LED | ![黑][blkcircle] 黑       | ![蓝][bluecircle] 蓝      |
| SAFETY_SW     | ![黑][blkcircle] 黑       | ![白][wcircle] 白         |
| BUZZER        | ![黑][blkcircle] 黑       | ![蓝][bluecircle] 蓝      |
| +5V           | ![黑][blkcircle] 黑       | ![红][rcircle] 红         |
| GND           | ![黑][blkcircle] 黑       | ![黑][blkcircle] 黑       |

## 高功率布线

对于高功率布线，最重要的设计准则是选择合适的线径，以允许足够的电流流通。  
通用的横截面积要求为每8A电流对应1 mm²的线径。

虽然实际操作中很少见，但将正负极导线绞合在一起是有益的。

高功率线缆的EMI会对磁力计产生显著影响。  
因此，高功率线缆与导航磁力计之间始终需要保持较大的分离距离。

### 电缆颜色编码

大多数制造商将红色用于高压线，黑色用于地线。其他颜色的使用由制造商自行决定。[Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)仅要求电压公共集电极（VCC）引脚/电缆为红色。

对信号线进行颜色编码有助于识别特定电缆，使无人机组装更加便捷。

一种便于电缆识别的颜色编码方案应遵循以下规则：

- 红色和黑色专用于电源。
- 相同类型的信号应使用相同颜色。
- 相邻导线的颜色在连接器中不应重复。
- 相同引脚数的线束必须具有唯一的颜色序列。
  这决定了电缆类型。
  （这对使用在手册中的照片特别有用。）

遵循这些规则设计的电缆颜色示例如下：

| 颜色               | 名称   | 推荐用途                             |
| ------------------- | ------ | ------------------------------------------- |
| ![red][rcircle]     | 红色   | 电源电压                               |
| ![green][gcircle]   | 绿色   | 通用信号                                 |
| ![white][wcircle]   | 白色   | 通用信号                                 |
| ![yellow][ycircle]  | 黄色   | 通用信号                                 |
| ![blue][bluecircle] | 蓝色   | 电源返回，开集电极控制信号               |
| ![black][blkcircle] | 黑色   | GND，电源返回地线                        |

[ycircle]: ../../assets/hardware/cables/yellow.png
[rcircle]: ../../assets/hardware/cables/red.png
[gcircle]: ../../assets/hardware/cables/green.png
[wcircle]: ../../assets/hardware/cables/white.png
[bluecircle]: ../../assets/hardware/cables/blue.png
[blkcircle]: ../../assets/hardware/cables/black.png

::: info
上述规则由Thunderfly提供，并应用于其电缆设计。

Thunderfly和其他部分厂商的电缆颜色编码详见下文章节。
引脚标签对应自动驾驶仪侧的引脚分配。
所有电缆均为直连（1:1）。
若需要交叉连接（例如UART），应通过设备内部连接解决。
:::