# Auterion Skynode

[Skynode](https://auterion.com/product/skynode/) 是一款功能强大的飞行计算机，将任务计算机、飞行控制器、视频流传输、网络连接和蜂窝连接等功能集成在单一高度整合的设备中。

![Auterion Skynode (Enterprise)](../../assets/companion_computer/auterion_skynode/skynode_small.png)

机载软件是 Auterion OS，包括在飞行控制器上运行的企业级强化版本 PX4，以及在任务计算机上运行的操作系统和高级管理软件。
该操作系统由 Auterion 在生产环境中进行管理，客户应用作为任务计算机中安全沙箱中的"附加组件"运行。

Auterion OS 和 Skynode 可与 Auterion 的其他软件和机队管理产品无缝集成。

关于 Auterion 和 Skynode 的更多信息：

- [auterion.com](https://auterion.com/)
- [Skynode](https://auterion.com/product/skynode/) (auterion.com)
- Skynode 指南：
  - [制造商指南](https://docs.auterion.com/manufacturers/getting-started/readme)
  - [应用开发者指南](https://docs.auterion.com/developers/getting-started/readme)

## Skynode 与纯净版 PX4

Skynode 预装了由 Auterion 管理的 PX4 发行版。
如果需要尝试更新的 PX4 飞行内核，可以从 [PX4/PX4-Autopilot](https://github.com/PX4/PX4-Autopilot) 安装上游的"纯净版" PX4。

上游 PX4 通常可以正常工作，但需要注意以下事项：

- 可能缺少针对您具体产品的配置。
  您可能会丢失 ESC、电池、传感器配置等配置信息。
- Auterion OS 发行的 PX4 版本中某些参数的默认值可能不同。
- 通过随行计算机运行的供应商特定自定义功能可能在 PX4 中不可用。
- Auterion 支持运行其自有 Auterion 管理版本 PX4 的 Skynode。

## 构建/上传固件

Skynode 的 PX4 `px4_fmu-v5x` 二进制文件通过标准 [开发者环境](../dev_setup/dev_env.md) 和 [构建命令](../dev_setup/building_px4.md) 从源代码构建，使用 `upload_skynode_usb` 或 `upload_skynode_wifi` 上传目标进行上传。

`upload_skynode_usb` 和 `upload_skynode_wifi` 通过网络接口使用默认（固定）IP 地址连接到 Skynode，分别参考 [USB](https://docs.auterion.com/manufacturers/avionics/skynode/advanced-configuration/connecting-to-skynode) 和 [WiFi](https://docs.auterion.com/manufacturers/avionics/skynode/advanced-configuration/configuration)，并将 TAR 压缩的二进制文件上传到任务计算机。
任务计算机随后解压二进制文件并将其安装到飞行控制器。

::: info
使用这些上传目标需要 SSH 和 TAR，但这些工具在 Ubuntu 和运行在 WSL2 上的 Windows Ubuntu 中默认已安装。
在 macOS 上应首先安装 [gnu-tar](https://formulae.brew.sh/formula/gnu-tar)。
:::

在上传过程中需要两次输入 Skynode 开发者镜像的密码。

:::: tabs

::: tab "通过 USB 连接的 Skynode"

```
make px4_fmu-v5x upload_skynode_usb
```

:::

::: tab "通过 WiFi 连接的 Skynode"

```
make px4_fmu-v5x upload_skynode_wifi
```

:::

::::

## 恢复默认 PX4 固件

当通过 USB 连接时，在仓库中运行以下命令可重新安装原始 Skynode 版本的 PX4：

:::: tabs

::: tab "通过 USB 连接的 Skynode"

```
./Tools/auterion/upload_skynode.sh --revert
```

:::

::: tab "通过 WiFi 连接的 Skynode"

```
./Tools/auterion/upload_skynode.sh --revert --wifi
```

:::

::::