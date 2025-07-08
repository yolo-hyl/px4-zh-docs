# Holybro Pixhawk 4 Mini（已停产）

:::warning
PX4 不生产此（或任何）自动驾驶仪。
如有硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

_Pixhawk<sup>&reg;</sup> 4 Mini_自动驾驶仪专为希望使用_Pixhawk 4_强大功能但受限于小型无人机的工程师和爱好者设计。  
_Pixhawk 4 Mini_ 从_Pixhawk 4_中提取FMU处理器和内存资源，同时移除通常未使用的接口。  
这使得_Pixhawk 4 Mini_ 的体积足够小，可适用于250mm竞速无人机。

_Pixhawk 4 Mini_ 由Holybro<sup>&reg;</sup>和Auterion<sup>&reg;</sup>合作设计开发。  
它基于[Pixhawk](https://pixhawk.org/) **FMUv5**设计标准，并针对运行PX4飞行控制软件进行了优化。

![Pixhawk4 mini](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_iso_1.png)

:::tip
PX4维护和测试团队[支持](../flight_controller/autopilot_pixhawk_standard.md)此自动驾驶仪。
:::

## 快速摘要

- 主FMU处理器：STM32F765  
  - 32位Arm® Cortex®-M7，216MHz，2MB内存，512KB RAM  
- 机载传感器：  
  - 加速度计/陀螺仪：ICM-20689  
  - 加速度计/陀螺仪：BMI055 或 ICM20602  
  - 磁力计：IST8310  
  - 气压计：MS5611  
- GPS：u-blox Neo-M8N GPS/GLONASS接收器；集成磁力计IST8310  
- 接口：  
  - 8路PWM输出  
  - FMU上4路专用PWM/捕获输入  
  - 专用CPPM遥控输入  
  - 专用Spektrum/DSM和S.Bus遥控输入（含模拟/PWM RSSI输入）  
  - 3个通用串口  
  - 2个I2C端口  
  - 3个SPI总线  
  - 1个CAN总线用于CAN电调  
  - 电池电压/电流模拟输入  
  - 2个额外模拟输入  
- 电源系统：  
  - 电源模块输入：4.75~5.5V  
  - USB电源输入：4.75~5.25V  
  - 舵机电源输入：0~24V  
  - 最大电流检测：120A  
- 重量和尺寸：  
  - 重量：37.2g  
  - 尺寸：38x55x15.5mm  
- 其他特性：  
  - 工作温度：-40 ~ 85°c  

更多信息请参阅 [_Pixhawk 4 Mini_ 技术数据表](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4mini/pixhawk4mini_technical_data_sheet.pdf)。

## 购买渠道

从[Holybro](https://holybro.com/collections/autopilot-flight-controllers/products/pixhawk4-mini)订购。

## 接口

![Pixhawk 4 Mini 接口](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_interfaces.png)

:::warning
**RC IN**和**PPM**端口仅适用于遥控接收机。这些端口带电！**切勿**连接舵机、电源或电池（或连接到任何接收机）。
:::

## 引脚分配

从[这里](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4mini/pixhawk4mini_pinouts.pdf)下载_Pixhawk 4 Mini_引脚分配。

## 尺寸

![Pixhawk 4 Mini 尺寸](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_dimensions.png)

## 电压规格

_Pixhawk 4 Mini_ 可通过双电源实现冗余供电。电源轨道为：**POWER**和**USB**。

::: info
**MAIN OUT**的输出电源轨道**不**为飞控板供电（且不受其供电）。  
您必须[供电](../assembly/quick_start_pixhawk4_mini.md#power)给**POWER**或**USB**之一，否则板载设备将无电。
:::

**正常操作最大电压**

在此条件下，系统将按以下顺序使用所有电源供电：

1. **POWER** (4.75V 到 5.5V)  
1. **USB** 输入 (4.75V 到 5.25V)  

**最大电压**

- 电源模块输入：4.75~5.5V  
- USB电源输入：4.75~5.25V  
- 舵机电源输入：0~24V  

**最大电流检测**：120A  

## 组装与设置

- 重量：37.2g  
- 尺寸：38x55x15.5mm  
- 工作温度：-40 ~ 85°c  

## 固件构建

使用以下命令构建固件：  
```
make px4_fmu-v5_default
```

## 调试端口

调试端口涉及硬件连接，需保留英文术语如“FMUv5”等。

## 串口映射

| 设备       | 描述          |
|------------|---------------|
| GPS1       | GPS1          |
| ...        | ...           |

## 外设

- [数字空速传感器](https://example.com)  
- [技术数据表](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4mini/pixhawk4mini_technical_data_sheet.pdf)  

## 支持的平台

:::warning
_Pixhawk 4 Mini_ 没有AUX端口。  
该板无法用于需要超过8个端口或使用AUX端口进行电机或舵面控制的机架。  
可用于通过AUX连接非关键外设（例如“RC AUX1通道直通”）的机架。
:::

## 进一步信息

- [_Pixhawk 4 Mini_ 技术数据表](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4mini/pixhawk4mini_technical_data_sheet.pdf)  
- [FMUv5参考设计引脚分配](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165)