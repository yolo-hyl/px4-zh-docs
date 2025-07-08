# 3DR WiFi 通信模块（已停产）

::: info
该产品已停止生产，3DR 不再提供。
:::

PX4 支持 _3DR WiFi 通信模块_。  
只需将其连接至飞控的 `TELEM1` 接口，即可为机体创建以下参数的 WiFi "热点"：

```sh
essid: APM_PIX
password: 12345678
```

将地面站连接至上述 WiFi SSID。  
连接成功后，机体应能被自动识别并连接至 _QGroundControl_。

![3DR Wifi Telemetry Radio 1](../../assets/hardware/telemetry/3dr_telemetry_wifi_1.jpg)
![3DR Wifi Telemetry Radio 2](../../assets/hardware/telemetry/3dr_telemetry_wifi_2.jpg)