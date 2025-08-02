# 探测器

<LinkedBadge type="warning" text="Experimental" url="../airframes/#experimental-vehicles"/>

:::warning
对探测器的支持为[实验性质](../airframes/index.md#experimental-vehicles)。
欢迎维护人员志愿者贡献新特性、新框架配置或其他改进！[贡献](../contribute/index.md)
:::

![探测器](../../assets/airframes/rover/rovers.png)

PX4 支持三种最常见的探测器类型：
| 探测器类型                | 转向方式                                                                                                                                                      |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**阿克曼**](#阿克曼)   | 通过将车轮指向运动方向来控制方向。这种转向方式被大多数商业车辆（包括汽车、卡车等）采用。                                                                      |
| [**差速**](#差速) | 通过调整左右轮速度差异来控制方向。                                                                                                                            |
| [**麦克纳姆**](#麦克纳姆)  | 通过单独调整每个麦克纳姆轮的速度和方向来控制方向。                                                                                                            |

支持的框架可在 [Airframes Reference > 探测器](../airframes/airframe_reference.md#rover) 中查看。

## 阿克曼

<Badge type="tip" text="PX4 v1.16" /> <Badge type="warning" text="Experimental" />

阿克曼探测器通过将前轮指向运动方向来控制方向——[阿克曼转向几何](https://en.wikipedia.org/wiki/Ackermann_steering_geometry) 补偿了转弯时内外侧车轮速度不同的现象。
这种转向方式被大多数商业车辆（包括汽车、卡车等）采用。

::: info
PX4 不强制要求车辆使用阿克曼几何，任何前轮转向的探测器均可正常工作。
:::

![轴向越野探测器](../../assets/airframes/rover/axial_trail_honcho.png)

## 差速

<Badge type="tip" text="PX4 v1.16" /> <Badge type="warning" text="Experimental" />

差速探测器通过差速驱动机制控制运动，通过独立调整左右轮速度实现目标前进速度和偏航率。
前进通过同时以相同方向驱动双轮实现。
旋转通过以相反方向不同速度驱动双轮实现，使探测器可就地转向。

![Aion R1](../../assets/airframes/rover/aion_r1/r1_rover_no_bg.png)

::: info
差速配置同样适用于滑移转向或履带式探测器。
:::

## 麦克纳姆

<Badge type="tip" text="PX4 v1.16" /> <Badge type="warning" text="Experimental" />

麦克纳姆探测器是一种使用麦克纳姆轮实现全向移动的移动机器人。这些轮子的特殊之处在于其环周安装了45度角的滚轮，使探测器无需旋转即可实现前后、侧向和对角移动。
每个轮子由独立电机驱动，通过控制每个电机的速度和方向，探测器可以向任意方向移动或原地旋转。

![麦克纳姆探测器](../../assets/airframes/rover/rover_mecanum.png)

## 参见

- [驱动模式](../flight_modes_rover/index.md)。
- [配置/调参](../config_rover/index.md)
- [完整机体](../complete_vehicles_rover/index.md)

## 仿真

[Gazebo](../sim_gazebo_gz/index.md) 提供阿克曼和差速探测器的仿真：

- [阿克曼探测器](../sim_gazebo_gz/vehicles.md#ackermann-rover)
- [差速探测器](../sim_gazebo_gz/vehicles.md#differential-rover)

![探测器 Gazebo 仿真](../../assets/airframes/rover/rover_simulation.png)