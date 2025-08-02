# Lightware SF45/B 旋转激光雷达

LightWare [SF45/B](https://lightwarelidar.com/shop/sf45-b-50-m/) 是一款极其小巧且轻便的旋转激光雷达，探测距离可达50米。  
它可在不使用协同计算机的情况下启用 [碰撞预防](../computer_vision/collision_prevention.md) 功能。

![LightWare SF45 旋转激光雷达](../../assets/hardware/sensors/lidar_lightware/sf45.png)

:::info
激光雷达驱动程序未包含在 PX4 的默认构建中。  
您需要[创建并使用自定义构建](#向 PX4 构建中添加驱动程序)。
:::

## LightWare Studio 配置

在 [LightWare Studio](https://www.lightwarelidar.com/resources-software) 应用程序中设置以下参数：

| 参数        | 描述         |
| ----------- | ------------ |
| 波特率      | 921600       |

确保扫描角度设置为避免机体上的任何物体干扰测量。驱动程序和 [碰撞预防](../computer_vision/collision_prevention.md) 会自动处理不同于最大角度的扫描角度。

## 硬件设置

测距仪可以连接到任何未使用的串口，例如 `TELEM2`。[参数配置](#参数配置) 会说明如何配置所使用的端口及其他测距仪属性。

## PX4 设置

### 向 PX4 构建中添加驱动程序

该 LiDAR 的驱动程序默认未包含在 PX4 固件中。

您需要执行以下步骤：

1. 将 [lightware_sf45_serial](../modules/modules_driver_distance_sensor.md#lightware-sf45-serial) 驱动程序添加到固件：
   - 安装并打开 [menuconfig](../hardware/porting_guide_config.md#px4-menuconfig-setup)
   - 在 [menuconfig](../hardware/porting_guide_config.md#px4-menuconfig-setup) 中导航到 **Drivers > Distance sensors**
   - 选择/启用 `lightware_sf45_serial`
2. [构建 PX4](../dev_setup/building_px4.md) 您的飞控目标，然后上传新固件。

### 参数配置

您需要配置 PX4 以指示传感器连接的串口（参见 [串口配置](../peripherals/serial_configuration.md)），以及传感器的方向和其他属性。

需更改的 [参数](../advanced_config/parameters.md) 如表格所示：

| 参数                                                                                                   | 描述                                              |
| ------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| <a id="SENS_EN_SF45_CFG"></a>[SENS_EN_SF45_CFG](../advanced_config/parameter_reference.md#SENS_EN_SF45_CFG) | 设置为传感器连接的串口。                            |
| <a id="SF45_ORIENT_CFG"></a>[SF45_ORIENT_CFG](../advanced_config/parameter_reference.md#SF45_ORIENT_CFG)   | 设置传感器方向（朝上或朝下）                       |
| <a id="SF45_UPDATE_CFG"></a>[SF45_UPDATE_CFG](../advanced_config/parameter_reference.md#SF45_UPDATE_CFG)   | 设置更新频率                                      |
| <a id="SF45_YAW_CFG"></a>[SF45_YAW_CFG](../advanced_config/parameter_reference.md#SF45_YAW_CFG)            | 设置偏航方向                                      |

## 测试

您可以通过连接 QGroundControl 并观察 [MAVLink Inspector](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html) 中是否存在 [OBSTACLE_DISTANCE](https://mavlink.io/en/messages/common.html#OBSTACLE_DISTANCE) 来确认传感器配置是否正确。

QGC 中的障碍物叠加效果如下所示：

![QGC 中显示的 SF45 障碍物避障图](../../assets/hardware/sensors/lidar_lightware/sf45_obstacle_map.png)

## 驱动程序实现

[sensor driver](../modules/modules_driver_distance_sensor.md#lightware-sf45-serial) 发布 [ObstacleDistance](../msg_docs/ObstacleDistance.md) UORB 消息，供 PX4 [碰撞预防](../computer_vision/collision_prevention.md) 使用。  
每个扇区的测量值将对应传感器在该扇区的最小测量值。数据随后发布到 [OBSTACLE_DISTANCE](https://mavlink.io/en/messages/common.html#OBSTACLE_DISTANCE) MAVLink 消息中。

## 故障排除

### 错误和抖动

启动问题、抖动或通过 `lightware_sf45_serial status` 显示大量通信错误可能意味着传感器供电不足。

根据其数据手册，传感器需要在 5V 时提供 300 mA 电流。  
提供的电缆较长，当连接到 5V 电源（如飞控的 `TELEM2` 端口）时，测距仪的电压可能低于所需水平。  
一种缓解方法是通过电池电压的独立降压转换器为 SF45 供电，确保测距仪两端有 5V 电压。

### 使用 PlotJuggler 调试

如果能在 x-y 图中绘制 [OBSTACLE_DISTANCE](https://mavlink.io/en/messages/common.html#OBSTACLE_DISTANCE) 消息，距离传感器的问题调试会更容易，因为这可以直观显示测量值相对于机体的方位。

下图演示了如何在 PlotJuggler 中查看此类图：

<lite-youtube videoid="VwEd_7aiLEo" title="PX4 Autopilot: SF45 rangefinder - collision prevention "/>

为生成此类图，您需要向 PlotJuggler 添加以下响应式 Lua 脚本：

```lua
obs_dist_xy = ScatterXY.new("obstacle_distance_xy")
```

::: tip
在 [碰撞预防](../computer_vision/collision_prevention.md) 部分可找到更多调试技巧。
:::

```lua
-- 全局代码，执行一次
obs_dist_xy = ScatterXY.new("obstacle_distance_xy")
```

```lua
-- 每次数据更新时调用
function process()
    -- 获取障碍物距离数据
    dist = get_obstacle_distance()
    -- 更新图示
    obs_dist_xy:add_point(current_angle, dist)
end
```