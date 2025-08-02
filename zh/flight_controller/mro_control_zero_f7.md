# mRo Control Zero F7飞行控制器

:::warning
PX4不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.mrobotics.io/)。
:::

_mRo Control Zero F7<sup>&reg;</sup>_ 是mRo推出的新一代飞行控制器。

![mRo Control Zero F7](../../assets/flight_controller/mro_control_zero_f7/mro_control_zero_f7.jpg)

这是一款无妥协设计的三重IMU商用级飞行控制器。
包含8路PWM输出（支持DShot）、3个IMU、1个磁力计、1个气压传感器（高度计）、6路UART、SD卡，所有功能集成在32mm x 20mm的PCB中。
PWM输出为双向设计，具有EMI保护并电平转换至5V逻辑电平。
所有接口通过前后30针Molex PicoClasp连接器访问。
提供耐用塑料外壳、共形板涂层，并可选温度校准功能。

::: info
此飞行控制器获得[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 核心特性

- 微处理器:

  - 32位STM32F777 Cortex<sup>&reg;</sup> M4核心带FPU rev. 3
  - 216 MHz/512 KB RAM/2 MB Flash
  - Cypress MF25V02-G 256-Kbit铁电存储器（具备RAM速度的非易失性存储）

- 传感器:

  - [Bosch BMI088](https://www.bosch-sensortec.com/bst/products/all_products/bmi088_1) 三轴加速度计/陀螺仪（内置振动阻尼）
  - [Invensense ICM-20602](https://www.invensense.com/products/motion-tracking/6-axis/icm-20602/) 三轴加速度计/陀螺仪
  - [Invensense ICM-20948](https://www.invensense.com/products/motion-tracking/9-axis/icm-20948/) 三轴加速度计/陀螺仪/磁力计
  - [Infineon DPS310气压计](https://www.infineon.com/cms/en/product/sensor/pressure-sensors/pressure-sensors-for-iot/dps310/)（平滑性能且无光照敏感性）

- 接口:

  - 6路UART（3路支持硬件流控），1路FRSky遥测（D/X型），1路控制台+GPS+I2C
  - 8路PWM输出（均支持DShot）
  - 1路CAN
  - 1路I2C
  - 1路SPI
  - Spektrum DSM/DSM2/DSM-X® 卫星兼容输入及绑定
  - Futaba S.BUS® & S.BUS2® 兼容输入
  - FRSky遥测输出
  - Graupner SUMD
  - Yuneec ST24
  - PPM总和输入信号
  - 1路JTAG（TC2030连接器）
  - 1路RSSI（PWM或电压）输入
  - 三色LED

- 重量与尺寸（不含外壳）:

  - 重量：5.3g（0.19盎司）
  - 宽度：20mm（0.79英寸）
  - 长度：32mm（1.26英寸）

- 电源系统:
  - 3路超低噪声LDO电压调节器

## 购买渠道

- [mRo Control Zero](https://store.mrobotics.io/mRo-Control-Zero-F7-p/mro-ctrl-zero-f7.htm)

## 固件编译

:::tip
大多数用户无需自行编译固件！
当连接相应硬件时，_QGroundControl_ 会自动预装并安装该固件。
:::

为该目标编译[PX4固件](../dev_setup/building_px4.md)：

```
make mro_ctrl-zero-f7
```

## 调试端口

### 控制台端口

[PX4系统控制台](../debug/system_console.md)运行在`USART7`上，使用以下引脚。
此为标准串口配置，设计用于连接[3.3V FTDI](https://www.digikey.com/en/products/detail/TTL-232R-3V3/768-1015-ND/1836393)电缆（5V兼容）。

| mRo control zero f7 |             | FTDI |
| ------------------- | ----------- | ---- |
| 17                  | USART7 Tx   | 5    | FTDI RX（黄色） |
| 19                  | USART7 Rx   | 4    | FTDI TX（橙色） |
| 6                   | USART21 GND | 1    | FTDI GND（黑色） |

### SWD端口

[SWD端口](../debug/swd_debug.md)（JTAG）采用TC2030调试连接器，如下图所示。

![mro swd端口](../../assets/flight_controller/mro_control_zero_f7/mro_control_zero_f7_swd.jpg)

可通过[Tag Connect](https://www.tag-connect.com/)电缆[TC2030 IDC NL](https://www.tag-connect.com/product/tc2030-idc-nl)（配[固定夹](https://www.tag-connect.com/product/tc2030-clip-retaining-clip-board-for-tc2030-nl-cables)）连接至BlackMagic探针或ST-LINK V2调试器。

![tc2030 idc nl电缆](../../assets/flight_controller/mro_control_zero_f7/tc2030_idc_nl.jpg)

还可使用[ARM20-CTX 20针转TC2030-IDC适配器](https://www.tag-connect.com/product/arm20-ctx-20-pin-to-tc2030-idc-adapter-for-cortex)与其他调试探针配合使用。

## 引脚分配

![mRo Control Zero F7](../../assets/flight_controller/mro_control_zero_f7/mro_control_pinouts.jpg)

## 串口映射

| UART   | 设备       | 端口                                                            |
| ------ | ---------- | --------------------------------------------------------------- |
| USART2 | /dev/ttyS0 | TELEM1（带流控）                                           |
| USART3 | /dev/ttyS1 | TELEM2（带流控）                                           |
| UART4  | /dev/ttyS2 | GPS1                                                            |
| USART6 | /dev/ttyS3 | 灵活端口（可配置为SPI或带流控的UART）                 |
| UART7  | /dev/ttyS4 | 控制台                                                         |
| UART8  | /dev/ttyS5 | 可用串口（通常用于FrSky遥测）                         |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口信息 -->
<!-- 参见 https://github.com/PX4/PX4-Autopilot/blob/master/platforms/px4/src/px4/px4_tasks.c 中的配置 -->

## 更多信息

- [Introducing the new mRo Control Zero Autopilot](https://mrobotics.io/introducing-the-new-mro-control-zero-autopilot/) (blog)
- [Quick Start Guide](https://mrobotics.io/mrocontrolzero/)