# ARK Pixhawk自动驾驶仪总线载板

:::warning
PX4不生产此(或任何)自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://arkelectron.com/contact-us/)
:::

[ARK Pixhawk自动驾驶仪总线(PAB)载板](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pixhawk-autopilot-bus-carrier)是基于[Pixhawk自动驾驶仪总线开源标准](https://github.com/pixhawk/Pixhawk-Standards)的美国制造飞控载板。

PAB外形设计使ARK PAB载板可以与任何[PAB兼容飞控](../flight_controller/pixhawk_autopilot_bus.md)配合使用，例如[ARKV6X](../flight_controller/ark_v6x.md)。

![ARKPAB主视图](../../assets/flight_controller/arkpab/ark_pab_main.jpg)

### 购买渠道

从[Ark Electronics](https://arkelectron.com/product/ark-pixhawk-autopilot-bus-carrier/)订购 (美国)

## 功能特性

- [Pixhawk 自主飞行器总线 (PAB) 外形规格](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf?_ga=2.20605755.2081055420.1671562222-391294592.1671562222)
- 美国制造## 连接器

- PAB 板对板接口
  - 100针 Hirose DF40
  - 40针 Hirose DF40
- 双数字电源模块输入
  - 5V 输入
  - I2C 电源监控器
  - 6针 Molex CLIK-Mate
- 以太网
  - 100Mbps
  - 内置磁芯
  - 4针 JST-GH
- 完整 GPS 及安全开关端口
  - 10针 JST-GH
- 基础 GPS 端口
  - 6针 JST-GH
- 双 CAN 端口
  - 4针 JST-GH
- 三路遥测端口（带流控）
  - 6针 JST-GH
- 八路 PWM 输出
  - 10针 JST-GH
- UART/I2C 端口
  - 6针 JST-GH
- I2C 端口
  - 4针 JST-GH
- PPM 电调端口
  - 3针 JST-GH
- DSM 电调端口
  - 3针 JST-ZH
- SPI 端口
  - 11针 JST-GH
- ADIO 端口
  - 8针 JST-GH
- 调试端口
  - 10针 JST-SH## 尺寸

- 未安装飞控模块
  - 74.0mm x 43.5mm x 12.0mm
  - 22g## 电源

- `POWER1`、`POWER2`、`USB C` 和 `USB JST-GH` 接口的 5V 输入
  - 输入优先级顺序为：POWER1 > POWER2 > USB
  - `USB C` 和 `USB JST-GH` 并联
  - 5.8V 过压保护
  - 3.9V 欠压保护
- `VDD_5V_HIPOWER` 和 `VDD_5V_PERIPH` 每个均可在所有连接器上提供总计 1.5A 的电流## LED指示灯

- ARK PAB板载有两个LED指示灯  
  - `红色` 为以太网电源指示灯  
  - `绿色` 为以太网活动指示灯## 引脚分配

![ARKPAB Pinout](../../assets/flight_controller/arkpab/arkpab_pinout.jpg)

## 电源1

| 引脚     | 信号       | 电压    |
| ------- | ---------- | ------ |
| 1 (红)  | `VBRICK1`  | +5.0V  |
| 2 (黑)  | `VBRICK1`  | +5.0V  |
| 3 (黑)  | I2C1_SCL   | +3.3V  |
| 4 (黑)  | I2C1_SDA   | +3.3V  |
| 5 (黑)  | `GND`      | GND    |
| 6 (黑)  | `GND`      | GND    |## 电源2

| 引脚     | 信号    | 电压  |
| ------- | --------- | ----- |
| 1（红色） | `VBRICK2` | +5.0V |
| 2（黑色） | `VBRICK2` | +5.0V |
| 3（黑色） | I2C2_SCL  | +3.3V |
| 4（黑色） | I2C2_SDA  | +3.3V |
| 5（黑色） | `GND`     | 地   |
| 6（黑色） | `GND`     | 地   |## PWM

| 引脚      | 信号                    | 电压   |
| -------- | ------------------------- | ----- |
| 1（红）  | VDD_SERVO（未连接）       | +5.0V |
| 2（黑）  | FMU_CH1                   | +3.3V |
| 3（黑）  | FMU_CH2                   | +3.3V |
| 4（黑）  | FMU_CH3                   | +3.3V |
| 5（黑）  | FMU_CH4                   | +3.3V |
| 6（黑）  | FMU_CH5                   | +3.3V |
| 7（黑）  | FMU_CH6                   | +3.3V |
| 8（黑）  | FMU_CH7                   | +3.3V |
| 9（黑）  | FMU_CH8                   | +3.3V |
| 10（黑） | `GND`                     | GND   |## GPS1

| 引脚     | 信号                 | 电压   |
| -------- | ---------------------- | ----- |
| 1 (red)  | `VDD_5V_PERIPH`        | +5.0V |
| 2 (blk)  | USART1_TX_GPS1         | +3.3V |
| 3 (blk)  | USART1_RX_GPS1         | +3.3V |
| 4 (blk)  | I2C1_SCL               | +3.3V |
| 5 (blk)  | I2C1_SDA               | +3.3V |
| 6 (blk)  | nSAFETY_SWITCH_IN      | +3.3V |
| 7 (blk)  | nSAFETY_SWITCH_LED_OUT | +3.3V |
| 8 (blk)  | `3V3_FMU`              | +3.3V |
| 9 (blk)  | BUZZER                 | +5.0V |
| 10 (blk) | `GND`                  | GND   |## GPS2

| 引脚     | 信号           | 电压  |
| ------- | ---------------- | ----- |
| 1（红色） | `VDD_5V_HIPOWER` | +5.0V |
| 2（黑色） | UART8_TX_GPS2    | +3.3V |
| 3（黑色） | UART8_RX_GPS2    | +3.3V |
| 4（黑色） | I2C2_SCL         | +3.3V |
| 5（黑色） | I2C2_SDA         | +3.3V |
| 6（黑色） | `GND`            | 地    |## TELEM1

| 引脚     | 信号           | 电压  |
| ------- | ---------------- | ----- |
| 1 (红色) | `VDD_5V_HIPOWER` | +5.0V |
| 2 (黑色) | UART7_TX         | +3.3V |
| 3 (黑色) | UART7_RX         | +3.3V |
| 4 (黑色) | UART7_CTS        | +3.3V |
| 5 (黑色) | UART7_RTS        | +3.3V |
| 6 (黑色) | `GND`            | GND   |## TELEM2

| 引脚     | 信号              | 电压    |
| ------- | ----------------- | ------- |
| 1 (红色) | `VDD_5V_PERIPH`   | +5.0V   |
| 2 (黑色) | UART5_TX          | +3.3V   |
| 3 (黑色) | UART5_RX          | +3.3V   |
| 4 (黑色) | UART5_CTS         | +3.3V   |
| 5 (黑色) | UART5_RTS         | +3.3V   |
| 6 (黑色) | `GND`             | GND     |## TELEM3

| 引脚     | 信号           | 电压   |
| ------- | ---------------- | ----- |
| 1 (红) | `VDD_5V_HIPOWER` | +5.0V |
| 2 (黑) | USART2_TX        | +3.3V |
| 3 (黑) | USART2_RX        | +3.3V |
| 4 (黑) | USART2_CTS       | +3.3V |
| 5 (黑) | USART2_RTS       | +3.3V |
| 6 (黑) | `GND`            | GND   |## UART4/I2C3

| 引脚     | 信号            | 电压   |
| ------- | --------------- | ----- |
| 1 (red) | `VDD_5V_PERIPH` | +5.0V |
| 2 (blk) | UART4_TX        | +3.3V |
| 3 (blk) | UART4_RX        | +3.3V |
| 4 (blk) | I2C3_SCL        | +3.3V |
| 5 (blk) | I2C3_SDA        | +3.3V |
| 6 (blk) | `GND`           | GND   |## I2C3

| 引脚     | 信号          | 电压   |
| ------- | --------------- | ----- |
| 1 (红色) | `VDD_5V_PERIPH` | +5.0V |
| 2 (黑)  | I2C3_SCL        | +3.3V |
| 3 (黑)  | I2C3_SDA        | +3.3V |
| 4 (黑)  | `GND`           | GND   |## CAN1

| 引脚     | 信号           | 电压   |
| ------- | ---------------- | ----- |
| 1 (red) | `VDD_5V_HIPOWER` | +5.0V |
| 2 (blk) | CAN1_H           | +3.3V |
| 3 (blk) | CAN1_L           | +3.3V |
| 4 (blk) | `GND`            | GND   |## CAN2

| 引脚     | 信号          | 电压  |
| ------- | --------------- | ----- |
| 1 (red) | `VDD_5V_PERIPH` | +5.0V |
| 2 (blk) | CAN2_H          | +3.3V |
| 3 (blk) | CAN2_L          | +3.3V |
| 4 (blk) | `GND`           | GND   |## USB

所有信号与USB C连接器并行
引脚 | 信号 | 电压
--- | --- | ---
1 (red) | `VBUS_IN` | +5.0V
2 (blk) | USB_N | +3.3V
3 (blk) | USB_P | +3.3V
4 (blk) | `GND` | GND## ETH

| 引脚     | 信号       | 电压                 |
| ------- | ---------- | -------------------- |
| 1 (red) | ETH_RD_N  | +50.0V Tolerant     |
| 2 (blk) | ETH_RD_P  | +50.0V Tolerant     |
| 3 (blk) | ETH_TD_N  | +50.0V Tolerant     |
| 4 (blk) | ETH_TD_P  | +50.0V Tolerant     |## ADIO

| 引脚     | 信号          | 电压  |
| ------- | --------------- | ----- |
| 1 (红色) | `VDD_5V_PERIPH` | +5.0V |
| 2 (黑色) | FMU_CAP         | +3.3V |
| 3 (黑色) | BOOTLOADER      | +3.3V |
| 4 (黑色) | FMU_RST_REQ     | +3.3V |
| 5 (黑色) | nARMED          | +3.3V |
| 6 (黑色) | ADC1_3V3        | +3.3V |
| 7 (黑色) | ADC1_6V6        | +3.3V |
| 8 (黑色) | `GND`           | GND   |## RC/SBUS

| 引脚     | 信号             | 电压   |
| ------- | ------------------ | ----- |
| 1 (红色) | `VDD_5V_SBUS_RC`   | +5.0V |
| 2 (黑色) | USART6_RX_SBUS_IN  | +3.3V |
| 3 (黑色) | USART6_TX          | +3.3V |
| 4 (黑色) | `VDD_3V3_SPEKTRUM` | +3.3V |
| 5 (黑色) | `GND`              | GND   |## 脉冲调制（PPM）

| Pin     | 信号                  | 电压   |
| ------- | ----------------------- | ----- |
| 1 (红色) | `VDD_5V_PPM_RC`         | +5.0V |
| 2 (黑色) | DSM_INPUT/FMU_PPM_INPUT | +3.3V |
| 3 (黑色) | `GND`                   | GND   |## DSM

| 引脚     | 信号                  | 电压  |
| ------- | ----------------------- | ----- |
| 1 (红色) | `VDD_3V3_SPEKTRUM`      | +3.3V |
| 2 (黑色) | `GND`                   | GND   |
| 3 (黑色) | DSM_INPUT/FMU_PPM_INPUT | +3.3V |## SPI6

| 引脚      | 信号            | 电压    |
| -------- | --------------- | ------- |
| 1 (红色)  | `VDD_5V_PERIPH` | +5.0V   |
| 2 (黑色)  | SPI6_SCK        | +3.3V   |
| 3 (黑色)  | SPI6_MISO       | +3.3V   |
| 4 (黑色)  | SPI6_MOSI       | +3.3V   |
| 5 (黑色)  | SPI6_nCS1       | +3.3V   |
| 6 (黑色)  | SPI6_nCS2       | +3.3V   |
| 7 (黑色)  | SPIX_nSYNC      | +3.3V   |
| 8 (黑色)  | SPI6_DRDY1      | +3.3V   |
| 9 (黑色)  | SPI6_DRDY2      | +3.3V   |
| 10 (黑色) | SPI6_nRESET     | +3.3V   |
| 11 (黑色) | `GND`           | GND     |## 调试端口

[PX4 系统控制台](../debug/system_console.md) 和 [SWD 接口](../debug/swd_debug.md) 运行在 **FMU 调试** 端口上。

其引脚定义和连接器符合 [Pixhawk 调试完整](../debug/swd_debug.md#pixhawk-debug-full) 接口规范，该规范定义于 [Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) (JST SM10B 连接器)。

| 引脚      | 信号           | 电压  |
| -------- | ---------------- | ----- |
| 1 (红色)  | `Vtref`          | +3.3V |
| 2 (黑色)  | 控制台 TX（输出） | +3.3V |
| 3 (黑色)  | 控制台 RX（输入）  | +3.3V |
| 4 (黑色)  | `SWDIO`          | +3.3V |
| 5 (黑色)  | `SWCLK`          | +3.3V |
| 6 (黑色)  | `SWO`            | +3.3V |
| 7 (黑色)  | NFC GPIO         | +3.3V |
| 8 (黑色)  | PH11             | +3.3V |
| 9 (黑色)  | nRST             | +3.3V |
| 10 (黑色) | `GND`            | GND   |

关于使用此端口的更多信息请参见：

- [SWD 调试端口](../debug/swd_debug.md)
- [PX4 系统控制台](../debug/system_console.md) (注意，FMU 控制台映射到 USART3)

![ARKPAB 俯视图](../../assets/flight_controller/arkpab/ark_pab_top.jpg)

![ARKPAB 底部视图](../../assets/flight_controller/arkpab/ark_pab_back.jpg)

## 另请参见

- [ARK Pixhawk Autopilot Bus Carrier](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pixhawk-autopilot-bus-carrier)（ARK 文档）