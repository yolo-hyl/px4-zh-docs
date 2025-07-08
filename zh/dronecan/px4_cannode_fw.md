# PX4 DroneCAN 固件

PX4 可以作为固件运行在许多 DroneCAN 外设上。这种方式具有以下优势：

- PX4 内置了对[广泛传感器和外设组件](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers)的驱动支持。
- PX4 的 DroneCAN 驱动实现经过多年实地测试，具有高度可靠性。
- PX4 持续开发迭代，您可常规获得最新改进。
- PX4 的估计与控制代码使得创建集成 AHRS 模块等"智能"节点变得容易。
- 固件完全开源（PX4 采用 BSD 协议）。

## 构建固件

按照 [PX4 构建文档](../dev_setup/building_px4.md) 进行操作，与构建飞控固件相同。设备构建配置存储在 [此处](https://github.com/PX4/PX4-Autopilot/tree/main/boards)。安装完 [PX4 工具链](../dev_setup/dev_env.md) 后克隆源码并构建。例如为 [Ark Flow](ark_flow.md) 目标构建：

```sh
git clone --recursive https://github.com/PX4/PX4-Autopilot
cd PX4-Autopilot
make ark_can-flow_default
```

这将在 **build/ark_can-flow_default** 目录生成名为 **XX-X.X.XXXXXXXX.uavcan.bin** 的输出文件。按照 [DroneCAN 固件更新](index.md#firmware-update) 指南进行固件烧录。

## 开发者信息

本节内容针对希望为 PX4 Autopilot 添加新 DroneCAN 硬件支持的开发者。

### DroneCAN 引导程序安装

:::warning
DroneCAN 设备通常预装了引导程序。
除非您正在开发 DroneCAN 设备或意外损坏/擦除了引导程序，否则请勿执行本节操作。
:::

PX4 项目包含适用于 STM32 设备的标准 DroneCAN 引导程序。

引导程序占用闪存前 8-16 KB，是上电时最先执行的代码。通常引导程序会执行设备底层初始化，自动确定 CAN 总线波特率，作为 [DroneCAN 动态节点 ID 客户端](index.md#node-id-allocation) 获取唯一节点 ID，并等待飞控确认后才执行应用程序启动。

该流程确保了 DroneCAN 设备在应用程序固件损坏时能无需用户干预自动恢复，并支持自动固件更新。

通过指定相同的外设目标并使用 `canbootloader` 构建配置（而非默认配置）来构建引导程序固件。

例如为 [Ark Flow](ark_flow.md) 目标构建：

```sh
git clone --recursive https://github.com/PX4/PX4-Autopilot
cd PX4-Autopilot
make ark_can-flow_canbootloader
```

然后可以使用您喜欢的 SWD/JTAG 调试器（如 [Black Magic Probe](https://black-magic.org/index.html)、[ST-Link](https://www.st.com/en/development-tools/st-link-v2.html) 或 [Segger JLink](https://www.segger.com/products/debug-probes/j-link/)）将二进制文件烧录到微控制器。

### 固件内部结构

总的来说，外设固件的运行方式与飞控固件相同。但大多数模块被禁用 - 仅启用传感器驱动、DroneCAN 驱动和内部基础设施（uORB 等）。

DroneCAN 通信由 [uavcannode](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/uavcannode) 模块处理。
该驱动处理生产端通信 - 它从 uORB 获取传感器/执行器数据，使用 DroneCAN 库进行序列化，并通过 CAN 发布。
未来该模块可能会与 [uavcan](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/uavcan) 模块合并，后者处理飞控端（消费端）驱动，接收并反序列化 CAN 总线数据后通过 uORB 发布。

构建系统还会生成通过 DroneCAN 引导程序烧录的固件二进制文件，除了标准的原始二进制、ELF 和 `.px4` 固件文件外，还支持 [PX4 的 DroneCAN 烧录支持](index.md#firmware-update) 或 DroneCAN GUI 工具进行烧录。