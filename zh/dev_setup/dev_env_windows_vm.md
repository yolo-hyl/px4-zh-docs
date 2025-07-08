# Windows 虚拟机托管工具链

:::warning
此开发环境由[社区支持和维护](../advanced/community_supported_dev_env.md)。
该环境可能与当前版本的PX4兼容，也可能不兼容。

有关核心开发团队支持的环境和工具的信息，请参阅[工具链安装](../dev_setup/dev_env.md)。
:::

Windows 开发人员可以在虚拟机（VM）中运行 PX4 工具链，其中 Linux 作为客户机操作系统。  
设置好虚拟机后，虚拟机内 PX4 的安装和配置与原生 Linux 计算机上的操作完全一致。

虽然使用虚拟机设置和测试构建固件的环境非常简便，但用户需要注意以下几点：

1. 固件构建速度会比原生 Linux 慢。  
1. JMAVSim 模拟的帧率会比原生 Linux 慢得多。  
   在某些情况下，由于虚拟机资源不足，机体可能会因相关问题而坠毁。  
1. 可以安装 Gazebo 和 ROS，但运行速度会非常缓慢，几乎无法使用。

:::tip
尽可能为虚拟机分配更多的 CPU 核心和内存资源。
:::

有多种方法可以在您的系统上设置能够执行 PX4 环境的虚拟机。  
本指南将引导您完成 VMWare 的设置。  
最后还有一个不完整的 VirtualBox 部分（我们欢迎社区成员扩展此部分）。

## VMWare 设置

VMWare 的性能对于基本使用（构建固件）是可接受的，但不适用于运行 ROS 或 Gazebo Classic。

1. 下载 [VMWare Player Freeware](https://www.vmware.com/products/workstation-player/workstation-player-evaluation.html)  
1. 在 Windows 系统上安装  
1. 下载所需的 [Ubuntu 桌面 ISO 镜像](https://www.ubuntu.com/download/desktop)。  
   （参见 [Linux 指南页面](../dev_setup/dev_env_linux.md) 了解推荐的 Ubuntu 版本）。  
1. 打开 _VMWare Player_。  
1. 在虚拟机设置中启用 3D 加速：**VM > 设置 > 硬件 > 显示 > 加速 3D 图形**

   ::: info
   此选项对于正确运行 jMAVSim 和 Gazebo Classic 等 3D 模拟环境是必需的。  
   我们建议在虚拟环境中安装 Linux 之前完成此操作。
   :::

1. 选择创建新虚拟机的选项。  
1. 在虚拟机创建向导中，选择下载的 Ubuntu ISO 镜像作为安装介质，系统会自动检测要使用的操作系统。  
1. 在向导中，选择运行虚拟机时要分配的资源。  
   尽可能分配更多内存和 CPU 核心，但不要导致宿主 Windows 系统无法使用。  
1. 在向导结束时运行新虚拟机，并按照设置说明安装 Ubuntu。  
   请注意，所有设置仅用于宿主操作系统内部使用，因此可以禁用任何不会增加网络攻击风险的屏幕保护程序和本地工作站安全功能。  
1. 新虚拟机启动后，确保在客户系统中安装 _VMWare 工具驱动程序和扩展工具_。  
   这将增强虚拟机的性能和可用性：

   - 显著提升图形性能  
   - 正确支持硬件设备使用（如 USB 端口分配（对目标上传至关重要）、鼠标滚轮滚动、声音支持）  
   - 客户显示分辨率根据窗口大小自动调整  
   - 剪贴板与宿主系统共享  
   - 文件与宿主系统共享  

1. 继续进行 [Linux 的 PX4 环境设置](../dev_setup/dev_env_linux.md)

## VirtualBox 7 设置

VirtualBox 的设置与 VMWare 类似。  
社区成员，我们欢迎在此处提供分步指南！

### QGroundControl / 固件烧录的 USB 透传

:::tip
本节已在运行 Ubuntu 20.04 LTS 的 Windows 10 主机上测试过。
:::

虚拟机的一个限制是，您无法自动连接到插入主机计算机 USB 端口的飞控器，以[从终端构建和上传 PX4 固件](../dev_setup/building_px4.md#uploading-firmware-flashing-the-board)。  
您也无法从虚拟机中的 QGroundControl 连接到飞控器。

为此，您需要配置 USB 透传设置：

1. 确保通过终端命令将用户添加到虚拟机中的 dialout 组：

   ```sh
   sudo usermod -a -G dialout $USER
   ```

   然后重新启动虚拟机中的 Ubuntu。

1. 在 VM 中启用串口：**VirtualBox > 设置 > 串口 1/2/3/等...**  
1. 在 VM 中启用 USB 控制器：**VirtualBox > 设置 > USB**  
1. 在 VM 中为引导加载程序添加 USB 过滤器：**VirtualBox > 设置 > USB > 添加新的 USB 过滤器**。

   - 打开菜单并插入连接到自动驾驶仪的 USB 电缆。  
     当 UI 中出现设备时选择 `...Bootloader` 设备。

     ::: info
     引导加载程序设备在连接 USB 后仅显示几秒钟。  
     如果在您选择之前消失，请断开并重新连接 USB。
     :::

   - 当出现时选择 `...Autopilot` 设备（这在引导加载程序完成后发生）。

1. 在虚拟机实例的下拉菜单中选择设备：**VirtualBox > 设备 > your_device**

如果成功，您的设备将通过 `lsusb` 显示，并且 QGroundControl 会自动连接到该设备。  
您还应该能够使用如下命令构建和上传固件：

```sh
make px4_fmu-v5_default upload
```

### QGroundControl 的 WiFi 通信遥测

如果在虚拟机中使用 _QGroundControl_，应将虚拟机网络设置为 "桥接适配器" 模式。  
这使客户操作系统可以直接访问主机的网络硬件。  
如果您使用默认设置为 VirtualBox 7 运行 Ubuntu 20.04 LTS 的网络地址转换 (NAT)，这将阻止 QGroundControl 与机体通信所需的传出 UDP 数据包。