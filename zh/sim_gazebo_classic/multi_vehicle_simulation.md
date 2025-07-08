# 多机体模拟与Gazebo Classic

本主题说明如何使用 [Gazebo Classic](../sim_gazebo_classic/index.md) 和 SITL（仅限 Linux）模拟多个无人飞行器机体。  
在使用和不使用 ROS 的模拟中采用了不同的方法。

## 多机体仿真（Gazebo Classic）

在 Gazebo Classic 中仿真多个 iris 或 plane 机体时，请在终端中从 _Firmware_ 树根目录执行以下命令：

```sh
Tools/simulation/gazebo-classic/sitl_multiple_run.sh [-m <model>] [-n <number_of_vehicles>] [-w <world>] [-s <script>] [-t <target>] [-l <label>]
```

- `<model>`: 要生成的[机体类型/模型](../sim_gazebo_classic/vehicles.md)，例如：`iris`（默认）、`plane`、`standard_vtol`、`rover`、`r1_rover`、`typhoon_h480`。
- `<number_of_vehicles>`: 生成的机体数量。默认为 3，最大为 254。
- `<world>`: 机体生成的[世界](../sim_gazebo_classic/worlds.md)，例如：`empty`（默认）
- `<script>`: 生成不同类型的多个机体（覆盖 `-m` 和 `-n` 的值）。例如：

  ```sh
  -s "iris:3,plane:2,standard_vtol:3"
  ```

  - 支持的机体类型：`iris`、`plane`、`standard_vtol`、`rover`、`r1_rover`、`typhoon_h480`。
  - 冒号后的数字表示该类型生成的机体数量。
  - 最大机体数量为 254。

- `<target>`: 构建目标，例如：`px4_sitl_default`（默认）、`px4_sitl_nolockstep`
- `<label>`: 模型的特定标签，例如：`rplidar`

每个机体实例会分配一个唯一的 MAVLink 系统 ID（2、3、4 等）。MAVLink 系统 ID 1 被跳过以保持[命名空间](../ros2/multi_vehicle.md#principle-of-operation)的一致性。机体实例通过顺序分配的 PX4 远程 UDP 端口访问：`14541` - `14548`（额外实例均通过相同远程 UDP 端口 `14549` 访问）。

::: info
254 机体限制是由于 mavlink `MAV_SYS_ID` 仅支持同一网络中的 255 个机体（且第一个被跳过）。`MAV_SYS_ID` 在 SITL rcS 中分配：[init.d-posix/rcS](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/rcS#L131)
:::

### 视频：多旋翼（Iris）

<lite-youtube videoid="Mskx_WxzeCk" title="Multiple vehicle simulation in SITL gazebo"/>

### 视频：多固定翼飞机

<lite-youtube videoid="aEzFKPMEfjc" title="PX4 Multivehicle SITL gazebo for fixedwing"/>

### 视频：多 VTOL

<lite-youtube videoid="lAjjTFFZebI" title="PX4 Multivehicle SITL gazebo for VTOL"/>

### 构建与测试（XRCE-DDS）

`Tools/simulation/gazebo-classic/sitl_multiple_run.sh` 可用于在 Gazebo Classic 中通过 XRCE-DDS 仿真多个机体。

::: info
需要安装 XRCE-DDS 依赖项。更多信息请参见：[ROS 2 用户指南（PX4-ROS 2 Bridge）](../ros2/user_guide.md)，用于与 ROS 2 节点交互。
:::

要构建示例设置，请按照以下步骤操作：

1. 克隆 PX4/Firmware 代码，然后构建 SITL 代码：

   ```sh
   cd Firmware_clone
   git submodule update --init --recursive
   DONT_RUN=1 make px4_sitl gazebo-classic
   ```

1. 按照[此处的说明](../ros2/user_guide.md)构建 `micro xrce-dds agent` 和接口包。

1. 运行 `Tools/simulation/gazebo-classic/sitl_multiple_run.sh`。例如，生成 4 个机体，运行：

   ```sh
   ./Tools/simulation/gazebo-classic/sitl_multiple_run.sh -m iris -n 4
   ```

   ::: info
   每个机体实例分配一个唯一的 MAVLink 系统 ID（2、3、4 等）。MAVLink 系统 ID 1 被跳过。
   :::

1. 运行 `MicroXRCEAgent`。它将自动连接到所有四个机体：

   ```sh
   MicroXRCEAgent udp4 -p 8888
   ```

   ::: info
   模拟器启动脚本会自动为每个机体分配[唯一命名空间](../ros2/multi_vehicle.md)。
   :::

## 使用 MAVROS 和 Gazebo Classic 多机体

本示例演示了一个启动 Gazebo Classic 客户端 GUI 的配置，该 GUI 显示一个空世界中的两个 Iris 机体。  
您可以通过 _QGroundControl_ 和 MAVROS 以类似管理单机体的方式控制这些机体。

### 所需条件

- 当前 [PX4 ROS/Gazebo 开发环境](../ros/mavros_installation.md)（包含 [MAVROS 包](http://wiki.ros.org/mavros)）
- 最新 [PX4/PX4-Autopilot](https://github.com/PX4/PX4-Autopilot) 的克隆版本

### 构建与测试

要构建示例配置，请执行以下步骤：

1. 克隆 PX4/PX4-Autopilot 代码并构建 SITL 代码

   ```sh
   cd Firmware_clone
   git submodule update --init --recursive
   DONT_RUN=1 make px4_sitl_default gazebo-classic
   ```

2. 源环境配置：

   ```sh
   source Tools/simulation/gazebo-classic/setup_gazebo.bash $(pwd) $(pwd)/build/px4_sitl_default
   export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd):$(pwd)/Tools/simulation/gazebo-classic/sitl_gazebo
   ```

3. 运行启动文件：

   ```sh
   roslaunch px4 multi_uav_mavros_sitl.launch
   ```

   ::: info
   可在上述 _roslaunch_ 命令中添加 `gui:=false` 以无 UI 模式启动 Gazebo Classic。
   :::

该教程示例会在 Gazebo Classic 客户端 GUI 中显示一个空世界中的两个 Iris 机体。

您可以通过 _QGroundControl_ 或 MAVROS 以类似管理单机体的方式控制机体：

- _QGroundControl_ 将提供下拉菜单选择当前"焦点"机体
- MAVROS 需在主题/服务路径前添加相应命名空间（例如对于 `<group ns="uav1">`，您将使用 _/uav1/mavros/mission/push_）。

### 实现原理？

对于每个模拟机体，需要以下内容：

- **Gazebo Classic 模型**：在 `PX4-Autopilot/Tools/simulation/gazebo-classic/sitl_gazebo-classic/models/rotors_description/urdf/<model>_base.xacro` 中定义为 `xacro` 文件（详见 [此处](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/02060a86652b736ca7dd945a524a8bf84eaf5a05/models/rotors_description/urdf)）。  
  当前假设模型 `xacro` 文件名以 **base.xacro** 结尾。  
  该模型应包含名为 `mavlink_udp_port` 的参数，用于定义 Gazebo Classic 与 PX4 节点通信的 UDP 端口。  
  模型的 `xacro` 文件将用于生成包含所选 UDP 端口的 `urdf` 模型。  
  要定义 UDP 端口，需在启动文件中为每个机体设置 `mavlink_udp_port`（示例见 [此处](https://github.com/PX4/PX4-Autopilot/blob/4d0964385b84dc91189f377aafb039d10850e5d6/launch/multi_uav_mavros_sitl.launch#L37)）。

  ::: info
  如果使用相同机体模型，无需为每个机体单独创建 `xacro` 文件。
  :::

- **PX4 SITL 节点**：通过 `single_vehicle_spawn.launch` 启动，包含位置、姿态、模型路径等参数（详见代码块）。
- **MAVROS 节点**：通过 `px4.launch` 启动，包含通信端口和目标系统ID等参数（详见代码块）。

  ::: info
  每个机体的完整配置块被 `<group>` 标签包裹以隔离 ROS 命名空间。
  :::

要在此模拟中添加第三个 Iris 机体，需考虑两个主要组件：

- 在 **multi_uav_mavros_sitl.launch** 中添加 `UAV3`  
  - 复制现有机体（`UAV1` 或 `UAV2`）的 `<group>` 块  
  - 将 `ID` 参数递增至 `3`  
  - 为 `mavlink_udp_port` 选择不同的端口以通信 Gazebo Classic  
  - 通过修改 `fcu_url` 参数中的端口号选择 MAVROS 通信端口
- 创建启动文件并进行以下修改：
  - 复制现有 Iris rcS 启动文件（`iris_1` 或 `iris_2`）并重命名为 `iris_3`  
  - 将 `MAV_SYS_ID` 值设为 `3`  
  - 将 `SITL_UDP_PRT` 值与 `mavlink_udp_port` 启动参数匹配  
  - 将第一个 `mavlink start` 端口和 `mavlink stream` 端口值设为 QGC 通信所需值  
  - 将第二个 `mavlink start` 端口与启动文件 `fcu_url` 参数中使用的端口匹配

  ::: info
  注意区分不同端点的源端口（src）和目标端口（dst）。
  :::

## 使用 SDF 模型的多机体

本节展示了开发者如何通过 Gazebo Classic SDF 文件中定义的机体模型（而非本主题其余部分讨论的 ROS Xacro 文件中定义的模型）来模拟多个机体。

步骤如下：

1. 从 Linux 终端安装 _xmlstarlet_：

   ```sh
   sudo apt install xmlstarlet
   ```

1. 使用 _roslaunch_ 并指定 **multi_uav_mavros_sitl_sdf.launch** 启动文件：

   ```sh
   roslaunch multi_uav_mavros_sitl_sdf.launch vehicle:=<model_file_name>
   ```

   ::: info
   注意机体模型文件名参数是可选的（`vehicle:=<model_file_name>`）；如果省略，将默认使用 [plane 模型](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/master/models/plane)。
   :::
   ```

这种方法与使用 xacro 类似，区别在于 SITL/Gazebo Classic 端口号由 _xmstarlet_ 自动插入到每个生成的机体中，无需在 SDF 文件中指定。

要添加新机体，需要确保模型可被找到（以便在 Gazebo Classic 中生成），且 PX4 需要具有相应的启动脚本。

1. 您可以任选以下方式之一：

   - 修改 **single_vehicle_spawn_sdf.launch** 文件，通过修改以下行指向您的模型位置：

     ```sh
     $(find px4)/Tools/simulation/gazebo/sitl_gazebo-classic/models/$(arg vehicle)/$(arg vehicle).sdf
     ```

     ::: info
     即使您硬编码模型路径，也要确保设置 `vehicle` 参数。
     :::

   - 将您的模型复制到上述文件夹中（遵循相同的路径约定）。

1. `vehicle` 参数用于设置 `PX4_SIM_MODEL` 环境变量，该变量由默认的 rcS（启动脚本）用来查找模型的对应启动设置文件。
   在 PX4 中，这些启动文件位于 **PX4-Autopilot/ROMFS/px4fmu_common/init.d-posix/** 目录。
   例如，这是 plane 模型的 [启动脚本](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/airframes/1030_gazebo-classic_plane)。
   为了使其生效，启动文件中的 PX4 节点会传递参数指定 _rcS_ 文件（**etc/init.d/rcS**）和 rootfs 等目录的位置（`$(find px4)/build_px4_sitl_default/etc`）。
   出于简便考虑，建议将模型的启动文件与 PX4 的启动文件一起放置在 **PX4-Autopilot/ROMFS/px4fmu_common/init.d-posix/** 中。

## 附加资源

- 查看 [Simulation](../simulation/index.md) 了解UDP端口配置的说明。
- 查看 [URDF in Gazebo](http://wiki.ros.org/urdf/Tutorials/Using%20a%20URDF%20in%20Gazebo) 了解使用xacro生成模型的更多信息。
- 查看 [RotorS](https://github.com/ethz-asl/rotors_simulator/tree/master/rotors_description/urdf) 获取更多xacro模型。