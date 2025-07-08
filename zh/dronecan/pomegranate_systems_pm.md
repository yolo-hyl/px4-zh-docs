# Pomegranate Systems 电源模块

::: info
2022年，UAVCAN (v0) 被分叉并作为 `DroneCAN` 维护。
虽然此产品仍提及 "UAVCAN"，但完全兼容 PX4 的 DroneCAN 支持。
:::

![模块图像](../../assets/hardware/power_module/pomegranate_systems_pm/main_image.jpg)

具备高分辨率电流积分功能的数字电源模块，5V/2A 供电并带电源监控，单 DroneCAN CAN总线接口，以及 RGB 状态指示灯。

详细设置、配置和故障排除信息请参考[制造商设备主页](https://p-systems.io/product/power_module)。

## 硬件规格

- **输入电压：** 6-26V (2-6S)
- **最大持续电流：**
  - **台式测试：** 40A
  - **强制冷却：** 100A
- **最大 5V 输出电流：** 2A
- **电压分辨率：** 0.04 ΔV
- **电流分辨率：**
  - **主/电池总线：** 0.02 ΔA
  - **5V 总线：** 0.001 ΔA
- **CAN总线终端电阻：** 电子式（默认开启）
- **MCU：** STM32F302K8U
- **电气接口：**
  - **电源：** 焊盘或 XT60PW（直角板载连接器）
  - **CAN总线：** 双 JST GH-4（标准 UAVCAN 微型连接器）
  - **I2C/串口：** JST GH-5
  - **5V 输出：** 焊盘或 CAN总线/I2C 连接器
- **设备重量：**
  - **不含连接器：** 9g
  - **含 XT60PW 连接器：** 16g

![尺寸图](../../assets/hardware/power_module/pomegranate_systems_pm/mechanical.png)

## 固件设置

电源模块运行 pomegranate systems 自研的定制（开源）固件。
源代码和编译说明请参考 [bitbucket](https://bitbucket.org/p-systems/firmware/src/master)。

## 飞控设置

1. 通过将 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 参数设置为 `2`（传感器自动配置）或 `3` 来启用 DroneCAN。
1. 通过将 [UAVCAN_SUB_BAT](../advanced_config/parameter_reference.md#UAVCAN_SUB_BAT) 设置为 `1` 或 `2`（根据电池类型选择）来启用 DroneCAN 电池监控。
1. 使用 [MAVLink 控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html) 设置以下模块参数：
   - 电池容量（mAh）：`battery_capacity_mAh`
   - 电池满电电压：`battery_full_V`，
   - 电池放空电压：`battery_empty_V`
   - 启用电流积分：`enable_current_track`
   - （可选）关闭 CAN总线终端电阻：`enable_can_term`

**示例：** UAVCAN 节点 ID 为 `125`、连接 3S 锂电池（容量 5000mAh）的电源模块可通过以下命令配置：

```sh
uavcan param set 125 battery_capacity_mAh 5000
uavcan param set 125 battery_full_V 12.5
uavcan param set 125 battery_empty_V 11.2
uavcan param set 125 enable_current_track 1
uavcan param save 125
```

完整参数列表请参阅[设备配置页面](https://p-systems.io/product/power_module/configuration)。