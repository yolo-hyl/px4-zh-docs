# ARK 12S PAB Power Module

[ARK 12S PAB Power Module](https://arkelectron.gitbook.io/ark-documentation/power/ark-12s-pab-power-module) 是一款专为 Pixhawk Autopilot Bus Carrier 板设计的 5V 6A 电源和数字电源监控模块。

![ARK 12S PAB Power Module](../../assets/hardware/power_module/ark_power_modules//ark_12s_pab_power_module.jpg)

## 购买渠道

从以下渠道订购此模块：

- [ARK Electronics](https://arkelectron.com/product/ark-12s-pab-power-module/) (美国)

## 硬件规格

- **TI INA238 Digital Power Monitor**

  - 0.0001 欧姆分流器
  - I2C 接口

- **5.2V 6A 降压调节器**

  - 66V 最大输入电压
  - 6A 输出时 10V 最小输入电压
  - 输出过流保护

- **连接接口**

  - 电池输入焊盘
  - 电池输出焊盘
  - 6针 Molex CLIK-Mate 输出
    - [与 ARK PAB Carrier 电源引脚分配匹配](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pixhawk-autopilot-bus-carrier/pinout)

- **其他**

  - 美国制造
  - 包含 6针 Molex CLIK-Mate 电缆

- **附加信息**
  - 重量：15.5 克
  - 尺寸：3.7 厘米 × 2.2 厘米 × 1.3 厘米

## PX4 设置

- 如果已启用 `SENS_EN_INA226` 参数，请禁用。
- 启用 `SENS_EN_INA238` 参数。
- 重启飞控。
- 将 `INA238_SHUNT` 参数设置为 0.0001。
- 重启飞控。

## 参见

- [ARK 12S PAB Power Module 文档](https://arkelectron.gitbook.io/ark-documentation/power/ark-12s-pab-power-module) (ARK Docs)