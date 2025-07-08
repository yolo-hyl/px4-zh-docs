# 已停产：Falcon Vertigo Hybrid VTOL RTF（Dropix）

:::warning 已停产
该机体所基于的Falcon Venturi FPV机翼框架已不可用。
:::

*Falcon Vertigo Hybrid VTOL* 是一款专为与PX4及Dropix（兼容Pixhawk）飞控配合使用的四旋翼混合垂直起降飞行器，可搭载小型GoPro相机。

RTF套件包含除遥控接收机和数传模块外的完整系统所需组件。
组件也可单独购买。

关键信息：

- **框架**：Falcon Vertigo Hybrid VTOL
- **飞控**：Dropix
- **翼展**：1.3m

![Falcon Vertigo Hybrid VTOL RTF](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_complete.jpg)

## 材料清单

RTF套件几乎包含了所有所需组件（下方组件旁的链接可单独购买任何部件）：

* 预层压EPP机翼
* 机翼尖端和全套硬件
* Dropix飞控（已停产）包含：
  * GPS u-blox M8N
  * 电源传感器  
  * [空速传感器](https://store-drotek.com/793-digital-differential-airspeed-sensor-kit-.html)
* 四旋翼动力套装 [Tiger Motor MT-2216-11 900kv V2](https://www.getfpv.com/tiger-motor-mt-2216-11-900kv-v2.html)（已停产）
* 4 x 10”x 5”螺旋桨（四旋翼电机）
* 4 x [25A电调](http://www.getfpv.com/tiger-motor-flame-25a-esc.html)
* 1 x 10” x 5”螺旋桨（推进电机）
* 1 x 30A电调
* 推进电机动力系统
* 碳纤维管和安装件
* G10电机支架
* 1 x [3700mAh 4S 30C 锂聚合物电池](https://www.overlander.co.uk/batteries/lipo-batteries/power-packs/3700mah-4s-14-8v-25c-lipo-battery-overlander-sport.html)
* Dropix电源分配板和电缆

该套件不含遥控接收机或（可选）数传模块。本制作使用了以下组件：

- 接收机：[FrSSKY D4R-II](https://www.frsky-rc.com/product/d4r-ii/)
- 数传：[Holybro 100mW 915MHz模块](https://www.getfpv.com/holybro-100mw-fpv-transceiver-telemetry-radio-set-915mhz.html)（已停产）## 所需工具

组装机体使用了以下工具：

* 十字螺丝刀
* 5.5 mm 六角套筒螺丝刀
* 电线剪
* 电烙铁和焊锡
* 摄影用不锈钢镊子
* Gorilla胶
* 玻璃纤维增强胶带

![组装工具](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_build_tools.jpg)

## 组装步骤

该RTF套件需要执行以下组装流程。

### 步骤1：安装电机支架

1. 如图所示，在机翼支架内部涂抹大猩猩胶水。

   ![在机翼支架上涂抹胶水](../../assets/airframes/vtol/falcon_vertigo/wing_brackets_glue.jpg)
   
1. 将碳素管插入支架。支架和管必须使用白色标记对齐（如图所示）。

   ::: info
   这非常关键，因为白色标记指示了重心位置。
   :::

   <img src="../../assets/airframes/vtol/falcon_vertigo/carbon_tube_in_brackets.jpg" title="支架中的碳素管" width="300px" />

1. 下图展示了杆件从其他视角的对齐方式：

   ![四旋翼电机框架杆件底部视角对齐](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_9_bottom_view_rod_alignment.jpg)
   ![四旋翼电机框架杆件对齐示意图](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_11_rod_alignment_schamatic.jpg)


### 步骤2：安装机翼
 
1. 将两个碳素管插入机身。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_15_fuselage_tubes.jpg" width="500px" title="机身碳素管" />

1. 在每根管的两个白色标记之间涂抹大猩猩胶水（由红箭头指示）。中心的白色标记（蓝箭头）将放置在机身中央，其他标记位于两侧。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_13_rod_apply_glue.jpg" width="500px" title="在杆件上涂抹胶水" />
   
1. 确保控制器固定到位并使用减震泡沫垫。

### 步骤3：最终组装检查

最终组装步骤是检查机体稳定性及电机设置是否正确。

1. 检查电机旋转方向是否符合下方QuadX图示。

   <img src="../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_35_quad_motor_directions.png" width="200px" title="四旋翼电机顺序/方向" />

   ::: info
   如有必要，可通过QGroundControl [Actuator Output](../config/actuators.md#actuator-outputs) 配置中每个舵机输出的`Rev Range (for servos)`复选框反转舵机方向（仅限舵机）（这将设置[PWM_AUX_REV](../advanced_config/parameter_reference.md#PWM_AUX_REV)或[PWM_AUX_MAIN](../advanced_config/parameter_reference.md#PWM_MAIN_REV)参数）。
   :::
 
1. 检查机体是否围绕预期重心平衡

   * 用手指在重心处托住机体，检查是否保持稳定。 

      ![重心水平](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_57_level_centre_of_gravity.jpg)
   
   * 如果机体前倾或后仰，调整电机位置以达到平衡。
   
      ![电机水平](../../assets/airframes/vtol/falcon_vertigo/falcon_vertigo_55_level_motors.jpg)

## 配置

执行常规的[基础配置](../config/index.md)。

注意事项：

1. 在[机型](../config/airframe.md)中选择机体组/类型为 *Standard VTOL*，并选择具体机体为 [Generic Standard VTOL](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)，如下所示。

   ![QCG - Select Generic Standard VTOL](../../assets/qgc/setup/airframe/px4_frame_generic_standard_vtol.png)

1. 将[自动驾驶仪方向](../config/flight_controller_orientation.md)设置为 `ROTATION_YAW_270`，因为自动驾驶仪相对于机体前方呈[侧向](#flight_controller_orientation)安装。指南针方向是向前的，因此可以保留默认设置（`ROTATION_NONE`）。
1. 按照[执行器配置](../config/actuators.md)中的说明配置输出和几何结构。
1. 默认参数通常足以实现稳定飞行。如需更详细的调参信息，请参阅[Standard VTOL接线与配置](../config_vtol/vtol_quad_configuration.md)。

完成校准后，VTOL即可飞行。

## 视频

<lite-youtube videoid="h7OHTigtU0s" title="PX4 Vtol test"/>## 支持

如果您有关于VTOL转换或配置的任何问题，请访问<https://discuss.px4.io/c/px4/vtol>。