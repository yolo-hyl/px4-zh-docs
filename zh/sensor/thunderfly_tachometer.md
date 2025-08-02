

# ThunderFly TFRPM01 转速计数器模块

[TFRPM01](https://github.com/ThunderFly-aerospace/TFRPM01) 转速计是一个体积小巧且系统资源占用低的转速计数器模块。

该电路板本身不包含实际传感器，但可通过多种不同类型传感器/探针进行转速计数。
它配备 I²C 接口用于连接 PX4，并通过 3 针接口与实际传感器连接。
模块上还配有 LED，可提供基础诊断信息。

![TFRPM01A](../../assets/hardware/sensors/tfrpm/tfrpm01_electronics.jpg)

::: info
TFRPM01 传感器是开源硬件，可通过 [ThunderFly s.r.o.](https://www.thunderfly.cz/) 商业购买（制造数据在 [GitHub](https://github.com/ThunderFly-aerospace/TFRPM01) 上提供）。
:::

## 硬件设置

该板配备有两个通道的I²C连接器用于连接PX4，同时具有一个3针连接器可用于连接各种传感器：

- TFRPM01可连接到任意I²C端口。
- TFRPM01具有3针针脚连接器（带上拉输入）可连接到不同类型的探头。
  - 传感器/探头硬件需要脉冲信号。
    信号输入可接受+5V TTL逻辑或[open collector](https://en.wikipedia.org/wiki/Open_collector)输出。
    最大脉冲频率为20 kHz且占空比50%。
  - 探头连接器通过I²C总线提供+5V电源，最大可用功率受RC滤波器限制（详情请参见原理图）。

TFRPM01A电子设备配备信号LED，可用于检查探头连接状态。
当脉冲输入接地或处于逻辑0时LED会亮起，因此可通过手动旋转转子检查探头是否正常工作。

### 霍尔效应传感器探针

霍尔效应传感器（磁性操作的）适用于恶劣环境，例如污垢、灰尘和水可能接触到被测转子的环境。

目前市面上有多种霍尔效应传感器可供选择。例如，[55100小型法兰安装接近传感器](https://m.littelfuse.com/media?resourcetype=datasheets&itemid=6d69d457-770e-46ba-9998-012c5e0aedd7&filename=littelfuse-hall-effect-sensors-55100-datasheet) 是一个不错的选择。

![霍尔效应探针示例](../../assets/hardware/sensors/tfrpm/hall_probe.jpg)

### 光学传感器探头

也可以使用光学传感器（并且根据测量要求可能更合适）。  
透射式和反射式传感器类型都可用于脉冲生成。  

![光学透射式探头示例](../../assets/hardware/sensors/tfrpm/transmissive_probe.jpg)

## 软件设置

### 启动驱动程序

驱动程序不会在任何机体中自动启动。  
你需要手动启动它，可以通过使用[QGroundControl MAVLink控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)，或者将驱动程序添加到SD卡上的[startup script](../concept/system_startup.md#customizing-the-system-startup)。

#### 从控制台启动驱动

从[控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)使用以下命令启动驱动：

```sh
pcf8583 start -X -b <bus number>
```

其中：

- `-X` 表示使用外部总线
- `<bus number>` 是设备连接的总线编号

::: info
代码中的总线号 `-b <bus number>` 可能与自动驾驶仪上的总线标签不匹配。
例如，当使用 CUAV V5+ 或 CUAV Nano 时：

| 自动驾驶仪标签 | -b 编号 |
| -------------- | ------- |
| I2C1           | -X -b 4 |
| I2C2           | -X -b 2 |
| I2C3           | -X -b 1 |

`pcf8583 start` 命令会输出每个总线编号对应的自动驾驶仪总线名称/标签。
:::

### 测试

您可以使用多种方法验证计数器是否正常工作

#### PX4 (NuttX) MAVLink 控制台

[QGroundControl MAVLink 控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html) 还可用于检查驱动程序是否运行以及它输出的 UORB 主题。

要检查 TFRPM01 驱动程序的状态，请运行命令：

```sh
pcf8583 status
```

如果驱动程序正在运行，将打印 I²C 端口以及运行实例的其他基本参数。
如果驱动程序未运行，可以按照上述流程启动。

[listener](../modules/modules_command.md#listener) 命令可用于监控来自运行驱动程序的 RPM UORB 消息。

```sh
listener rpm
```

要周期性显示，可以在命令后添加 `-n 50` 参数，这将打印接下来的 50 条消息。

#### QGroundControl MAVLink 检查器

QGroundControl [MAVLink 检查器](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html) 可用于观察来自 PX4 的 MAVLink 消息，包括由驱动程序发出的 [RAW_RPM](https://mavlink.io/en/messages/common.html#RAW_RPM)：

1. 从 QGC 菜单启动检查器：**分析工具 > MAVLink 检查器**  
1. 检查 `RAW_RPM` 是否在消息列表中（如果缺失，请检查驱动程序是否正在运行）。

### 参数设置

通常，传感器可直接使用而无需配置，但RPM值应与实际RPM的倍数对应。这是因为`PCF8583_MAGNET`参数需对应所测转子单次旋转的脉冲数。  
如果需要，应调整以下参数：

- [PCF8583_POOL](../advanced_config/parameter_reference.md#PCF8583_POOL) — 读取计数的轮询间隔  
- [PCF8583_RESET](../advanced_config/parameter_reference.md#PCF8583_RESET) — 计数器值达到此数值时，计数应重置为零  
- [PCF8583_MAGNET](../advanced_config/parameter_reference.md#PCF8583_MAGNET) — 每转脉冲数，例如转子盘上的磁铁数量  

::: info  
上述参数在驱动/PX4重启后会显示在QGC中。  

如果重启后配置参数未出现，则应检查驱动是否已启动。  
可能驱动[未包含在固件中](../peripherals/serial_configuration.md#configuration-parameter-missing-from-qgroundcontrol)，此时需将其添加到板级配置中：  

```sh  
drivers/rpm/pcf8583  
```  

:::