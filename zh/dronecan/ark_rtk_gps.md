# ARK RTK GPS

[ARK RTK GPS](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-rtk-gps) 是一款开源的 [DroneCAN](index.md) [RTK GPS](../gps_compass/rtk_gps.md)，其基于 [u-blox F9P](https://www.u-blox.com/en/product/zed-f9p-module) 模块，集成了磁力计、气压计、IMU、蜂鸣器和安全开关模块。

![ARK RTK GPS](../../assets/hardware/gps/ark/ark_rtk_gps.jpg)

## 购买渠道

购买此模块请从以下渠道：

- [ARK Electronics](https://arkelectron.com/product/ark-rtk-gps/) (US)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_RTK_GPS)
- 传感器
  - Ublox F9P GPS
    - 多频段GNSS接收器可在数秒内实现厘米级精度
    - 同时接收GPS、GLONASS、Galileo和BeiDou卫星信号
    - 多频段RTK具备快速收敛时间和可靠性能
    - 高更新率适用于高动态应用
    - 小型化低功耗模块实现厘米级精度
  - Bosch BMM150 磁力计
  - Bosch BMP388 气压计
  - Invensense ICM-42688-P 六轴IMU
- STM32F412CEU6 微控制器
- 安全按钮
- 蜂鸣器
- 两个Pixhawk标准CAN接口（4针JST GH）
- F9P "UART 2" 接口
  - 3针JST GH
  - TX, RX, GND
- Pixhawk标准调试接口（6针JST SH）
- LED指示灯
  - 安全LED
  - GPS定位状态
  - RTK状态
  - RGB系统状态
- 美国制造
- 供电需求
  - 5V
  - 平均170mA
  - 最大180mA## 硬件设置

### 接线

ARK RTK GPS通过Pixhawk标准的4针JST GH电缆连接到CAN总线。更多信息请参考[CAN接线](../can/index.md#wiring)指南。

### 安装

推荐安装方向为使电路板的连接器朝向**机体尾部**。

该传感器可以安装在机体框架的任何位置，但需要在[PX4配置](#px4-configuration)时指定其相对于机体重心的位置。

## 固件设置

ARK RTK GPS 运行 [PX4 cannode 固件](px4_cannode_fw.md)。因此，它支持通过 CAN 总线进行固件更新和 [动态节点分配](index.md#node-id-allocation)。

ARK RTK GPS 板出厂时预装了最新的固件，但如果您希望自行构建和烧录最新固件，请参考 [cannode 固件构建说明](px4_cannode_fw.md#building-the-firmware)。

固件目标: `ark_can-rtk-gps_default`  
Bootloader目标: `ark_can-rtk-gps_canbootloader`## 飞行控制器设置

### 启用DroneCAN

为了使用ARK RTK GPS，需将其连接到Pixhawk的CAN总线，并通过设置参数[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)为`2`启用动态节点分配（或`3`若使用[DroneCAN ESC](../dronecan/escs.md)）。

操作步骤：

- 在_QGroundControl_中设置参数[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)为`2`或`3`并重启（详情见[查找/更新参数](../advanced_config/parameters.md)）。
- 将ARK RTK GPS的CAN接口连接到Pixhawk的CAN。

启用后，模块将在启动时被检测到。
GPS数据将以10Hz频率接收。

### PX4配置

需要设置必要的[DroneCAN](index.md)参数，并在传感器未位于机体中心时定义偏移量：

- 通过设置[EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)的bit 3为true，启用GPS航向融合。
- 通过设置[SENS_GPS_MASK](../advanced_config/parameter_reference.md#SENS_GPS_MASK)为7（所有三个bit位为true），启用GPS数据混合以确保航向始终发布。
- 启用[UAVCAN_SUB_GPS](../advanced_config/parameter_reference.md#UAVCAN_SUB_GPS)、[UAVCAN_SUB_MAG](../advanced_config/parameter_reference.md#UAVCAN_SUB_MAG)和[UAVCAN_SUB_BARO](../advanced_config/parameter_reference.md#UAVCAN_SUB_BARO)。
- 若ARK RTK GPS与机体重心存在偏移，可通过设置[EKF2_GPS_POS_X](../advanced_config/parameter_reference.md#EKF2_GPS_POS_X)、[EKF2_GPS_POS_Y](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Y)和[EKF2_GPS_POS_Z](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Z)进行补偿。
- 若GPS是CAN总线的最后一个节点，需设置[CANNODE_TERM](../advanced_config/parameter_reference.md#CANNODE_TERM)为`1`。

### 设置移动基线与GPS航向

通过CAN总线使用两个ARK RTK GPS模块设置移动基线和GPS航向是最简单的方式，也可通过UART连接以减少CAN总线流量。

注意：仅当Rover处于RTX Fixed模式时才会输出航向，在RTK Float模式下不会输出航向。

通过CAN总线设置：

- 确保ARK RTK GPS模块通过CAN总线连接到Pixhawk（一个模块可连接到另一个模块的备用CAN端口）。两个模块需连接到同一CAN总线以传输校正数据。
- 选择一个ARK RTK GPS作为_Rover_，另一个作为_Moving Base_。
- 重新打开QGroundControl，进入参数设置，选择`Standard`隐藏下拉菜单，选择`Component ##`查看每个ARK RTK GPS的CAN节点参数
  ::: info
  若在打开QGroundControl前未连接ARK RTK GPS到Pixhawk，`Component ##`将不可见。
  :::
- 在_Rover_上设置：
  - [GPS_UBX_MODE](../advanced_config/parameter_reference.md#GPS_UBX_MODE)设置为`3`
  - [GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET)根据_Rover_与_Moving Base_的相对位置设置为：
    - `0`（_Rover_在_Moving Base_前方）
    - `90`（_Rover_在_Moving Base_右侧）
    - `180`（_Rover_在_Moving Base_后方）
    - `270`（_Rover_在_Moving Base_左侧）
  - [CANNODE_SUB_MBD](../advanced_config/parameter_reference.md#CANNODE_SUB_MBD)设置为`1`。
- 在_Moving Base_上设置：
  - [GPS_UBX_MODE](../advanced_config/parameter_reference.md#GPS_UBX_MODE)设置为`4`
  - [CANNODE_PUB_MBD](../advanced_config/parameter_reference.md#CANNODE_PUB_MBD)设置为`1`

通过UART设置：

- 确保ARK RTK GPS模块通过CAN总线连接到Pixhawk。
- 通过UART2端口将两个ARK RTK GPS模块互连（UART2引脚定义如下）。
  注意：一个模块的TX需连接到另一个模块的RX。

| 引脚 | 名称 |
| --- | ---- |
| 1   | TX   |
| 2   | RX   |
| 3   | GND  |

- 在_Rover_上设置：
  - [GPS_UBX_MODE](../advanced_config/parameter_reference.md#GPS_UBX_MODE)设置为`1`
  - [GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET)根据_Rover_与_Moving Base_的相对位置设置为：
    - `0`（_Rover_在_Moving Base_前方）
    - `90`（_Rover_在_Moving Base_右侧）
    - `180`（_Rover_在_Moving Base_后方）
    - `270`（_Rover_在_Moving Base_左侧）
- 在_Moving Base_上设置：
  - [GPS_UBX_MODE](../advanced_config/parameter_reference.md#GPS_UBX_MODE)设置为`2`## LED指示灯

- GPS状态指示灯位于连接器右侧

  - 绿色闪烁表示GPS定位成功
  - 蓝色闪烁表示接收到差分信号和RTK浮点定位
  - 蓝色常亮表示RTK固定解

- CAN状态指示灯位于连接器左上方
  - 绿色慢闪表示等待CAN连接
  - 绿色快闪表示正常运行
  - 绿色和蓝色慢闪表示CAN总线枚举
  - 绿色、蓝色和红色慢闪表示固件更新中
  - 红色闪烁表示错误
    - 如果看到红色LED，请检查以下内容：
      - 确保飞控板已安装SD卡
      - 在烧录`ark_can-rtk-gps_default`前，确保ARK RTK GPS已安装`ark_can-rtk-gps_canbootloader`
      - 清除SD卡根目录和ufw目录下的二进制文件，重新构建并烧录

### 更新Ublox F9P模块

ARK RTK GPS配备的Ublox F9P模块固件版本为1.13或更高。您也可以检查版本并按需更新固件。

操作步骤：

- [从u-blox.com下载u-center](https://www.u-blox.com/en/product/u-center) 并安装到您的PC（仅限Windows）
- 打开 [u-blox ZED-F9P官网](https://www.u-blox.com/en/product/zed-f9p-module#tab-documentation-resources)
- 向下滚动并点击 "Show Legacy Documents" 复选框
- 再次向下滚动到 Firmware Update 并下载所需固件（至少需要1.13版本）
- 长按ARK RTK GPS的安全开关，通过CAN端口供电，直至三个LED快速闪烁
- 通过调试端口（如Black Magic Probe或FTDI线缆）将ARK RTK GPS连接到PC
- 打开u-center，选择ARK RTK GPS的COM端口并连接
  ![U-Center连接](../../assets/hardware/gps/ark/ark_rtk_gps_ucenter_connect.png)
- 点击查看当前固件版本：View → Messages View → UBX → MON → VER
  ![检查版本](../../assets/hardware/gps/ark/ark_rtk_gps_ublox_version.png)
- 更新固件：
  - 点击 Tools → Firmware Update
  - 固件镜像字段应为从u-blox ZED-F9P官网下载的.bin文件
  - 勾选 "Use this baudrate for update" 并选择115200
  - 确保其他复选框如图所示
  - 点击左下角的绿色GO按钮
  - 成功更新后应显示 "Firmware Update SUCCESS"
    ![固件更新](../../assets/hardware/gps/ark/ark_rtk_gps_ublox_f9p_firmware_update.png)

## 另请参见

- [ARK RTK GPS 文档](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-rtk-gps) (ARK Docs)