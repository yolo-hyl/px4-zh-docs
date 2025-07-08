# ThePeach FCC-R1

:::warning
PX4不生产此（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://thepeach.kr/)。
:::

**ThePeach FCC-R1** 是由 **ThePeach** 设计制造的先进自动驾驶仪。  
该设备基于 **Pixhawk-project FMUv3** 开源硬件设计，运行 **PX4** 操作系统于 **Nuttx OS** 平台。

![ThePeach_R1](../../assets/flight_controller/thepeach_r1/main.png)

## 规格参数

- **主处理器**：STM32F427VIT6  
  - 32位 ARM Cortex-M4，168 MHz，256 KB RAM，2 MB Flash 存储

- **IO处理器**：STM32F100C8T6  
  - 32位 ARM Cortex-M3，24 MHz，8 KB SRAM

- **板载传感器**  
  - 加速度计/陀螺仪：ICM-20602  
  - 加速度计/陀螺仪/磁力计：MPU-9250  
  - 气压计：MS5611

- **接口**  
  - 8+6 PWM 输出（8来自IO，6来自FMU）  
  - Spektrum DSM / DSM2 / DSM-X 卫星兼容输入  
  - Futaba S.BUS 兼容输入和输出  
  - PPM 信号输入  
  - 模拟/PWM RSSI 输入  
  - S.Bus 舵机输出  
  - 安全开关/LED  
  - 4x UART：TELEM1, TELEM2（Raspberry Pi CM3+）, GPS, SERIAL4  
  - 1x I2C 接口  
  - 1x CAN 总线  
  - 1节电池电压/电流模拟输入

- **Raspberry Pi CM3+ 接口**  
  - VBUS  
  - DDR2 连接器：Raspberry Pi CM3+  
  - 1x UART  
  - 2x USB  
  - 1x Raspberry Pi 摄像头接口

- **机械规格**  
  - 尺寸：49.2 x 101 x 18.2 mm  
  - 重量：100 g

## 连接器

![pinmap_top](../../assets/flight_controller/thepeach_r1/pinmap.png)

## 串口映射

| UART   | 设备        | 端口                         |
| ------ | ----------- | ---------------------------- |
| USART1 | /dev/ttyS0  | IO处理器调试                 |
| USART2 | /dev/ttyS1  | TELEM1（流量控制）           |
| USART3 | /dev/ttyS2  | TELEM2（Raspberry Pi CM3+）  |
| UART4  | /dev/ttyS3  | GPS1                         |
| USART6 | /dev/ttyS4  | PX4IO                        |
| UART7  | /dev/ttys5  | 调试控制台                   |
| UART8  | /dev/ttyS6  | TELEM4                       |

## 电压规格

**ThePeach FCC-R1** 可在双电源输入时实现电源冗余。两个电源通道为：**POWER** 和 **USB**。

注意：
1. 输出电源通道 **FMU PWM OUT** 和 **I/O PWM OUT** 不为飞控板供电（也不受其供电）。必须为 **POWER** 或 **USB** 提供电源，否则整块板将无电。
2. USB 不为 **Raspberry Pi CM3+** 供电。必须为 **POWER** 提供电源，否则 Raspberry Pi CM3+ 将无电。

**正常运行最大电压**

在此条件下，系统将按以下顺序使用所有电源供电：
1. POWER 输入（5V 至 5.5V）
2. USB 输入（4.75V 至 5.25V）

**绝对最大电压**

在此条件下，所有电源输入都会导致飞控板永久损坏：
1. POWER 输入（5.5V 以上）
2. USB 输入（5.5V 以上）

## 固件构建

构建 PX4 固件的命令：

```jsx
make thepeach_r1_default
```

## 购买渠道

从 [ThePeach](http://thepeach.shop/) 购买