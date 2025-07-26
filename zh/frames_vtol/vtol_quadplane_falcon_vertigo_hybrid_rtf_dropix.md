

# 已停产：Falcon Vertigo Hybrid VTOL 开箱即飞套件（Dropix）

:::warning 已停产
该机体所基于的Falcon Venturi FPV Wing机架已不再提供。
:::

*Falcon Vertigo Hybrid VTOL* 是一款专为与PX4和Dropix（Pixhawk兼容）飞控协同工作的四旋翼混合垂直起降飞行器，可搭载小型GoPro相机。

开箱即飞套件包含完整系统所需的所有组件，仅需额外准备遥控接收机和数传模块。  
所有组件也可单独购买。

关键信息：

- **机架**：Falcon Vertigo Hybrid VTOL  
- **飞控系统**：Dropix  
- **翼展**：1.3m  

![Falcon Vertigo Hybrid VTOL 开箱即飞套件](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_complete.jpg)

## 材料清单

RTF套件几乎包含了所有所需组件（下述组件旁的链接可供单独购买）：

* 预层压EPP机翼
* 机翼尖端及全套硬件
* Dropix飞控（已停产），包含  
  * GPS u-blox M8N  
  * 电源传感器  
  * [空速传感器](https://store-drotek.com/793-digital-differential-airspeed-sensor-kit-.html)  
* 四旋翼动力套装 [Tiger Motor MT-2216-11 900kv V2](https://www.getfpv.com/tiger-motor-mt-2216-11-900kv-v2.html)（已停产）  
* 4 x 10”x 5”螺旋桨（四旋翼电机）  
* 4 x [25A电调](http://www.getfpv.com/tiger-motor-flame-25a-esc.html)  
* 1 x 10”x 5”螺旋桨（推力电机）  
* 1 x 30A电调  
* 推力电机动力系统  
* 碳纤维管材及安装支架  
* G10电机支架  
* 1 x [3700mAh 4S 30C 锂聚合物电池](https://www.overlander.co.uk/batteries/lipo-batteries/power-packs/3700mah-4s-14-8v-25c-lipo-battery-overlander-sport.html)  
* Dropix电源分配板及线缆  

该套件不含遥控接收机或（可选的）数传模块。  
本组装使用了以下组件：

- 接收机：[FrSSKY D4R-II](https://www.frsky-rc.com/product/d4r-ii/)  
- 数传：[Holybro 100mW 915MHz模块](https://www.getfpv.com/holybro-100mw-fpv-transceiver-telemetry-radio-set-915mhz.html)（已停产）

## 所需工具

组装该机体时使用的工具如下：

* 十字螺丝刀  
* 5.5 mm 六角套筒螺丝刀  
* 电线剪  
* 电烙铁和焊锡  
* 爱好用不锈钢镊子  
* 猴子胶（Gorilla glue）  
* 玻璃纤维加强胶带  

![构建工具](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_build_tools.jpg)

## 组装步骤

开箱即飞套件需要以下组装步骤。

### 步骤1：安装电机支架

1. 按照所示方式在机翼支架内涂抹 gorilla 胶水。

   ![在机翼支架上添加胶水](../../assets/airframes/vtol/falcon_vertigo/wing_brackets_glue.jpg)
   
1. 将碳管安装到支架中。支架和碳管必须通过白色标记对齐（如图片所示）。

   ::: info
   这非常关键，因为白色标记指示了重心位置。
   :::

   <img src="../../assets/airframes/vtol/falcon_vertigo/carbon_tube_in_brackets.jpg" title="支架中的碳管" width="300px" />

1. 以下图片展示了从其他视角的杆件对齐情况：

   ![多旋翼电机框架杆件底部视角对齐](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_9_bottom_view_rod_alignment.jpg)
   ![多旋翼电机框架杆件对齐示意图](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_11_rod_alignment_schamatic.jpg)

### 第2步：安装机翼

1. 将两根碳管插入机身。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_15_fuselage_tubes.jpg" width="500px" title="Fuselage carbon tubes" />

1. 在每根管子的两个白色标记处涂抹 gorilla glue（红色箭头指示位置）。中心的白色标记（蓝色箭头）应位于机身中心，其他标记位于两侧。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_13_rod_apply_glue.jpg" width="500px" title="Apply glue to rod" />
 
1. 碳管插入机身内部后，在管子剩余部分涂抹 gorilla glue 并安装机翼。

1. 机身有两处用于电机和舵机线缆的孔位。将线缆穿过这些孔位后再将机翼与机身连接。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_17_fuselage_holes_cables.jpg" width="500px" title="Fuselage holes for cables" />
 
1. 在机身内部，将机翼穿过机身的信号线通过提供的连接器连接到 ESC。电调已连接到电机并设置为正确的旋转顺序（后续步骤需要将 ESC PDB 连接到电源模块）。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_19_connect_esc_power_and_signal_cables.jpg" width="500px" title="Connect ESC power and signal cables" />
 
1. 与 ESC 类似，舵机已安装完毕。将机翼（穿过机身）的信号线连接到飞控器。  

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_21_connect_servo_cables.jpg" width="500px" title="Connect servo cables" />
   
1. 重复这些步骤安装另一侧的机翼。

### 第3步：连接电子设备 

该套件包含预先连接了大部分所需电子设备的 Dropix 飞行控制器（如果您使用其他与 Pixhawk 兼容的飞行控制器，连接方式类似）。

<img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_23_dropix_and_other_electronics.jpg" width="500px" title="Falcon Vertigo 电子设备" />

::: info
有关连接 Dropix 的一般信息，请参见 [Dropix Flight Controller](../flight_controller/dropix.md)。
:::

#### 连接ESC电源接口并将信号线连接到飞控

1. 使用XT60连接器将ESC连接到电源模块

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_25_aileron_esc_connections.jpg" width="500px" title="" />
   
1. 将信号线接入飞控

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_27_gps_esc_servo_connections.jpg" width="500px" title="GPS, ESC, Servo connections" />

#### 电机接线

电机和舵机的接线方式几乎完全由您决定，但应匹配[通用标准VTOL](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)配置，如机架参考文档所示。几何结构和输出分配可通过[执行器配置](../config/actuators.md#actuator-outputs)进行设置。

例如，您可以按照如下示例进行接线（以"坐在飞行器中"的视角为准）：

端口 | 连接
--- | ---
MAIN 1   | 前右电机，CCW
MAIN 2   | 后左电机，CCW
MAIN 3   | 前左电机，CW
MAIN 4   | 后右电机，CW
AUX  1   | 左副翼
AUX  2   | 右副翼 
AUX  3   | 升降舵
AUX  4   | 方向舵
AUX  5   | 油门


<a id="dropix_back"></a>

#### 飞行控制器连接：电机、舵机、遥控接收器、电流传感器

下图显示了dropix飞行控制器的背面，突出显示了用于连接四旋翼电机线缆、副翼信号线缆、油门电机、电流传感器和接收器（RC IN）输入引脚的输出引脚。

<img id="dropix_outputs" src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_33_dropix_outputs.jpg" width="500px" title="Dropix motor/servo outputs" />

1. 连接四旋翼电机信号线缆。

1. 在辅助输出端口连接副翼线缆和油门电机。

1. 从电调将油门电机信号线缆连接到适当的飞控辅助端口。将电调连接到油门电机。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_37_connect_throttle_motor.jpg" width="500px" title="Connect throttle motor" />

1. 连接接收器（RC IN）。


<a id="dropix_front"></a>

#### 飞控连接：遥测、空速传感器、GPS、蜂鸣器和安全开关

传感器输入、遥测、蜂鸣器和安全开关位于飞控的前部，如下方连接图所示。

<img src="../../assets/flight_controller/dropix/dropix_connectors_front.jpg" width="500px" alt="Dropix connectors front" title="Dropix connectors front" />

1. 按照所示连接遥测、空速传感器、GPS、蜂鸣器和安全开关。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_39_connect_sensors.jpg" width="500px" title="Connect sensors" />

#### 飞行控制器：连接电源模块和外部USB

USB端口、电源模块和外部USB的输入接口位于飞行控制器右侧。

1. 按示意图连接电源和USB

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_41_connect_power_module_usb.jpg" width="500px" title="Connect power module and USB" />

:::tip
外部USB是可选的。  
如果飞行控制器安装后难以接触到USB端口，则应使用它。
:::

#### 安装皮托管（空速传感器）

皮托管安装在飞机前部，并通过管子连接到空速传感器。

:::warning
必须确保皮托管的气流不被任何物体阻挡。这对固定翼飞行以及四旋翼到固定翼的转换至关重要。
:::

1. 在飞机前部安装皮托管

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_43_airspeed_sensor_mounting.jpg" width="500px" title="空速传感器安装" />

1. 固定连接软管并确保其没有弯折/压扁。
   
   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_45_airspeed_sensor_tubing.jpg" width="500px" title="空速传感器安装" />
   
1. 将软管连接到空速传感器。 

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_47_connect_airspeed_sensor_tubing.jpg" width="500px" title="连接空速传感器和软管" />

#### 安装/连接接收器和遥测模块

1. 将接收器和遥测模块粘贴在机体框架外侧。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_49_receiver_mounting.jpg" width="500px" title="粘贴接收器" />

1. 将接收器连接到dropix背面的RC IN端口（如上图所示，另见[飞控说明](#dropix_back)）。

1. 将遥测模块连接到飞控正面，如下图所示（详见[飞控说明](#dropix_front)中关于引脚的详细说明）。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_51_telemetry_module_mounting.jpg" width="500px" title="粘贴遥测模块" />


<a id="compass_gps"></a>

#### GPS/Compass模块

GPS/Compass模块已默认方向安装在机翼上，无需进行额外操作！

<img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_gps_compass.jpg" width="500px" title="GPS/Compass" />


<a id="flight_controller_orientation"></a>

#### 安装和调整飞行控制器

1. 将飞行控制器方向设置为270度。
   
   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_53_flight_controller_orientation.jpg" width="500px" title="Flight controller orientation" />
   
1. 使用减震泡沫固定控制器。

### 步骤4：最终组装检查

最终组装步骤是检查机体是否稳定以及电机是否正确设置。

1. 检查电机是否按正确方向旋转（如下方QuadX图示所示）。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_35_quad_motor_directions.png" width="200px" title="四旋翼电机顺序/方向" />

   ::: info
   如果需要，可以通过QGroundControl [执行器输出](../config/actuators.md#actuator-outputs)配置中每个舵机输出的`Rev Range (for servos)`复选框反转舵机方向（仅限舵机）（这会设置[PWM_AUX_REV](../advanced_config/parameter_reference.md#PWM_AUX_REV)或[PWM_AUX_MAIN](../advanced_config/parameter_reference.md#PWM_MAIN_REV)参数）。
   :::
 
1. 检查机体是否围绕预期重心平衡

   * 用手指握住机体的重心位置，检查机体是否保持稳定。

      ![Level Centre of Gravity](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_57_level_centre_of_gravity.jpg)
   
   * 如果机体向前或向后倾斜，调整电机位置使其平衡。
   
      ![Level Motors](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_55_level_motors.jpg)

## 配置

执行正常的[基本配置](../config/index.md)。

注意事项：

1. 对于[Airframe](../config/airframe.md)选择机体组/类型为 *Standard VTOL*，特定机体为[Generic Standard VTOL](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)，如下图所示。

   ![QCG - Select Generic Standard VTOL](../../assets/qgc/setup/airframe/px4_frame_generic_standard_vtol.png)

1. 将[Autopilot Orientation](../config/flight_controller_orientation.md)设置为`ROTATION_YAW_270`，因为飞控安装在机体[侧向](#flight_controller_orientation)。指南针方向为正向，因此可以保留默认值(`ROTATION_NONE`)。
1. 按照[Actuators Configuration](../config/actuators.md)中的说明配置输出和几何参数
1. 默认参数通常足以实现稳定飞行。如需更详细的调参信息，请参见[Standard VTOL Wiring and Configuration](../config_vtol/vtol_quad_configuration.md)。

完成校准后，垂直起降机体即可飞行。

## 视频

<lite-youtube videoid="h7OHTigtU0s" title="PX4 Vtol test"/>

## 支持

如果您有关于VTOL转换或配置的任何问题，请访问<https://discuss.px4.io/c/px4/vtol>。