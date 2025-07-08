# ThePeach FCC-K1

:::warning
PX4不生产此（或任何）自动驾驶仪。
如需硬件支持或合规问题，请联系[制造商](https://thepeach.kr/)。
:::

**ThePeach FCC-K1** 是由 **ThePeach** 设计制造的先进自动驾驶仪。

其基于 **Pixhawk-project FMUv3** 开源硬件设计，并在 **Nuttx OS** 上运行 **PX4**。

![ThePeach FCC-K1](../../assets/flight_controller/thepeach_k1/main.png)

## 规格参数

- 主处理器: STM32F427VIT6

  - 32位ARM Cortex-M4，168 MHz，256 KB RAM，2 MB Flash存储

- IO处理器: STM32F100C8T6

  - ARM Cortex-M3，32位ARM Cortex-M3，24 MHz，8KB SRAM

- 板载传感器

  - 加速计/陀螺仪: ICM-20602
  - 加速计/陀螺仪/磁力计: MPU-9250
  - 气压计: MS5611

- 接口

  - 8+5 PWM输出（8来自IO，5来自FMU）
  - 支持Spektrum DSM/DSM2/DSM-X卫星接收机输入
  - 支持Futaba S.BUS输入输出
  - PPM总线输入
  - 模拟/PWM RSSI输入
  - S.Bus舵机输出
  - 安全开关/LED
  - 4个UART端口: TELEM1, TELEM2, GPS, SERIAL4
  - 2个I2C端口
  - 1个CAN总线
  - 1个ADC
  - 电池电压/电流模拟输入

- 机械参数
  - 尺寸: 40.2 x 61.1 x 24.8 mm
  - 重量: 65g

## 接口连接器

![pinmap_top](../../assets/flight_controller/thepeach_k1/pinmap_top.png)

![pinmap_bottom](../../assets/flight_controller/thepeach_k1/pinmap_bottom.png)

## 串口映射表

| UART   | 设备        | 接口                  |
| ------ | ----------- | --------------------- |
| USART1 | /dev/ttyS0  | IO处理器调试          |
| USART2 | /dev/ttyS1  | TELEM1（流量控制）    |
| USART3 | /dev/ttyS2  | TELEM2（流量控制）    |
| UART4  | /dev/ttyS3  | GPS1                  |
| USART6 | /dev/ttyS4  | PX4IO                 |
| UART7  | /dev/ttyS5  | 调试控制台            |
| UART8  | /dev/ttyS6  | TELEM4                |

## 电压规格

**ThePeach FCC-K1** 在提供两个电源输入时可实现双重冗余供电。
两个电源轨为：**POWER** 和 **USB**。

::: info
输出电源轨 **FMU PWM OUT** 和 **I/O PWM OUT** 不为飞控板供电（也不被其供电）。
必须为 **POWER** 或 **USB** 中的一个供电，否则电路板将无电。
:::

**常规操作最大电压**

在此条件下，系统将按以下顺序使用所有电源供电：

1. POWER输入（5V至5.5V）
2. USB输入（4.75V至5.25V）

**绝对最大电压**

在此条件下，所有电源输入都会永久损坏飞控板：

1. POWER输入（5.5V以上）
2. USB输入（5.5V以上）

## 固件编译

要为此目标编译PX4：

```jsx
make thepeach_k1_default
```

## 购买渠道

从 [ThePeach](http://thepeach.shop/) 订购