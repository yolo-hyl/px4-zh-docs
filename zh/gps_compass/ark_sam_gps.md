# ARK SAM GPS

[ARK SAM GPS](https://arkelectron.gitbook.io/ark-documentation/gps/ark-sam-gps>) 是一款美国制造且符合NDAA要求的 [GNSS/GPS](../gps_compass/index.md) u-blox SAM-M10Q GPS模块及工业级磁力计。

![ARK SAM GPS](../../assets/hardware/gps/ark/ark_sam_gps.jpg)

## 购买渠道

可通过以下渠道订购该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-sam-gps/) (美国)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_SAM_GPS/tree/main)
- 传感器
  - [u-blox SAM-M10Q](https://www.u-blox.com/en/product/sam-m10q-module)
    - 在不牺牲GNSS性能的前提下功耗低于38mW
    - 支持4个GNSS同时接收，实现最大定位可用性
    - 具备先进的欺骗和干扰检测功能
  - [ST IIS2MDC磁力计](https://www.st.com/en/mems-and-sensors/iis2mdc.html)
- Pixhawk标准UART/I2C接口（6针JST SH）
- 电源需求
  - 5V
  - 平均15mA
  - 最大20mA
- LED指示灯
  - GPS定位状态
- 美国制造
- NDAA合规
- 6针Pixhawk标准UART/I2C线缆

## 硬件安装

该模块配备Pixhawk标准6针接口，可插入最新Pixhawk飞控器的`GPS2`端口。
建议安装时正面朝上，并尽可能远离飞控器及其他电子设备。

更多详情请参见 [GNSS/磁力计安装](../gps_compass/index.md#mounting-the-gnss-compass) 和 [硬件安装](../gps_compass/index.md#hardware-setup)。

## PX4配置

该模块在使用大多数飞控器的`GPS2`端口时应可即插即用。

[备用GPS配置（UART）](../gps_compass/index.md#secondary-gps-configuration-uart) 说明了如何配置端口（注意：即使将GPS2作为主磁力计使用，配置方式相同）。

## 引脚定义

### Pixhawk标准UART/I2C接口 - 6针JST-SH

| 引脚编号 | 信号名称 | 电压 |
| -------- | -------- | ---- |
| 1        | 5V       | 5.0V |
| 2        | RX       | 3.3V |
| 3        | TX       | 3.3V |
| 4        | SCL      | 3.3V |
| 5        | SDA      | 3.3V |
| 6        | GND      | GND  |

## 参见

- [ARK SAM GPS官方文档](https://arkelectron.gitbook.io/ark-documentation/gps/ark-sam-gps) (ARK Docs)