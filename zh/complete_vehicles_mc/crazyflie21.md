# Crazyflie 2.1

<LinkedBadge type="warning" text="Experimental" url="../flight_controller/autopilot_experimental.html"/>

:::warning
PX4 不生产此类（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://www.bitcraze.io/)。
:::

:::warning
Crazyflie 2.1 仅能在 [Stabilized mode](../flight_modes_mc/manual_stabilized.md) 中飞行。
:::

Crazyflie 系列微型四轴飞行器由 Bitcraze AB 开发。
Crazyflie 2.1 的概述可[在此处找到](https://www.bitcraze.io/products/crazyflie-2-1/)。

![Crazyflie2 图像](../../assets/flight_controller/crazyflie21/crazyflie_2.1.jpg)

## 快速概览

::: info
主要的硬件文档在[这里](https://wiki.bitcraze.io/projects:crazyflie2:index)
:::

- 主系统芯片：STM32F405RG  
  - CPU：168 MHz ARM Cortex M4 单精度FPU  
  - RAM：192 KB SRAM  
- nRF51822 无线电与电源管理微控制器  
- BMI088 三轴加速度计/陀螺仪  
- BMP388 高精度压力传感器  
- uUSB 接口  
- 板载LiPo电池充电器，支持100mA、500mA和980mA模式  
- 全速USB设备接口  
- 部分USB OTG功能（USB OTG存在但无5V输出）  
- 8KB EEPROM## 哪里购买

机体可通过以下链接购买：[Crazyflie 2.1](https://store.bitcraze.io/products/crazyflie-2-1) (store.bitcraze.io)

有用的外设硬件包括：

- [Crazyradio PA 2.4 GHz USB 适配器](https://store.bitcraze.io/collections/kits/products/crazyradio-pa)：_QGroundControl_ 与 Crazyflie 2.0 之间的无线通信
- [Breakout deck](https://store.bitcraze.io/collections/decks/products/breakout-deck)：用于连接新外设的扩展板
- [Flow deck v2](https://store.bitcraze.io/collections/decks/products/flow-deck-v2)：光学流传感器和距离传感器，用于高度和位置控制
- [Z-ranger deck v2](https://store.bitcraze.io/collections/decks/products/z-ranger-deck-v2)：高度控制距离传感器（与 Flow deck 使用相同传感器）
- [Multi-ranger deck](https://store.bitcraze.io/collections/decks/products/multi-ranger-deck) 多向物体检测
- [Buzzer deck](https://store.bitcraze.io/collections/decks/products/buzzer-deck) 系统事件音频反馈，如低电量或充电完成
- [Breakout deck](https://store.bitcraze.io/collections/decks/products/breakout-deck)：无需焊接即可轻松测试新硬件的扩展板
- [SD-card deck](https://store.bitcraze.io/collections/decks/products/sd-card-deck)：将数据高速记录到 micro SD 卡的机载日志功能
- [Logitech Joystick](https://support.logi.com/hc/en-us/articles/360024326793--Getting-Started-Gamepad-F310)

## 组装 Crazyflie 2.1

- [Bitcraze crazyflie 2.1 getting started](https://www.bitcraze.io/documentation/tutorials/getting-started-with-crazyflie-2-x/).## 烧录 PX4

::: info
这些说明仅在 Ubuntu 上测试过。
:::

在设置好 PX4 开发环境后，按照以下步骤将 PX4 自主飞行控制器安装到 Crazyflie 2.1：

1. 下载 PX4 引导程序源代码：

   ```sh
   git clone https://github.com/PX4/Bootloader.git --recurse-submodules
   ```

1. 进入源代码顶层目录并使用以下命令编译：

   ```sh
   make crazyflie21_bl
   ```

1. 按照以下步骤将 Crazyflie 2.1 进入 DFU 模式：
   - 确保其最初处于断电状态。
   - 确保电池已断开。
   - 按住重置按钮（见下图...）。
     ![Crazyflie2 Reset Button](../../assets/flight_controller/crazyflie/crazyflie_reset_button.jpg)
   - 插入电脑的 USB 端口。
   - 一秒钟后，蓝色 LED 应开始闪烁，5 秒后闪烁速度加快。
   - 松开按钮。
1. 安装 _dfu-util_：

   ```sh
   sudo apt-get update
   sudo apt-get install dfu-util
   ```

1. 使用 _dfu-util_ 烧录引导程序，完成时拔下 Crazyflie 2.1：

   ```sh
   sudo dfu-util -d 0483:df11 -a 0 -s 0x08000000 -D ./build/crazyflie21_bl/crazyflie21_bl.bin
   ```

   上电后 Crazyflie 2.1 的黄色 LED 应开始闪烁。

1. 下载 PX4 自主飞行控制器源代码：

   ```sh
   git clone https://github.com/PX4/PX4-Autopilot.git
   ```

1. 进入源代码顶层目录并使用以下命令编译：

   ```sh
   cd PX4-Autopilot/
   make bitcraze_crazyflie21_default upload
   ```

1. 当提示插入设备时，插入 Crazyflie 2.1。
   黄色 LED 应开始闪烁表示进入引导模式。
   随后红色 LED 亮起表示烧录过程已开始。
1. 等待完成。
1. 完成！使用 [QGroundControl](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors.html) 校准传感器。

## 刷写原始Bitcraze固件

1. 下载最新的[Crazyflie 2.1 引导加载程序](https://github.com/bitcraze/crazyflie2-stm-bootloader/releases)
1. 按照以下步骤将 Crazyflie 2.1 进入 DFU 模式：
   - 确保机体初始未通电。
   - 确保电池已断开。
   - 按住复位按钮。
   - 连接到电脑的 USB 接口。
   - 约1秒后，蓝色LED开始闪烁，5秒后闪烁频率加快。
   - 松开按钮。
1. 使用 _dfu-util_ 刷写引导程序，完成后拔下 Crazyflie 2.1：

   ```sh
   sudo dfu-util -d 0483:df11 -a 0 -s 0x08000000 -D cf2loader-1.0.bin
   ```

   上电后，Crazyflie 2.1 的黄色LED应开始闪烁。

1. 使用 [此教程](https://www.bitcraze.io/documentation/tutorials/getting-started-with-crazyflie-2-x/#update-fw) 安装最新的 Bitcraze Crazyflie 2.1 固件。

## 无线设置说明

机载nRF模块允许通过蓝牙或专有的2.4GHz Nordic ESB协议连接到开发板。

- 推荐使用[Crazyradio PA](https://www.bitcraze.io/crazyradio-pa/)。
- 若需立即飞行Crazyflie 2.1，可通过蓝牙使用Crazyflie手机应用。

通过**MAVLink**连接：

- 使用Crazyradio PA搭配兼容的地面控制站。
- 下载_crazyflie-lib-python_源代码：

  ```sh
  git clone https://github.com/bitcraze/crazyflie-lib-python.git
  ```

::: info
我们将使用[cfbridge.py](https://github.com/bitcraze/crazyflie-lib-python/blob/master/examples/cfbridge.py)在Crazyflie 2.1（烧录PX4固件）与QGroundControl之间建立无线MAVlink通信链路。_cfbridge_使QGroundControl能够与crazyradio PA通信。
目前基于C语言的[cfbridge](https://github.com/dennisss/cfbridge)存在数据丢失问题，因此选择使用**cfbridge.py**。
:::

- 确保已为USB Radio设置udev权限。操作步骤请参考[此处](https://www.bitcraze.io/documentation/repository/crazyflie-lib-python/master/installation/usb_permissions/)并**重启**计算机。
- 通过USB连接Crazyradio PA。
- 使用以下方法构建[虚拟环境（本地Python环境）](https://virtualenv.pypa.io/en/latest/)及依赖包：

  ```sh
  pip install tox --user
  ```

- 进入crazyflie-lib-python文件夹并执行：

  ```sh
  make venv
  ```

- 激活虚拟环境：

  ```sh
  source venv/bin/activate
  ```

- 安装依赖项：

  ```sh
  pip install cflib
  pip install -r requirements.txt
  ```

通过以下步骤使用crazyradio连接Crazyflie 2.1：

- 关闭并重新启动Crazyflie 2.1，等待其启动完成。
- 通过USB连接Crazyflie无线电设备。
- 进入crazyflie-lib-python文件夹。
- 激活环境：

  ```sh
  source venv/bin/activate
  ```

- 进入示例文件夹：

  ```sh
  cd examples

  ```

- 启动cfbridge：

  ```sh
  python cfbridge.py
  ```

  ::: info
  _cfbridge_默认尝试在通道80和地址0xE7E7E7E7E7上初始化无线电链路通信。
  如果在同一房间内使用[多个crazyflies和/或crazyradios](https://github.com/dennisss/cfbridge/blob/master/index.md#advanced-swarming)，并希望为每个设备使用不同的通道和/或地址，首先通过USB线连接crazyflie和QGroundControl，并在QGroundControl中更改syslink参数（通道、地址）。
  接着，通过分别提供相同通道和地址作为第一个和第二个参数来启动cfbridge，例如：`python cfbridge.py 90 0x0202020202`
  :::

- 打开QGroundControl。
- 使用完_cfbridge_后，若已激活虚拟环境，可通过按`CTRL+z`关闭。
  通常情况下，从同一终端重新启动_cfbridge_无法连接到crazyflie，可通过关闭终端并在新终端中重新启动_cfbridge_解决此问题。

:::tip
如果修改了[crazyflie-lib-python](https://github.com/bitcraze/crazyflie-lib-python)中的任何驱动程序，或者在新终端中启动_cfbridge_时找不到crazyflie，可以尝试进入crazyflie-lib-python文件夹并运行以下脚本重新构建cflib。

```sh
make venv
```

:::

::: info
QGC中的操纵杆菜单仅在将控制器连接到PC后才会出现（例如Playstation 3控制器）。

![QGC操纵杆菜单](../../assets/flight_controller/crazyflie21/joystick_menu_qgc.png)
:::

## 硬件设置

Crazyflie 2.1 仅能在 [稳定模式](../flight_modes_mc/manual_stabilized.md) 下飞行。

为了记录飞行数据，可以将 SD 卡扩展板安装在 Crazyflie 顶部，如下图所示：

![Crazyflie SDCard](../../assets/flight_controller/crazyflie21/crazyflie21_sd.jpeg)

## 使用 FrSky Taranis 遥控器作为操纵杆

如果你已拥有 Taranis 遥控器并希望将其用作控制器，可以配置为 USB 操纵杆：

- 在 Taranis 中创建新模型。

  ![Taranis - 新模型](../../assets/flight_controller/crazyflie/taranis_model.jpg)

- 在 _MODEL SETUP_ 菜单页面，关闭内部和外部 TX 模块。

  ![Taranis - 模型设置](../../assets/flight_controller/crazyflie/taranis_model_setup.jpg)

- 在 _OUTPUTS_ 菜单页面（在部分 Taranis 遥控器中称为“SERVOS”页面），反转油门（CH1）和副翼（CH3）。

  ![Taranis - 输出设置](../../assets/flight_controller/crazyflie/taranis_outputs.jpg)

要使用 Taranis 开关进行启动/关闭和切换飞行模式：

- 在 Taranis UI 的 _MIXER_ 菜单页面，可将开关分配到 9-16 通道范围内的任意通道，这些通道对应 QGroundControl 操纵杆设置中的按钮 0-7。例如，Taranis 的“SD”开关可设置为 Taranis UI 中的 9 号通道：

  ![Taranis 开关设置](../../assets/flight_controller/crazyflie/taranis_switch_setup.jpg)

- 使用 USB 线将 Taranis 连接到 PC 并打开 QGroundControl。
- 在 QGroundControl 操纵杆设置中，当切换开关时，按钮会变为黄色。例如，Taranis 的 9 号通道对应 QGroundControl 操纵杆设置中的按钮 0。你可以为此按钮分配任意模式，例如 _Altitude_ 模式。现在当将“SD”开关下压时，飞行模式将切换为 _Altitude_。

  ![操纵杆设置](../../assets/flight_controller/crazyflie/crazyflie_QGCjoystick_setup.png)

### ROS

要通过 MAVROS 连接到 Crazyflie 2.1：

- 按上述说明启动 _cfbridge_。
- 更改 QGroundControl 监听的 UDP 端口：
  - 在 QGroundControl 中，导航到 **Application Settings > General**，取消勾选 _Autoconnect to the following devices_ 下的所有复选框。
  - 在 **Comm Links** 中添加一个类型为 _UDP_ 的链接，勾选 _Automatically Connect on Start_ 选项，将 _Listening Port_ 更改为 14557，添加目标主机：127.0.0.1，然后点击 **OK**。
- 确保已安装 [MAVROS](https://github.com/mavlink/mavros/tree/master/mavros#installation)。
- 使用以下命令启动 MAVROS：

  ```sh
  roslaunch mavros px4.launch fcu_url:="udp://:14550@127.0.0.1:14551" gcs_url:="udp://@127.0.0.1:14557"
  ```

- 如果 QGroundControl 未连接，请重新启动。

## 飞行

<lite-youtube videoid="0qy7O3fVN2c" title="Crazyflie 2.1 - PX4 Firmware (stabilized mode)"/>