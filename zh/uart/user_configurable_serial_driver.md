# 使串口驱动可用户配置

本主题说明如何设置串口驱动，使其可通过参数由终端用户配置，从而在飞行控制器板上的任何可配置串口上运行。

## 前提条件

假设驱动已存在，并可通过以下命令语法在壳层中启动：

```sh
<driver_name> start -d <serial_port> [-b <baudrate> | -b p:<param_name>]
```

其中，

- `-d`: 串口名称。
- `-b`: 波特率（可选），如果驱动支持多波特率。
  如果支持，则驱动必须允许同时以裸波特率和参数名的形式指定速率，格式为`-b p:<param_name>`（可通过`px4_get_parameter_value()`解析）。
  :::tip
  请参考[gps驱动](https://github.com/PX4/PX4-Autopilot/blob/main/src/drivers/gps/gps.cpp#L1023)示例。
  :::

## 使驱动可配置

要使驱动可配置：

1. 创建YAML模块配置文件：

   - 在驱动源目录中添加名为**module.yaml**的新文件
   - 插入以下文本并根据需要调整：

     ```cmake
     module_name: uLanding Radar
     serial_config:
         - command: ulanding_radar start -d ${SERIAL_DEV} -b p:${BAUD_PARAM}
           port_config_param:
             name: SENS_ULAND_CFG
             group: Sensors
     ```

     ::: info
     模块配置文件的完整文档可在[validation/module_schema.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml)文件中找到。
     它也用于验证CI中的所有配置文件。
     :::

1. 将模块配置添加到驱动模块的**CMakeLists.txt**文件中：

   ```cmake
   px4_add_module(
   	MODULE drivers__ulanding
   	MAIN ulanding_radar
   	SRCS
   		ulanding.cpp
   	MODULE_CONFIG
   		module.yaml
   	)
   ```