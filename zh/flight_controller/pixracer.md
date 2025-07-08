# mRo Pixracer

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.mrobotics.io/)。
:::

Pixhawk<sup>&reg;</sup> XRacer 板系列专为小型竞速四轴和飞机优化。
与 [Pixfalcon](../flight_controller/pixfalcon.md) 和 [Pixhawk](../flight_controller/pixhawk.md) 相比，它具有内置 WiFi、新型传感器、便捷的完整舵机接头、CAN 总线支持以及 2M 闪存。

<img src="../../assets/flight_controller/pixracer/pixracer_hero_grey.jpg" width="300px" title="pixracer + 8266 grey" />

:::tip
此自动驾驶仪的 [支持情况](../flight_controller/autopilot_pixhawk_standard.md) 由 PX4 维护和测试团队提供支持。
:::

## 核心功能

- 主系统芯片: [STM32F427VIT6 rev.3](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN1789)  
  - CPU: 180 MHz ARM Cortex<sup>&reg;</sup> M4 带单精度FPU  
  - RAM: 256 KB SRAM (L1)  
- 标准FPV尺寸: 36x36 mm，标准30.5 mm孔位  
- Invensense<sup>&reg;</sup> ICM-20608 加速度计/陀螺仪 (4 KHz) / MPU9250 加速度计/陀螺仪/磁力计 (4 KHz)  
- HMC5983 温度补偿磁力计  
- Measurement Specialties MS5611 气压计  
- JST GH连接器  
- microSD（日志记录）  
- Futaba S.BUS 和 S.BUS2 / Spektrum DSM2 和 DSMX / Graupner SUMD / PPM 输入 / Yuneec ST24  
- FrSky<sup>&reg;</sup> 遥测端口  
- OneShot PWM输出（可配置）  
- 可选：安全开关和蜂鸣器## 购买渠道

Pixracer 可通过 [mRobotics.io](https://store.mrobotics.io/mRo-PixRacer-R15-Official-p/m10023a.htm) 购买。

配件包括：

- [数字空速传感器](https://hobbyking.com/en_us/hkpilot-32-digital-air-speed-sensor-and-pitot-tube-set.html)
- [Hobbyking<sup>&reg;</sup> OSD + EU 遥测（433 MHz）](https://hobbyking.com/en_us/micro-hkpilot-telemetry-radio-module-with-on-screen-display-osd-unit-433mhz.html)

## 套件

Pixracer 设计为使用独立的航电电源供电。这是为了避免电机或电调产生的电流涌动倒灌到飞控系统，从而干扰其精密传感器。

- 电源模块（带电压和电流检测）
- I2C 分线器（支持 AUAV、Hobbyking 和 3DR® 外设）
- 所有常见外设的线缆套件## WiFi（无需 USB）

该开发板的主要功能之一是能够通过 WiFi 进行固件刷新、系统设置和飞行遥测。这使其摆脱了对任何桌面系统的依赖。

- [ESP8266 WiFi](../telemetry/esp8266_wifi_module.md)
- [Custom ESP8266 MAVLink firmware](https://github.com/dogmaphobic/mavesp8266)

::: info
固件升级功能尚未通过 WiFi 启用（默认引导程序已支持该功能，但尚未启用）。系统设置和遥测功能已支持。
:::

## 装配

请参见[Pixracer Wiring Quickstart](../assembly/quick_start_pixracer.md)

## 接线图

![Grau setup pixracer top](../../assets/flight_controller/pixracer/grau_setup_pixracer_top.jpg)

::: info
如果使用 `TELEM2` 作为外部遥测模块，则需要将其配置为 MAVLink 串口。  
更多详情请参阅: [Pixracer 接线快速入门 > 外部遥测](../assembly/quick_start_pixracer.md#external-telemetry)
:::

![Grau setup pixracer bottom](../../assets/flight_controller/pixracer/grau_setup_pixracer_bottom.jpg)

![setup pixracer GPS](../../assets/flight_controller/pixracer/grau_setup_pixracer_gps.jpg)

![Grau b Pixracer FrSkyS.Port Connection](../../assets/flight_controller/pixracer/grau_b_pixracer_frskys.port_connection.jpg)

![Grau ACSP4 2 roh](../../assets/flight_controller/pixracer/grau_acsp4_2_roh.jpg)

![Grau ACSP5 roh](../../assets/flight_controller/pixracer/grau_acsp5_roh.jpg)

## 连接器

所有连接器均遵循[Pixhawk连接器标准](https://pixhawk.org/pixhawk-connector-standard/)。  
除非另有说明，所有连接器均为JST GH类型。

## 接线图

![Pixracer顶部接线图](../../assets/flight_controller/pixracer/pixracer_r09_top_pinouts.jpg)

![Pixracer底部接线图](../../assets/flight_controller/pixracer/pixracer_r09_bot_pinouts.jpg)

![Pixracer ESP](../../assets/flight_controller/pixracer/pixracer_r09_esp_01.jpg)

#### TELEM1、TELEM2+OSD端口

| 针脚     | 信号    | 电压  |
| ------- | --------- | ----- |
| 1 (红色) | 电源       | +5V   |
| 2 (黑色) | 发送 (OUT)  | +3.3V |
| 3 (黑色) | 接收 (IN)   | +3.3V |
| 4 (黑色) | CTS (IN)  | +3.3V |
| 5 (黑色) | RTS (OUT) | +3.3V |
| 6 (黑色) | 地线       | GND   |

#### GPS端口

| 针脚     | 信号   | 电压  |
| ------- | -------- | ----- |
| 1 (红色) | 电源      | +5V   |
| 2 (黑色) | 发送 (OUT) | +3.3V |
| 3 (黑色) | 接收 (IN)  | +3.3V |
| 4 (黑色) | I2C1 SCL | +3.3V |
| 5 (黑色) | I2C1 SDA | +3.3V |
| 6 (黑色) | 地线      | GND   |

#### FrSky遥测/SERIAL4

| 针脚     | 信号   | 电压  |
| ------- | -------- | ----- |
| 1 (红色) | 电源      | +5V   |
| 2 (黑色) | 发送 (OUT) | +3.3V |
| 3 (黑色) | 接收 (IN)  | +3.3V |
| 4 (黑色) | 地线      | GND   |

#### 遥控输入 (支持PPM/S.BUS/Spektrum/SUMD/ST24)

| 针脚     | 信号  | 电压  |
| ------- | ------- | ----- |
| 1 (红色) | 电源     | +5V   |
| 2 (黑色) | RC输入   | +3.3V |
| 3 (黑色) | RSSI输入 | +3.3V |
| 4 (黑色) | 3V3电源 | +3.3V |
| 5 (黑色) | 地线     | GND   |

#### CAN总线

| 针脚     | 信号 | 电压 |
| ------- | ------ | ---- |
| 1 (红色) | 电源    | +5V  |
| 2 (黑色) | CAN_H  | +12V |
| 3 (黑色) | CAN_L  | +12V |
| 4 (黑色) | 地线    | GND  |

#### 电源

| 针脚     | 信号  | 电压  |
| ------- | ------- | ----- |
| 1 (红色) | 电源     | +5V   |
| 2 (黑色) | 电源     | +5V   |
| 3 (黑色) | 电流     | +3.3V |
| 4 (黑色) | 电压     | +3.3V |
| 5 (黑色) | 地线     | GND   |
| 6 (黑色) | 地线     | GND   |

#### 开关

| 针脚     | 信号         | 电压  |
| ------- | -------------- | ----- |
| 1 (红色) | 安全           | GND   |
| 2 (黑色) | !IO_LED_SAFETY | GND   |
| 3 (黑色) | 电源           | +3.3V |
| 4 (黑色) | BUZZER-        | -     |
| 5 (黑色) | BUZZER+        | -     |

#### 调试端口

该接线图和连接器符合[Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini)接口规范，详见[Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) (JST SM06B连接器)。

| 针脚     | 信号           | 电压  |
| ------- | ---------------- | ----- |
| 1 (红色) | 目标移位电源     | +3.3V |
| 2 (黑色) | 控制台发送 (OUT) | +3.3V |
| 3 (黑色) | 控制台接收 (IN)  | +3.3V |
| 4 (黑色) | SWDIO            | +3.3V |
| 5 (黑色) | SWCLK            | +3.3V |
| 6 (黑色) | 地线             | GND   |

关于使用该端口的更多信息，请参见：

- [SWD调试端口](../debug/swd_debug.md)
- [PX4系统控制台](../debug/system_console.md) (注意，FMU控制台映射到UART7)

## 串口映射

| UART   | 设备       | 端口                    |
| ------ | ---------- | ----------------------- |
| UART1  | /dev/ttyS0 | WiFi (ESP8266)          |
| USART2 | /dev/ttyS1 | TELEM1 (流控制)         |
| USART3 | /dev/ttyS2 | TELEM2 (流控制)         |
| UART4  |            |
| UART7  | CONSOLE    |
| UART8  | SERIAL4    |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->## 原理图

参考文件为：[Altium Design Files](https://github.com/AUAV-OpenSource/FMUv4-PixRacer)

以下PDF文件仅用于方便提供：

- [pixracer-rc12-12-06-2015-1330.pdf](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixracer/pixracer-rc12-12-06-2015-1330.pdf)
- [pixracer-r14.pdf](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixracer/pixracer-r14.pdf) - R14 或 RC14 印在SD卡插槽旁边## 构建固件

:::tip
大多数用户无需构建此固件！
当连接适当硬件时，_QGroundControl_ 会预构建并自动安装。
:::

要[构建 PX4](../dev_setup/building_px4.md)以用于此目标：

```
make px4_fmu-v4_default
```

## 配置

[指南针校准](../config/compass.md)应在断开USB的情况下进行。  
这始终是推荐操作，但对于Pixracer来说是必须的，因为USB连接会产生特别大的磁干扰。

其他配置与其他板相同。

## Credits

此设计由 Nick Arsov 和 Phillip Kocmoud 创建，由 Lorenz Meier、David Sidrane 和 Leonard Hall 进行架构设计。