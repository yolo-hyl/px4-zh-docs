# Holybro M8N & M9N GPS

该GPS模块包含M8N或M9N UBLOX模块、IST8310磁力计、三色LED指示灯和安全开关。

模块提供三种不同的连接器选项，可便捷地连接到符合Pixhawk连接器标准的飞控器，作为主GPS或从GPS使用。该模块出厂默认波特率为38400 Hz。

模块的主要区别在于：M8N最多可同时接收3个GNSS系统，而M9N最多可同时连接4个GNSS系统。

<img src="../../assets/hardware/gps/holybro_m8n_gps.jpg" width="200px" title="holybro_gps" /> <img src="../../assets/hardware/gps/holybro_m9n_gps.jpg" width="215px" title="holybro_gps" />

### 功能与规格

- Ublox Neo-M8N或M9N模块
- 行业领先的-167 dBm导航灵敏度
- 冷启动时间：26秒
- LNA MAX2659ELT+
- 25 x 25 x 4 mm陶瓷贴片天线
- 可充电Farah电容
- 低噪声3.3V稳压器
- 电流消耗：<150mA @ 5V
- 定位状态指示LED
- 保护外壳
- 含26cm线缆
- 直径：50mm总尺寸，含外壳32克
- **M8N:** 最多同时接收3个GNSS（GPS、伽利略、格洛纳斯、北斗）
- **M9N:** 最多同时接收4个GNSS（GPS、伽利略、格洛纳斯、北斗）

## 购买渠道

- [Holybro](https://holybro.com/collections/gps)

请注意M8N和M9N均有不同型号，并提供三种不同连接器选项。

## 接线与连接

M8N和M9N GPS模块提供三种型号：
这些型号可开箱即用连接到符合Pixhawk连接器标准的飞控器，作为主GPS或从GPS使用。

- **SKU12012 Holybro M8N GPS（JST GHR1.25mm 10针线缆）**。

  该JST GH 10针连接器可用于Pixhawk系列10针`GPS Module`或`GPS1`输入端口。

  ![Holybro M8N与Pixhawk GPS1连接器](../../assets/hardware/gps/holybro_gps_pinout.jpg)

- **SKU12014 Holybro M8N 从GPS（JST GHR1.25mm 6针线缆）**

  该JST GH 6针连接器可用于Pixhawk系列6针`UART`、`I2C`或`GPS2`输入端口作为从GPS。

  ![Holybro M8N与Pixhawk从GPS连接器](../../assets/hardware/gps/holybro_gps_pinout3.jpg)

- **SKU12013 Holybro M8N GPS for Pix32（Molex 1.25mm 6针/4针/3针线缆）**

  这些Molex 1.25mm 6针/4针/3针连接器用于[Holybro pix32飞控](../flight_controller/holybro_pix32.md)`Switch`、`GPS`和`I2C`输入端口（不适用于Pix32 v5或v6）。

  ![Holybro M8N与Pix32连接器](../../assets/hardware/gps/holybro_gps_pinout2.jpg)

## 尺寸

![显示两个模块尺寸的示意图](../../assets/hardware/gps/holybro_gps_dimensions.jpg)