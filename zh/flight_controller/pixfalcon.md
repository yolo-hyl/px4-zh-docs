# Pixfalcon飞行控制器（已停产）

<Badge type="info" text="已停产" />

:::warning
此飞行控制器已[停产](../flight_controller/autopilot_experimental.md)，目前不再商业销售。
:::

:::warning
PX4不生产此（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

Pixfalcon自动驾驶仪（由[Holybro<sup>&reg;</sup>](https://holybro.com/)设计）是[Pixhawk 1](../flight_controller/pixhawk.md)设计的二进制兼容（FMUv2）衍生版本，针对空间受限的应用场景（如FPV竞速机）进行了优化，其IO接口较少以实现尺寸缩减。

![Pixfalcon主图](../../assets/hardware/hardware-pixfalcon.png)

## 快速概览

- 主系统芯片：[STM32F427](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN1789)
  - CPU：180 MHz ARM<sup>&reg;</sup> Cortex<sup>&reg;</sup> M4，带单精度FPU
  - RAM：256 KB SRAM（L1）
- 失效保护系统芯片：STM32F100
  - CPU：24 MHz ARM Cortex M3
  - RAM：8 KB SRAM
- GPS：u-blox<sup>&reg;</sup> M8（含配套设备）

### 连接性

- 1x I2C
- 2x UART（1个用于数传/OSD，无流控制）
- 8x PWM带手动覆盖
- S.BUS / PPM 输入

## 可用性

通过经销商[Hobbyking<sup>&reg;</sup>](https://hobbyking.com/en_us/pixfalcon-micro-px4-autopilot-plus-micro-m8n-gps-and-mega-pbd-power-module.html)获取

可选硬件：

- 光流传感器：来自制造商[Holybro](https://holybro.com/products/px4flow)的PX4 Flow单元
- 数字空速传感器：来自制造商[Holybro](https://holybro.com/products/digital-air-speed-sensor)或经销商[Hobbyking](https://hobbyking.com/en_us/hkpilot-32-digital-air-speed-sensor-and-pitot-tube-set.html)
- 集成数传的屏幕显示：
  - [Hobbyking OSD + EU 数传（433 MHz）](https://hobbyking.com/en_us/micro-hkpilot-telemetry-radio-module-with-on-screen-display-osd-unit-433mhz.html)
- 纯数传选项：
  - [Hobbyking 无线WiFi数传](https://hobbyking.com/en_us/apm-pixhawk-wireless-wifi-radio-module.html)
  - [SIK Radios](../telemetry/sik_radio.md)

## 固件构建

:::tip
大多数用户无需自行构建此固件！
当连接到适当硬件时，_QGroundControl_ 会自动预构建并安装固件。
:::

为此目标[构建PX4](../dev_setup/building_px4.md)的命令：

```
make px4_fmu-v2_default
```

## 调试端口

此板无调试端口（即没有访问[系统控制台](../debug/system_console.md)或[SWD接口](../debug/swd_debug.md)（JTAG）的端口）。

开发人员需要将导线焊接到板上的测试垫以获取SWD，并焊接到STM32F4（IC）的TX和RX以获得控制台。

## 串口映射

| UART   | 设备       | 端口                     |
| ------ | ---------- | ------------------------ |
| UART1  | /dev/ttyS0 | IO 调试                 |
| USART2 | /dev/ttyS1 | TELEM1（无流控制）      |
| UART4  | /dev/ttyS2 | GPS                     |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->