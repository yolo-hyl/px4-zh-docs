# FlightGear 机体

:::warning
此模拟器由[社区支持和维护](../simulation/community_supported_simulators.md)。
它可能与当前版本的 PX4 一起使用，也可能不支持。

有关核心开发团队支持的环境和工具的信息，请参见[工具链安装](../dev_setup/dev_env.md)。
:::

本主题列出了/展示了 PX4 [FlightGear](../sim_flightgear/index.md) 模拟器支持的机体，以及运行它们所需的 `make` 命令（命令在 **PX4-Autopilot** 目录的终端中运行）。
支持的类型包括：固定翼飞机、自转旋翼机和地面车辆（这些类型中包含特定的机体框架）。

:::tip
要查看完整的构建目标列表，请运行 `make px4_sitl list_vmd_make_targets`（过滤掉以 `flightgear_` 开头的条目）。
:::

::: info
[FlightGear](../sim_flightgear/index.md) 页面详细说明了如何安装和使用 FlightGear（此页面是针对特定机体功能的摘要）。
:::

## 标准固定翼飞机

FlightGear 包含多种飞机模型。
目前最适合无人机开发的是 [Rascal RC 飞机](https://github.com/ThunderFly-aerospace/FlightGear-Rascal)（该模型有多个变体）。

![Rascal飞机在FlightGear中的示例](../../assets/simulation/flightgear/vehicles/rascal110.jpg)

各变体的主要区别在于 [FDM](http://wiki.flightgear.org/Flight_Dynamics_Model) 模型。
所有变体都有一个通用功能选择表，可通过按下计算机键盘上的 `=` 键激活。

还有一个弹出式表格，可用于激活高级功能。

![Rascal飞机FlightGear高级选项](../../assets/simulation/flightgear/vehicles/rascal_options.jpg)

最重要的选项包括：

- Smoke - 生成烟雾轨迹以增强飞行器在空中的可见性（需要在 **FG View > rendering options > Particles checkbox** 中激活烟雾和粒子选项）。
- Trajectory markers - 沿飞行轨迹显示正交标记。

轨迹标记显示世界坐标中的绝对飞行路径，而烟雾轨迹显示空气团中的相对路径。

### Rascal 110 YASim

Rascal 模型的主要变体采用往复式活塞发动机模型。
这导致怠速时功率非零，造成螺旋桨在怠速转速下旋转。

启动命令：

```sh
make px4_sitl_nolockstep flightgear_rascal
```

### Rascal 110 Electric YASim

配备电动发动机的 Rascal 机体。

```sh
make px4_sitl_nolockstep flightgear_rascal-electric
```

::: info
此变体需要最新的 FlightGear 代码（源码至少来自 2020 年 4 月 26 日）。
否则，由于电动发动机的意外定义，FlightGear 会崩溃。
:::

### Rascal 110 JSBsim

Rascal JSBsim 变体。

此变体没有直接的 `make` 选项，但可以在 **rascal.json** 配置文件中手动选择（该文件是 [PX4-FlightGear-Bridge](https://github.com/ThunderFly-aerospace/PX4-FlightGear-Bridge) 的一部分）。
只需将 [此文件](https://github.com/ThunderFly-aerospace/PX4-FlightGear-Bridge/blob/master/models/rascal.json#L2) 中的 `Rascal110-YASim` 更改为 `Rascal110-JSBSim`。

## 自转旋翼机

FlightGear 目前只支持一种无人机自转旋翼机模型，即 [TF-G1 自转旋翼机](https://github.com/ThunderFly-aerospace/TF-G1)。

```sh
make px4_sitl_nolockstep flightgear_tf-g1
```

![TF-G1在FlightGear中的示例](../../assets/simulation/flightgear/vehicles/tf-g1.jpg)

## Ackerman车辆（UGV/地面车辆）

### TF-R1 地面支援车

此地面车辆配备了牵引钩，可用于牵引其他飞行器。

```sh
make px4_sitl_nolockstep flightgear_tf-r1
```

![TF-R1地面车辆在FlightGear中的示例](../../assets/simulation/flightgear/vehicles/tf-r1_towing.jpg)

## 四旋翼无人机

目前只有一个 [不完整的多旋翼模型](https://github.com/ThunderFly-aerospace/FlightGear-TF-Mx1)。
该模型尚不可用（数值不稳定，需要进一步开发）。

## 添加新机体

新机体模型需要作为 git 子模块包含到 [PX4-FlightGear-Bridge/models/](https://github.com/PX4/PX4-FlightGear-Bridge/tree/master/models) 目录中。
该目录包含一个控制通道定义的 [JSON 文件](https://github.com/PX4/PX4-FlightGear-Bridge/blob/master/models/rascal.json)。

```json
{
  "FgModel": "Rascal110-YASim",
  "Url": "https://github.com/ThunderFly-aerospace/FlightGear-Rascal/archive/master.zip",
  "Controls": [
    ["5", "/controls/flight/aileron", "-1"],
    ["7", "/controls/flight/elevator", "-1"],
    ["2", "/controls/flight/rudder", "1"],
    ["4", "/controls/engines/engine/throttle", "1"]
  ]
}
```

文件内容含义如下：

- `FgModel` - FlightGear 模型的精确大小写名称，对应模型目录中 "XXXX-set.xml"（XXXX 为模型名称）。
- `Url` 是可选的，目前未使用。用于将来自动从网络下载模型。
- `Controls` - 添加新机体过程中最重要的部分。此部分包含 PX4 混合文件与 [FlightGear 属性树](http://wiki.flightgear.org/Property_tree) 的映射。
  - 列表中的第一个数字选择 PX4 混合输出。
  - 路径字符串是属性树中的 FlightGear 变量位置。
- 列表中的最后一个数字是乘数，允许反转或缩放混合输入。

准备所有文件后，新机体需要包含在 PX4 的 make 系统中。

PX4 配置位于 [/platforms/posix/cmake/sitl_target.cmake](https://github.com/PX4/PX4-Autopilot/blob/c5341da8137f460c84f47f0e38293667ea69a6cb/platforms/posix/cmake/sitl_target.cmake#L164-L171)。
新机体的 json 名称应添加到列表中。