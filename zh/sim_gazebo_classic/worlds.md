# Gazebo Classic 世界

本主题提供 PX4 支持的 [Gazebo Classic](../sim_gazebo_classic/index.md) 世界图像/信息。

默认情况下会生成 [empty.world](#empty_world)，但这可以通过 [特定模型的世界](#model_specific_worlds) 覆盖。
开发者也可以手动指定要加载的世界：[Gazebo Classic > 加载特定世界](../sim_gazebo_classic/index.md#loading-a-specific-world)。

支持的世界源代码可以在 GitHub 上找到：[PX4/PX4-SITL_gazebo-classic/tree/main/worlds](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/main/worlds)。

<a id="empty_world"></a>

## 空环境（默认）

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/empty.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/empty.world)

![empty](../../assets/simulation/gazebo_classic/worlds/empty.png)

## Baylands

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/baylands.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/baylands.world)

![Baylands World](../../assets/simulation/gazebo_classic/worlds/baylands.jpg)

## KSQL 机场

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/ksql_airport.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/ksql_airport.world)

![KSQL Airport World](../../assets/simulation/gazebo_classic/worlds/ksql_airport.jpg)

## McMillan 机场

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/mcmillan_airfield.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/mcmillan_airfield.world)

![McMillan Airfield World](../../assets/simulation/gazebo_classic/worlds/mcmillan_airfield.jpg)

## 安全着陆

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/safe_landing.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/safe_landing.world)

![Safe Landing World](../../assets/simulation/gazebo_classic/worlds/safe_landing.png)

## Sonoma 赛道

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/sonoma_raceway.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/sonoma_raceway.world)
![Sonoma_Raceway](../../assets/simulation/gazebo_classic/worlds/sonoma_raceway.png)

## 仓库

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/warehouse.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/warehouse.world)

![Warehouse](../../assets/simulation/gazebo_classic/worlds/warehouse.png)

## 雪山国家公园

[PX4/PX4-SITL_gazebo-classic/tree/main/worlds/yosemite.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/yosemite.world)

![Yosemite](../../assets/simulation/gazebo_classic/worlds/yosemite.jpg)

<a id="model_specific_worlds"></a>

## 机体特定世界

某些 [机体模型](../sim_gazebo_classic/vehicles.md) 依赖于特定世界物理/插件。
如果存在与机体模型同名的世界，PX4 工具链将自动加载该世界（而不是默认的 **empty.world**）：

机体特定世界包括：

- [boat.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/boat.world): 包含表面用于模拟 [船](../sim_gazebo_classic/vehicles.md#unmanned-surface-vehicle-usv-boat) 的浮力。
- [uuv_hippocampus.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/uuv_hippocampus.world): 用于模拟 [HippoCampus UUV](../sim_gazebo_classic/vehicles.md#hippocampus-tuhh-uuv) 水下环境的空世界。
- [typhoon_h480.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/typhoon_h480.world): 由 [Typhoon H480 (Hexrotor)](../sim_gazebo_classic/vehicles.md#typhoon-h480-hexrotor) 机体模型使用，包含视频小部件用于启用/禁用视频流。
  该世界包含一个模拟相机的 gazebo 插件。
- [iris_irlock.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/iris_irlock.world): 包含 IR 信标用于测试 [精确着陆](../advanced_features/precland.md)。