# Volantex Ranger-Ex 四旋翼垂直起降飞行器（Pixhawk）

QuadRanger VTOL 是基于 Volantex Ranger-Ex 改装的常规尾翼飞机，已加装四旋翼系统。  
Ranger-Ex 是一款易于飞行的 FPV 平台，广泛可用。  
它也由 HobbyKing 提供，并重新命名为 Quanum Observer。  
塑料机身和 2 米 EPO 机翼使其成为具有高载重能力的坚固机架。

关键信息：

- **机架:** Volantex Ranger-Ex 或 *Quanum Observer*  
- **飞控:** Pixhawk  

![QuadRanger](../../assets/airframes/vtol/quadranger_rangerex_pixhawk/quadranger_vtol_complete_build.jpg)

VTOL 改装使飞机稍重（包括锂电池约 3.5kg）。  
改装后飞机巡航时推油约为 65%。  
建议的四旋翼配置可提供 7.5kg 推力，机架总重量可达约 4.5kg。  
这为 FPV 设备和相机等载荷提供了足够的承载能力。

该改装设计旨在最小化对空气动力学的影响，并通过增加强度减少机翼变形。

## 所需材料

- Volantex Ranger-Ex 或 Quanum Observer  
- 1200KV 530W 电机  
- 30A 电调  
- 4S 电池  
- APC 电动 11x5 螺旋桨  

## 改装套件

- 基本零件包括：  
- Pixhawk 或兼容设备  
- 数字空速传感器  
- 3DR 电源模块或兼容设备  
- GPS  

完整的零件清单及 Hobbyking 欧洲和国际仓库链接请参见：  
[QuadRanger-VTOL-partslist](https://px4.io/wp-content/uploads/2016/01/QuadRanger-VTOL-partslist-1.xlsx)

下图显示了一侧机翼所需的零件。

![QuadRanger 零件](../../assets/airframes/vtol/quadranger_rangerex_pixhawk/quadranger_vtol_parts_for_one_wing.jpg)

改装所需的工具包括：

- Dremel 或类似旋转工具  
- 美工刀  
- UHU POR 胶水  
- CA 胶水  
- 卷尺  
- 胶带  

![QuadRanger 改装工具](../../assets/airframes/vtol/quadranger_rangerex_pixhawk/quadranger_vtol_conversion_tools.jpg)

## 机翼改装

::: info
请注意，本改装日志中使用的机翼曾因先前改装受损。
:::

将两根 800mm 方形碳管分别切割为 570mm 和 230mm 的长度。

使用旋转工具配合某种导向装置，在聚苯乙烯机翼上切割出 1.5cm 深的槽。  
槽的长度、深度和宽度应与一根 230mm 方形碳管一致，位置如下图所示。

![QuadRanger 碳管槽](../../assets/airframes/vtol/quadranger_rangerex_pixhawk/quadranger_vtol_carbon_tube_slot.jpg)

用 CA 胶水将 300x150x1.5mm 碳板粘接到 230mm 碳管上，并开孔以便布线。  
将电源和信号线穿入电调。  
使用 UHU POR 胶水将碳板和碳管粘接到聚苯乙烯机翼上，位置如下图所示。

![QuadRanger 碳板安装](../../assets/airframes/vtol/quadranger_rangerex_pixhawk/quadranger_vtol_sheet_attachment.jpg)

用 CA 胶水将 570mm 方形碳管粘接到碳板上，位置应距机翼连接处 285mm。  
碳管应相对于机翼垂直区域居中，两侧各延伸 165mm。

将电机支架固定到电机上，用另一块电机支架板和 4 个 M3x25mm 螺丝将电机安装在方形碳管末端，如下图所示。  
用电线绑带将电调固定到碳管上。使用 Afro 电调时，务必连接信号和地线。

![QuadRanger 电机和电调](../../assets/airframes/vtol/quadranger_rangerex_pixhawk/quadranger_vtol_motor_and_esc.jpg)

## 接线

Pixhawk 的输出应按以下方式接线（接线方向以“坐在飞机内”视角为准）。

端口 | 连接
--- | ---
MAIN 1   | 前右电机，逆时针  
MAIN 2   | 后左电机，逆时针  
MAIN 3   | 前左电机，顺时针  
MAIN 4   | 后右电机，顺时针  
AUX  1   | 左副翼  
AUX  2   | 右副翼  
AUX  3   | 升降舵  
AUX  4   | 方向舵  
AUX  5   | 油门  

::: info
可通过 QGroundControl 的 PWM\_OUTPUT 组中的 PWM\_REV 参数反转舵机方向（齿轮图标标签，左侧菜单最后一项）。
:::

更多接线和配置说明请参见：[Standard VTOL Wiring and Configuration](../config_vtol/vtol_quad_configuration.md)

## 配置

在 QGroundControl 中按以下方式配置机架（别忘记点击顶部的 **Apply and Restart**）。

![QGC - 选择标准 VTOL 固件](../../assets/airframes/vtol/funcub_pixhawk/qgc_firmware_standard_vtol_fun_cub_quad.png)

## 支持

如对 VTOL 改装或配置有任何疑问，请访问 <https://discuss.px4.io/c/px4/vtol>。