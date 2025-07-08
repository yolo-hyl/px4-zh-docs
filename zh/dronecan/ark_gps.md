# ARK GPS (DroneCAN)

ARK GPS 是一款开源 [DroneCAN](index.md) [GNSS/GPS](../gps_compass/index.md)、磁力计、IMU、气压计、蜂鸣器和安全开关模块。

![ARK GPS](../../assets/hardware/gps/ark/ark_gps.jpg)

## 购买渠道

可通过以下链接购买该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-gps/) (美国)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_GPS)
- 传感器
  - Ublox M9N GPS
    - 超强米级GNSS定位
    - 最大化位置可用性，可同时接收4个GNSS信号
    - 先进的欺骗和干扰检测
    - 优秀的射频干扰抑制
  - Bosch BMM150 磁力计
  - Bosch BMP388 气压计
  - Invensense ICM-42688-P 6轴IMU
- STM32F412CEU6 微控制器
- 安全按钮
- 蜂鸣器
- 两个Pixhawk标准CAN接口（4针JST GH）
- Pixhawk标准调试接口（6针JST SH）
- 小型化设计
  - 5cm x 5cm x 1cm
- LED指示灯
  - 安全LED
  - GPS定位状态
  - RGB系统状态
- 美国制造
- 电源需求
  - 5V
  - 平均110mA
  - 最大117mA

## 硬件设置

### 接线

使用Pixhawk标准4针JST GH线缆将ARK GPS连接到CAN总线。  
更多信息请参考 [CAN接线](../can/index.md#wiring) 说明。

### 安装

建议的安装方向为：板载接口朝向 **机体后方**。

传感器可安装在机体任意位置，但在 [PX4配置](#px4-configuration) 阶段需要指定其相对于机体重心的位置。

## 固件设置

ARK GPS 运行 [PX4 DroneCAN固件](px4_cannode_fw.md)。  
因此支持通过CAN总线进行固件更新和 [动态节点分配](../dronecan/index.md#node-id-allocation)。

ARK GPS板出厂时已预装最新固件，如需自行构建和烧录最新固件，请参考 [PX4 DroneCAN固件 > 构建固件](px4_cannode_fw.md#building-the-firmware)。

- 固件目标：`ark_can-gps_default`
- 引导程序目标：`ark_can-gps_canbootloader`

## PX4配置

需要设置必要的 [DroneCAN](index.md) 参数，并定义传感器偏移（若传感器未位于机体中心）。  
所需配置步骤如下：

::: info
若飞行控制器上电时未插入SD卡，ARK GPS将无法启动。
:::

### 启用DroneCAN

要使用ARK GPS板，需将其连接到Pixhawk CAN总线，并通过设置参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 为 `2`（动态节点分配）或 `3`（若使用 [DroneCAN电调](../dronecan/escs.md)）来启用DroneCAN驱动。

操作步骤为：
- 在 _QGroundControl_ 中将参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 设置为 `2` 或 `3` 并重启（详见 [查找/更新参数](../advanced_config/parameters.md)）。
- 将ARK GPS的CAN接口连接到Pixhawk的CAN接口。

启用后，模块将在启动时被检测到。GPS数据将以10Hz频率接收。

更多关于PX4中DroneCAN配置的细节请参考 [DroneCAN > 启用DroneCAN](../dronecan/index.md#enabling-dronecan)。

### 传感器位置配置

若传感器未位于机体中心，还需定义传感器偏移：
- 通过设置 [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL) 的第3位为 `true` 启用GPS偏航融合。
- 启用 [UAVCAN_SUB_GPS](../advanced_config/parameter_reference.md#UAVCAN_SUB_GPS)、[UAVCAN_SUB_MAG](../advanced_config/parameter_reference.md#UAVCAN_SUB_MAG) 和 [UAVCAN_SUB_BARO](../advanced_config/parameter_reference.md#UAVCAN_SUB_BARO)。
- 若该节点是CAN总线的最后一个节点，将 [CANNODE_TERM](../advanced_config/parameter_reference.md#CANNODE_TERM) 设置为 `1`。
- 可通过设置 [EKF2_GPS_POS_X](../advanced_config/parameter_reference.md#EKF2_GPS_POS_X)、[EKF2_GPS_POS_Y](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Y) 和 [EKF2_GPS_POS_Z](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Z) 来补偿ARK GPS相对于机体重心的偏移。

## LED含义

在烧录过程中会看到绿色、蓝色和红色LED，运行正常时绿色LED会闪烁。

若看到红色LED表示存在错误，请检查以下内容：
- 确保飞行控制器已安装SD卡。
- 确保在烧录 `ark_can-gps_default` 之前已安装 `ark_can-gps_canbootloader`。
- 删除SD卡根目录和ufw目录下的二进制文件，重新构建并烧录。

## 参见

- [ARK GPS](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-gps) (ARK官方文档)