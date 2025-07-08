# Holybro遥测无线电

Holybro [SiK](../telemetry/sik_radio.md) 遥测无线电是一款小巧、轻便且价格实惠的开源"即插即用"无线电平台，用于连接您的自动飞行控制器和地面站。

该无线电与所有具备JST-GH 6针连接器TELEM端口的运行PX4的飞控器均兼容（[Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards)）。它提供了在飞控器与地面站之间建立遥测连接最简单的方式。该无线电使用专为MAVLink数据包优化且与QGroundControl & PX4自动驾驶仪集成的开源固件。

开箱即用状态下，其通信距离通常超过300米（通过地面使用补丁天线可扩展至数公里）。

无线电频率可选择915 MHz（美国）或433 MHz（欧盟、亚洲、非洲大洋洲）。请注意，上述区域划分仅供参考，您应核查本地的法规要求。

<img src="../../assets/hardware/telemetry/holybro_sik_radio_v3.png" width="600px" title="Sik Telemetry Radio" />

## 购买渠道

- [Holybro SiK遥测无线电V3](https://holybro.com/collections/telemetry-radios/products/sik-telemetry-radio-v3)

## 特性

- 开源SiK固件
- 与Pixhawk标准飞控器即插即用
- 连接自动驾驶仪和地面站的最简便方式
- 可互换的空用和地面无线电
- 微型USB接口（附带Type-C适配线）
- 6位JST-GH连接器

## 规格参数

- 最大输出功率100 mW（可调节）-117 dBm接收灵敏度
- RP-SMA连接器
- 通过自适应TDM UART接口的双工全双工通信
- 透明串行链路
- MAVLink协议封装
- 可配置占空比的跳频扩频(FHSS)
- 错误纠正可修正高达25%的位错误 开源SiK固件
- 可通过Mission Planner & APM Planner配置
- FT230X USB转BASIC UART芯片

## LED状态指示

无线电配备2个状态LED，一个红色和一个绿色。不同LED状态的含义如下：

- 绿色LED闪烁 - 正在搜索另一台无线电
- 绿色LED常亮 - 已与其他无线电建立连接
- 红色LED闪烁 - 正在传输数据
- 红色LED常亮 - 固件更新模式

<img src="../../assets/hardware/telemetry/holybro_sik_telemetry_label.jpg" width="500px" title="Pixhawk5x Upright Image" />

## 连接飞控器

使用无线电附带的6针JST-GH连接器将其连接到飞控器的`TELEM1`端口（也可使用`TELEM2`，但推荐默认使用`TELEM1`）。

## 连接PC或地面站

将无线电连接到Windows PC或地面站的操作如同将微型/Type-C USB线缆（无线电附带的Type-C适配器）连接到您的PC/地面站一样简单。

所需驱动程序将自动安装，无线电会作为新的“USB串口”出现在Windows设备管理器的端口（COM和LPT）下。Mission Planner的COM端口选择下拉菜单中也会显示相同的COM端口。

## 包装内容

- 带天线的无线电模块 *2
- 微型USB到USB-A数据线 *1
- 微型USB到微型USB OTG适配线 *1
- 微型USB到Type C适配器
- JST-GH-6P到JST-GH-6P线缆 *1（用于Pixhawk标准飞控）
- JST-GH-6P到Molex DF12 6P线缆（用于Pix32、Pixhawk 2.4.6等）

<img src="../../assets/hardware/telemetry/holybro_sik_radio_v3_include.png" width="600px" title="Sik Telemetry Radio" />