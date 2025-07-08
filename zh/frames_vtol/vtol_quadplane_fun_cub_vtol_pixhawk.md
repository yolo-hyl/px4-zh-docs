# FunCub QuadPlane（Pixhawk）

Fun Cub QuadPlane VTOL 是一款标准尾翼飞机（Multiplex FunCub）改装的垂直起降飞机。

关键信息：

- **机体:** Multiplex FunCub
- **飞控:** Pixhawk

![Fun Cub VTOL](../../assets/airframes/vtol/funcub_pixhawk/fun_cub_vtol_complete.jpg)

未改装的 Fun Cub 是一款价格相对实惠且易于飞行的飞机。  
改装后飞机重量显著增加且气动性降低。  
它仍然飞行表现良好，但前飞时需要大约75%油门。


## 材料清单

改装后的飞机外观大致如上图所示（其他类似型号同样适用，此为Multiplex Fun Cub）。

所需的基本设备包括：

- Multiplex FunCub（或类似型号）
- Pixhawk 或兼容设备
- 数字空速传感器
- 900 kV 电机（如 Iris 推进系统 - 电机和电调）
- 四旋翼电机使用的10英寸桨（10x45 或 10x47）
- 固定翼电机使用的10英寸桨（10×7）
- GPS模块
- 4S电池
- 用于安装四旋翼电机的铝合金框架（10x10mm方管，1mm壁厚）
- 起飞重量约为2.3kg（使用4200mAh 4S电池）


## 结构

结构由铝制支架组成，如下所示。

![quad_frame](../../assets/airframes/vtol/funcub_pixhawk/fun_cub_aluminium_frame_for_vtol.jpg)
![Fun Cub -frame for vtol mounted](../../assets/airframes/vtol/funcub_pixhawk/fun_cub_aluminium_frame_for_vtol_mounted.jpg)


## 布线

电机和舵机布线基本由用户自行决定，但应匹配 [通用标准VTOL](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol) 配置，如气动框架参考中所示。  
几何结构和输出分配可在 [执行器配置](../config/actuators.md#actuator-outputs) 中设置。

例如，您可以像下面这样布线（以"坐在飞机内"的视角）：

端口 | 连接
--- | ---
MAIN 1 | 右前电机（逆时针）
MAIN 2 | 左后电机（逆时针）
MAIN 3 | 左前电机（顺时针）
MAIN 4 | 右后电机（顺时针）
AUX 1  | 左副翼 TODO
AUX 2  | 右副翼
AUX 3  | 升降舵
AUX 4  | 方向舵
AUX 5  | 油门

更多布线和配置说明请参见：  
[标准VTOL布线和配置](../config_vtol/vtol_quad_configuration.md)。 <!-- 请替换为Pixhawk布线快速入门 -->


## 机体配置

1. 在 [机体](../config/airframe.md) 中选择机体组/类型为 *标准VTOL*，并选择具体机体为 [通用标准VTOL](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)（如下所示，不要忘记点击顶部的 **应用并重启**）。

   ![QCG - Select Generic Standard VTOL](../../assets/qgc/setup/airframe/px4_frame_generic_standard_vtol.png)

1. 按照 [执行器配置](../config/actuators.md) 中的说明配置输出和几何结构  
1. 默认参数通常足以实现稳定飞行。更多详细调参信息请参见 [标准VTOL布线和配置](../config_vtol/vtol_quad_configuration.md)。

完成校准后，VTOL即可准备飞行。


## 视频

<lite-youtube videoid="4K8yaa6A0ks" title="Fun Cub PX4 VTOL Maiden"/>


## 支持

如果您有关于VTOL改装或配置的任何问题，请访问 <https://discuss.px4.io/c/px4/vtol>。