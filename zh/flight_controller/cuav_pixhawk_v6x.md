# CUAV Pixhawk V6X

:::warning
PX4不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.cuav.net/)。
:::

_Pixhawk V6X_<sup>&reg;</sup> 是Pixhawk®飞行控制器系列的最新更新版本，由CUAV<sup>&reg;</sup>和PX4团队合作设计和制造。

该产品基于以下标准：
- [Pixhawk® Autopilot FMUv6X 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf)
- [Autopilot Bus 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)
- [Connector 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)

![Pixhawk V6X](../../assets/flight_controller/cuav_pixhawk_v6x/pixhawk_v6x.jpg)

:::tip
此自动驾驶仪获得PX4维护和测试团队的[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

Pixhawk<sup>&reg;</sup> V6X在性能、稳定性和可靠性方面提供了终极解决方案。

- Arm® Cortex®-M7处理器（STM32H753）带浮点运算单元（FPU），480MHz高速运算和2MB闪存
  开发者可实现更高效率，支持更复杂的算法和模型
- 基于FMUv6X开放标准的高性能低噪声IMU和汽车级磁力计
  实现更优的稳定性和抗干扰能力
- 三重冗余IMU和双冗余气压计，通过独立总线连接
  当PX4自动驾驶仪检测到传感器故障时，系统可无缝切换到备用传感器以保持飞行控制可靠性
- 每组传感器由独立LDO供电并具备独立电源控制
  振动隔离系统可过滤高频振动并降低噪声，确保读数精度，提升机体整体飞行性能
- 外部传感器总线（SPI5）包含两条芯片选择线和数据就绪信号，支持SPI接口的额外传感器和载荷
- 集成Microchip以太网PHY芯片，实现与任务计算机等设备的高速以太网通信
- 新设计的振动隔离系统可过滤高频振动并降低噪声，确保读数精度
- IMU通过板载加热电阻进行温度控制，实现最佳工作温度
- 模块化飞行控制器：分离的IMU、FMU和基板通过100针和50针Pixhawk®自动驾驶仪总线连接

Pixhawk® V6X适用于企业研究实验室、学术研究和商业应用。

### 处理器与传感器

- FMU处理器：STM32H753
  - 32位Arm® Cortex®-M7，480MHz，2MB闪存，1MB RAM
- IO处理器：STM32F103
  - 32位Arm® Cortex®-M3，72MHz，20KB SRAM
- 板载传感器
  - 加速度计/陀螺仪：BMI088
  - 加速度计/陀螺仪：ICM-42688-P
  - 加速度计/陀螺仪：ICM-20649
  - 磁力计：RM3100
  - 气压计：2x ICP-20100

### 电气参数

- 电压规格：
  - 最大输入电压：5.7V
  - USB供电输入：4.75～5.25V
  - 伺服总线输入：0～9.9V
- 电流规格：
  - TELEM1和GPS2组合输出电流限制器：1.5A
  - 其他所有端口组合输出电流限制器：1.5A

### 接口

- 16路PWM伺服输出
- 1路专用R/C输入（支持Spektrum/DSM和S.Bus）带模拟/PWM RSSI输入
- 3个TELEM端口（带全流量控制）
- 1个UART4（串口和I2C）
- 2个GPS端口
  - 1个完整GPS+安全开关端口（GPS1）
  - 1个基础GPS端口（带I2C，GPS2）
- 2个USB端口
  - 1个TYPE-C
  - JST GH1.25
- 1个以太网端口
  - 无变压器应用
  - 100Mbps
- 1个SPI总线
  - 2条芯片选择线
  - 2条数据就绪线
  - 1条SPI同步线
  - 1条SPI复位线
- 2个CAN总线（用于CAN外设）
  - CAN总线支持独立静默控制或电调接收器复用控制
- 4个电源输入端口
  - 2个Dronecan/UAVCAN电源输入
  - 2个SMBUS/I2C电源输入
- 1个AD与IO端口
  - 2个额外模拟输入（3.3V和6.6V）
  - 1个PWM/捕获输入
- 2个专用调试端口
  - FMU调试
  - IO调试

### 机械参数

- 重量
  - 飞行控制器模块：99g
  - 核心模块：43g
  - 底板：56g
- 工作与存储温度：-20～85°C
- 尺寸

  - 飞行控制器

    ![Pixhawk V6X](../../assets/flight_controller/cuav_pixhawk_v6x/v6x_size.jpg)

  - 核心模块

    ![Pixhawk V6X](../../assets/flight_controller/cuav_pixhawk_v6x/core.png)

## 购买途径

从 [CUAV](https://store.cuav.net/) 下单购买。

## 组装/设置

[Pixhawk V6X 接线快速入门](../assembly/quick_start_cuav_pixhawk_v6x.md)提供了如何组装必需/重要外设（如GPS、电源模块等）的指导说明。

## 引脚分配

![Pixhawk V6x引脚分配图](../../assets/flight_controller/cuav_pixhawk_v6x/pixhawk_v6x_pinouts.png)

说明：

- [相机捕获引脚](../camera/fc_connected_camera.md#camera-capture-configuration)（`PI0`）位于AD&IO端口的第2针脚，上方标记为`FMU_CAP1`。

## 串口映射

| UART   | 设备         | 端口           |
| ------ | ------------ | -------------- |
| USART1 | /dev/ttyS0   | GPS            |
| USART2 | /dev/ttyS1   | TELEM3         |
| USART3 | /dev/ttyS2   | 调试控制台     |
| UART4  | /dev/ttyS3   | UART4          |
| UART5  | /dev/ttyS4   | TELEM2         |
| USART6 | /dev/ttyS5   | PX4IO/RC       |
| UART7  | /dev/ttyS6   | TELEM1         |
| UART8  | /dev/ttyS7   | GPS2           |## 电压规格

_Pixhawk V6X_ 可通过三个电源输入实现供电冗余功能。三个电源通道分别为：**POWERC1/POWER1**、**POWERC2/POWER2** 和 **USB**。

- **POWER C1** 和 **POWER C2** 是 DroneCAN/UAVCAN 电池接口（推荐）；**POWER1** 和 **POWER2** 是 SMbus/I2C 电池接口（备用）
- **POWER C1** 和 **POWER1** 共用同一电源开关，**POWER C2** 和 **POWER2** 共用同一电源开关

**正常操作最大额定值**

系统将按以下优先级顺序使用所有可用电源供电：

1. **POWER C1**、**POWER C2**、**POWER1** 和 **POWER2** 输入（4.75V 至 5.7V）
2. **USB** 输入（4.75V 至 5.25V）

**绝对最大额定值**

在以下条件下系统将完全断电（不工作），但硬件保持完好：

1. **POWER1** 和 **POWER2** 输入（工作范围 4.7V 至 5.7V，0V 至 10V 不损坏）
2. **USB 输入**（工作范围 4.7V 至 5.7V，0V 至 6V 不损坏）
3. **舵机输入**：**FMU PWM OUT** 和 **I/O PWM OUT** 的 `VDD_SERVO` 引脚（0V 至 42V 不损坏）

**电压监控**

数字 DroneCAN/UAVCAN 电池监控功能默认启用（详见 [快速入门 > 电源](../assembly/quick_start_cuav_pixhawk_v6x.md#power)）。

::: info
通过 ADC 实现的模拟电池监控不支持该主板，但可能在不同底板版本的飞控中支持
:::

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接了合适的硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要[构建 PX4](../dev_setup/building_px4.md)以针对此目标：

```
make px4_fmu-v6x_default
```

<a id="debug_port"></a>## 调试端口

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)运行在 **FMU调试** 端口上。

该引脚定义和连接器符合[Pixhawk调试完整](../debug/swd_debug.md#pixhawk-debug-full)接口标准，该标准定义在[Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)中（JST SM10B连接器）。

| 引脚       | 信号           | 电压   |
| ---------- | -------------- | ------ |
| 1 (红色)   | `Vtref`        | +3.3V  |
| 2 (黑色)   | 控制台TX (OUT) | +3.3V  |
| 3 (黑色)   | 控制台RX (IN)  | +3.3V  |
| 4 (黑色)   | `SWDIO`        | +3.3V  |
| 5 (黑色)   | `SWCLK`        | +3.3V  |
| 6 (黑色)   | `SWO`          | +3.3V  |
| 7 (黑色)   | NFC GPIO       | +3.3V  |
| 8 (黑色)   | PH11           | +3.3V  |
| 9 (黑色)   | nRST           | +3.3V  |
| 10 (黑色)  | `GND`          | 地     |

关于该端口的接线和使用信息请参见：

- [PX4系统控制台](../debug/system_console.md#pixhawk_debug_port)（注意，FMU控制台映射到USART3）。
- [SWD调试端口](../debug/swd_debug.md)

## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台/机型

任何可以通过普通遥控舵机或Futaba S-Bus舵机控制的多旋翼/固定翼飞机/地面车辆或船只。
完整的支持配置列表请参见[Airframes Reference](../airframes/airframe_reference.md)。

## 更多信息

- [CUAV 文档](https://doc.cuav.net/) (CUAV)
- [Pixhawk V6X 接线快速入门](../assembly/quick_start_cuav_pixhawk_v6x.md)
- [Pixhawk 自主飞行器 FMUv6X 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf)
- [Pixhawk 自主飞行器总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)
- [Pixhawk 接插件标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)