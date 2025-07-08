# WiFi遥测无线电

WiFi遥测支持机体上的WiFi无线电与地面站（GCS）之间的MAVLink通信。  
与普通遥测无线电相比，WiFi通常传输距离较短，但支持更高的数据速率，并且更便于支持FPV/视频传输。  
通常机体仅需一个无线电单元（假设地面站已具备WiFi功能）。

PX4通过UDP和WiFi支持遥测。它会向255.255.255.255的14550端口广播心跳包，直到收到地面控制站的第一个心跳包后，将仅向该地面控制站发送数据。

兼容的WiFi遥测模块包括：

- [ESP8266 WiFi模块](../telemetry/esp8266_wifi_module.md)
- [ESP32 WiFi模块](../telemetry/esp8266_wifi_module.md)
- [3DR Telemetry WiFi](../telemetry/3dr_telemetry_wifi.md) (已停产)