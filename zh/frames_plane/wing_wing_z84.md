# Wing Wing Z-84 Pixracer 组装

Wing Wing Z-84 是一款飞翼机架。  
它体积小巧、坚固耐用，足以容纳一个 [Pixracer](../flight_controller/pixracer.md)。

关键信息：

- **机架：** Wing Wing Z-84  
- **飞控：** Pixracer  

![Wing Wing Z-84 构建](../../assets/airframes/fw/wing_wing/wing_wing_build11.jpg)

## 零部件清单

### Z-84 Plug n' Fly (PNF/PNP) 或 散件套件

选择以下其一：  
- [Banggood](https://www.banggood.com/Wing-Wing-Z-84-Z84-EPO-845mm-Wingspan-Flying-Wing-PNP-p-973125.html)  
- [Hobbyking US Warehouse](https://hobbyking.com/en_us/wing-wing-z-84-epo-845mm-kit.html)  

:::tip  
PNF（或"PNP"）版本包含电机、螺旋桨和电子调速器。  
"套件"版本不包含这些组件，需单独购买。  
:::

### 电子调速器（ESC）

选择以下其一（任一小型（≥12A）ESC均可）：

- [Turnigy 20A 有刷电子调速器](https://hobbyking.com/en_us/turnigy-20a-brushed-esc.html)（Hobbyking）  
- [Lumenier Regler 30A BLHeli_S ESC OPTO](https://www.getfpv.com/lumenier-30a-blheli-s-esc-opto-2-4s.html)（GetFPV）  

### 自动控制系统及核心组件

- [Pixracer](../flight_controller/pixracer.md) 套件（含GPS和电源模块）  
- FrSky D4R-II 接收机或等效设备（根据手册设置为PPM总线输出）  
- [Holybro pix32 的微型遥测套件](../flight_controller/pixfalcon.md#availability)  
- [Holybro pix32 / Pixfalcon 的数字空速传感器](../flight_controller/pixfalcon.md#availability)  
- 1800 mAh 2S 锂聚合物电池 - 例如 [Team Orion 1800mAh 7.4V 50C 2S1P](https://teamorion.com/en/batteries-en/lipo/soft-case/team-orion-lipo-1800-2s-7-4v-50c-xt60-en/)  

### 推荐备用零件

- 1厘米直径螺旋桨保护环（[Hobbyking](https://hobbyking.com/en_us/wing-wing-z-84-o-ring-10pcs.html)）  
- 125x110 mm 螺旋桨（[Hobbyking](https://hobbyking.com/en_us/gws-ep-propeller-dd-5043-125x110mm-green-6pcs-set.html)）  

## 接线

按照如下方式连接舵机和电机。  
使用 `MAIN` 输出口（非AUX标记的输出口）。  
电机控制器需内置BEC，因为自动驾驶仪不为舵机供电。  

端口 | 连接  
--- | ---  
RC IN    | PPM 或 S.BUS / S.BUS2 输入  
MAIN 1   | 左副翼  
MAIN 2   | 右副翼  
MAIN 3   | 空置  
MAIN 4   | 电机1  

## 组装日志

下图大致展示了组装过程，步骤简单，可用热熔胶枪完成。

![wing wing build01](../../assets/airframes/fw/wing_wing/wing_wing_build01.jpg)  
![wing wing build02](../../assets/airframes/fw/wing_wing/wing_wing_build02.jpg)  
![wing wing build03](../../assets/airframes/fw/wing_wing/wing_wing_build03.jpg)  
![wing wing build04](../../assets/airframes/fw/wing_wing/wing_wing_build04.jpg)  
![wing wing build09](../../assets/airframes/fw/wing_wing/wing_wing_build09.jpg)  
![Wing Wing Z-84 构建](../../assets/airframes/fw/wing_wing/wing_wing_build11.jpg)  

## PX4 配置

### 机体配置

在 QGroundControl [机体配置](../config/airframe.md) 中选择 **飞翼 > 通用飞翼**：  

![QGC - 为 West Wing 选择固件](../../assets/airframes/fw/wing_wing/qgc_firmware_flying_wing_west_wing.png)  

### 执行器映射

根据上述[接线部分](#wiring)设置[执行器配置](../config/actuators.md)以匹配副翼和油门的连接。  

![QGC - 设置执行器](../../assets/airframes/fw/wing_wing/qgc_actuator_config.png)  

### 其他配置

完成所有[基础配置](../config/index.md)，包括[自动调参](../config/autotune_fw.md)。  

高级调参可选 - 参见[固定翼机体配置](../config_fw/index.md)。