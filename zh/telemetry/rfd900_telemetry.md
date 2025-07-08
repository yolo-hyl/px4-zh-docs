# RFD900 长距离遥测

[jDrones](http://store.jDrones.com) 和 [RFDesign](http://rfdesign.com.au/) 提供 **长距离** [SiK](../telemetry/sik_radio.md) 兼容遥测无线电。
这些无线电设备使用普通天线即可实现超过 5km 的稳定连接（已报告实现更远距离）。

![jDrones 长距离遥测](../../assets/hardware/telemetry/jdrones_long_range_uav_telemetry_rf900set02_2.jpg)

:::tip
_jDrones_ 对 _RFDesign_ 调制解调器进行了产品化（添加了外壳、电源管理、滤波器等内部电子元件，以及连接主流飞控的线缆，并对天线进行了单独验证）。
首个此类调制解调器是 _RFD900_，但 _RFDesign_ 和 _jDrones_ 随后都迭代到了新版本。
:::

_jDrones_ 无线电配备 JST-GH 接口，并附带以下线缆：_JST-GH 到 JST-GH_ 和 _JST-GH 到 DF-13_。因此可以以"即插即用"方式与大多数 [Pixhawk Series](../flight_controller/pixhawk_series.md) 控制器配合使用（某些"非标准"板可能需要更换/使用合适的连接器）。

目前有多个版本可用：

- [jD-RF900Plus 长距离 (900Mhz)](http://store.jdrones.com/jD_RD900Plus_Telemetry_Bundle_p/rf900set02.htm) (美国)
- [jD-RF868Plus 长距离 (868Mhz)](http://store.jdrones.com/jD_RD868Plus_Telemetry_Bundle_p/rf868set02.htm) (欧洲)
- [store.rfdesign.com.au](https://store.rfdesign.com.au/radio-modems/):
  - [RFD 900+ Modem](https://store.rfdesign.com.au/rfd-900p-modem/)
  - [RFD 868x Modem (EU)](https://store.rfdesign.com.au/rfd868x-eu-hs-8517-62-00-90/)
  - [RFD900x](https://store.rfdesign.com.au/rfd-900x-modem-hs-8517-62-00-90/)