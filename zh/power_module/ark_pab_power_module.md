# ARK PAB 电源模块

[ARK PAB 电源模块](https://arkelectron.gitbook.io/ark-documentation/power/ark-pab-power-module) 是一款额定在 20°C 环境温度下可提供 60A 连续电池电流的先进电源模块。
对于更高连续电流需求，建议并联运行多个电源模块。
需要注意的是，在 60A 和 20°C 无冷却条件下，5V 调压器会降额至 3A 连续输出。

![ARK PAB 电源模块](../../assets/hardware/power_module/ark_power_modules//ark_pab_power_module.jpg)

## 购买渠道

可通过以下渠道订购该模块：

- [ARK 电子](https://arkelectron.com/product/ark-pab-power-module/)（美国）

## 硬件规格

- **TI INA226 数字电源监控器**

  - 0.0005 欧姆分流器
  - I2C 接口

- **5.2V 6A 降压稳压器**

  - 33V 最大输入电压
  - 6A 输出时 5.8V 最小输入电压
  - 输出过压保护
  - 输出过流保护

- **连接器**

  - XT60 电池输入
  - XT60 电池输出
  - 6针 Molex CLIK-Mate 输出
    - [匹配 ARK PAB 载板电源引脚定义](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pixhawk-autopilot-bus-carrier/pinout)

- **其他**

  - 美国制造
  - 符合 FCC 认证
  - 配备 6针 Molex CLIK-Mate 电缆

- **附加信息**
  - 重量：17.9 克
  - 尺寸：4.75 厘米 x 3.43 厘米 x 1.15 厘米

## PX4 设置

- 如果 SENS_EN_INA226 参数未启用，请启用该参数。
- 重启飞控。

## 参见

- [ARK PAB 电源模块文档](https://arkelectron.gitbook.io/ark-documentation/power/ark-pab-power-module)（ARK 官方文档）