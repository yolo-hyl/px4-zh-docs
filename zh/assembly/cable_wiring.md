# 电缆布线基础

电缆是[电磁干扰 (EMI)](https://en.wikipedia.org/wiki/Electromagnetic_interference)的常见来源，可能导致飞行路径异常、"toilet bowling"现象以及整体飞行质量下降。通过在无人机中使用合适的布线方案可以避免这些问题。

设计无人机电缆时应牢记以下基本概念：

- 高功率电缆和信号电缆应尽可能分开布线。
- 电缆长度应保持最短以方便线缆组件的处理操作。电线张力应足够以承受机体可能的变形（甚至硬着陆时）
  （电线不应是第一个断裂的部件）。
- 应避免通过电缆环路来减少冗余长度 - 使用更短的线缆！
- 对于数字信号，可以通过降低波特率来减少辐射能量并提高数据传输的可靠性。
  这意味着在不需要高数据率时，可以使用更长的电缆。

## 信号接线

信号协议具有不同的特性，因此每种情况下使用的电缆需要稍有不同的规格。

本主题提供不同信号协议的布线指南，以及多个无人机硬件厂商使用的[cable-colour-coding](#cable-colour-coding)颜色编码。

### I2C 电缆

[I2C总线](https://en.wikipedia.org/wiki/I%C2%B2C)广泛用于连接传感器。
不同厂商的电缆颜色在下表中有具体说明。

| 信号 | Pixhawk 颜色            | ThunderFly 颜色         | CUAV 颜色（I2C/CAN）     |
| ---- | ----------------------- | ----------------------- | ------------------------ |
| +5V  | ![red][rcircle] 红色    | ![red][rcircle] 红色    | ![red][rcircle] 红色    |
| SCL  | ![black][blkcircle] 黑色| ![yellow][ycircle] 黄色 | ![white][wcircle] 白色  |
| SDA  | ![black][blkcircle] 黑色| ![green][gcircle] 绿色  | ![yellow][ycircle] 黄色 |
| GND  | ![black][blkcircle] 黑色| ![black][blkcircle] 黑色| ![black][blkcircle] 黑色|

[Dronecode标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)规定自动驾驶仪中SDA和SCL信号需要1.5k欧姆的上拉电阻。

#### 电缆绞合

通过适当绞合电缆线，可以显著改善I2C总线的信号串扰和电磁兼容性。
[Twisted pairs](https://en.wikipedia.org/wiki/Twisted_pair)对传感器信号尤为重要。
30厘米以下的短电缆可减少干扰。

| 接线要求 | 具体说明 |
|---------|---------|
| 双绞线 | SDA和SCL必须双绞 |
| 线径   | 0.5mm²以上铜芯线 |
| 绝缘层 | 聚氯乙烯（PVC） |

#### 上拉电阻

| 测量步骤 | 操作说明 |
|--------|--------|
| 1. 准备工具 | 示波器、万用表 |
| 2. 连接测试点 | SDA/SCL引脚 |
| 3. 测量电压 | 3.3V±5% |
| 4. 检查波形 | 上升沿时间<100ns |

### UAVCAN 电缆

| 信号 | Pixhawk 颜色 | ThunderFly 颜色 | CUAV 颜色 |
| ---- |------------|----------------|----------|
| CAN_H | ![green][gcircle] 绿色 | ![blue][bcircle] 蓝色 | ![green][gcircle] 绿色 |
| CAN_L | ![yellow][ycircle] 黄色 | ![green][gcircle] 绿色 | ![yellow][ycircle] 黄色 |
| GND  | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 |

### SPI 电缆

| 信号 | Pixhawk 颜色 | ThunderFly 颜色 | CUAV 颜色 |
| ---- |------------|----------------|----------|
| SCK  | ![white][wcircle] 白色 | ![orange][ocircle] 橙色 | ![white][wcircle] 白色 |
| MOSI | ![yellow][ycircle] 黄色 | ![white][wcircle] 白色 | ![yellow][ycircle] 黄色 |
| MISO | ![green][gcircle] 绿色  | ![green][gcircle] 绿色  | ![green][gcircle] 绿色  |
| CS   | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 |

### UART 电缆

| 信号 | Pixhawk 颜色 | ThunderFly 颜色 | CUAV 颜色 |
| ---- |------------|----------------|----------|
| TX   | ![green][gcircle] 绿色  | ![white][wcircle] 白色 | ![green][gcircle] 绿色  |
| RX   | ![yellow][ycircle] 黄色 | ![green][gcircle] 绿色  | ![yellow][ycircle] 黄色 |
| CTS  | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 |
| RTS  | ![red][rcircle] 红色    | ![red][rcircle] 红色    | ![red][rcircle] 红色    |

### GPS(UART) & 安全信号

| 信号         | Pixhawk 颜色 | ThunderFly 颜色 | CUAV 颜色 |
|------------|-------------|----------------|----------|
| SAFE_VCC   | ![red][rcircle] 红色    | ![red][rcircle] 红色    | ![red][rcircle] 红色    |
| 安全开关LED | ![black][blkcircle] 黑色 | ![blue][bcircle] 蓝色  | ![blue][bcircle] 蓝色  |
| 安全开关    | ![black][blkcircle] 黑色 | ![white][wcircle] 白色 | ![white][wcircle] 白色 |
| 蜂鸣器      | ![black][blkcircle] 黑色 | ![blue][bcircle] 蓝色  | ![blue][bcircle] 蓝色  |
| +5V        | ![black][blkcircle] 黑色 | ![red][rcircle] 红色    | ![red][rcircle] 红色    |
| GND        | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 |

### 模拟信号（电调模块）

| 信号     | Pixhawk 颜色 | ThunderFly 颜色 | CUAV 颜色 |
|--------|-------------|----------------|----------|
| VCC    | ![red][rcircle] 红色    | ![red][rcircle] 红色    | ![red][rcircle] 红色    |
| 电流输入 | ![black][blkcircle] 黑色 | ![white][wcircle] 白色 | ![white][wcircle] 白色 |
| 电压输入 | ![black][blkcircle] 黑色 | ![yellow][ycircle] 黄色 | ![yellow][ycircle] 黄色 |
| GND    | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 | ![black][blkcircle] 黑色 |## 高功率接线

对于高功率接线，最重要的设计标准是选择合适的电线厚度，以确保足够的电流通过。一般横截面要求为每8A电流对应1 mm²的截面积。

虽然很少实际应用，但将正负极电线扭绞在一起是有益的。

高功率线缆的电磁干扰（EMI）会对磁力计产生显著影响。因此，必须始终确保高功率线缆与导航磁力计之间保持较大间距。

### 电缆颜色编码

大多数制造商使用红色表示高压线路，黑色表示地线。其他颜色由制造商自行决定。[Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)仅规定电压公共集电极（VCC）引脚/电缆必须为红色。

对信号线进行颜色编码有助于识别特定电缆，使无人机的组装更加便捷。

一种便于电缆识别的颜色编码方案可遵循以下规则：

- 红色和黑色专用于电源。
- 相同类型的信号应使用相同颜色。
- 相邻导线的颜色在连接器中不应重复。
- 相同引脚数量的线束必须具有唯一的颜色序列（这决定了电缆类型。这对手册中使用的照片特别有用）。

符合上述规则的电缆颜色编码示例如下：

| 颜色               | 名称   | 推荐用途                             |
| ------------------- | ------ | ------------------------------------------- |
| ![red][rcircle]     | 红色    | 电源电压                               |
| ![green][gcircle]   | 绿色  | 通用信号                      |
| ![white][wcircle]   | 白色  | 通用信号                      |
| ![yellow][ycircle]  | 黄色 | 通用信号                      |
| ![blue][bluecircle] | 蓝色   | 电源回路、开集控制信号 |
| ![black][blkcircle] | 黑色  | 地线、电源回路地线                    |

<!-- 图片源引用说明
此方法允许更紧凑的markdown格式 -->

[ycircle]: ../../assets/hardware/cables/yellow.png
[rcircle]: ../../assets/hardware/cables/red.png
[gcircle]: ../../assets/hardware/cables/green.png
[wcircle]: ../../assets/hardware/cables/white.png
[bluecircle]: ../../assets/hardware/cables/blue.png
[blkcircle]: ../../assets/hardware/cables/black.png

::: info
上述规则由Thunderfly提供，并应用于其电缆设计。

Thunderfly及其他部分厂商的电缆颜色编码方案详见下文。引脚标签对应自动驾驶仪侧的引脚分配。所有电缆均为直连（1:1）。若需要交叉连接（如UART），应通过设备的内部连接实现。
:::