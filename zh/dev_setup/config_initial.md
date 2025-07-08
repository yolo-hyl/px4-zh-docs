# 初始设置与配置

我们建议开发者获取以下描述的基本设备和软件（或类似设备）

## 基本设备

:::tip
PX4 可以使用比此处描述的更广泛的设备，但新开发者最好选择标准配置之一。
Taranis 遥控器和中档安卓平板即可组成非常经济的现场套件。
:::

以下设备强烈推荐：

- **安全飞行员用遥控器**  
  - [Taranis Plus](https://www.frsky-rc.com/product/taranis-x9d-plus-2/) 遥控器（或等效设备）
- **开发用电脑**

  ::: info
  列出的电脑性能尚可，但建议使用更近期且性能更强的电脑。
  :::

  - 搭载 i5 处理器的联想 Thinkpad 运行 Windows 11
  - MacBook Pro（2015 年初及以后型号）搭载 macOS 10.15 或更高版本
  - 搭载 i5 处理器的联想 Thinkpad 运行 Ubuntu Linux 20.04 或更高版本

- **地面控制站**（电脑或平板）：
  - iPad（可能需要 Wi-Fi 通信适配器）
  - 任何 MacBook 或 Ubuntu Linux 笔记本电脑（可以是开发电脑）
  - 一款近期的中档安卓平板或手机，屏幕足够大以有效运行 _QGroundControl_（6 英寸）
- **可运行 PX4 的机体**：
  - [购买预组装机体](../complete_vehicles_mc/index.md)
  - [自行组装](../frames_multicopter/kits.md)
- **护目镜**
- **系留绳**（仅限多旋翼，用于高风险测试）

## 机体配置

为桌面操作系统安装 [QGroundControl Daily Build](../dev_setup/qgc_daily_build.md)。

配置机体的步骤：

1. [安装 PX4 固件](../config/firmware.md#installing-px4-main-beta-or-custom-firmware)（包括包含自定义更改的"自定义"固件）。
1. 从 [机体参考手册](../airframes/airframe_reference.md) 中选择最适合您机体的 [初始机体类型](../config/airframe.md)。
1. [基础配置](../config/index.md) 说明如何执行基本配置。
1. [参数配置](../advanced_config/parameters.md) 说明如何查找和修改单个参数。

::: info

- _QGroundControl_ 移动版本不支持机体配置。
- _每日构建版_ 包含开发工具和官方发布版中没有的新功能。
- 机体参考手册中的配置已在实际机体上飞行过，是"起飞"的良好起点。

:::