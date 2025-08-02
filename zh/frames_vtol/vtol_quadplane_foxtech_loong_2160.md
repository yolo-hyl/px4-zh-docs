

# Foxtech Loong 2160 VTOL

_Foxtech Loong 2160 VTOL_ 是一款易于组装的几乎可飞（ARF）四旋翼飞行器VTOL无人机，翼展为2160毫米。  
本组装指南展示了如何使用 [Auterion Skynode评估套件](../companion_computer/auterion_skynode.md) 或 [Pixhawk 6C](../flight_controller/pixhawk6c.md) 添加飞控系统并设置 PX4。

![Loong VTOL飞行中的照片](../../assets/airframes/vtol/foxtech_loong_2160/01-foxtech-loong.jpg)

## 概述

规格参数：

- 翼展：2160mm
- 机身长度：1200mm
- 起飞重量：约7kg（不含载荷）
- 最大飞行时间：最长1小时30分钟
- 巡航速度：约17m/s
- 最大载荷重量：约1.5kg
- 便携箱尺寸：125cm x 34cm x 34cm

主要特性：

- 轻松组装：快速且易于设置
- 便携性：紧凑设计配合便携箱，便于携带运输
- 即飞状态：所有执行器预先安装并接线，极大减少设置时间
- 持久飞行时间：最长可达1小时30分钟，具体取决于天气条件和起飞重量
- 多样化载荷能力：宽敞的机身可容纳多种载荷，包括如Sony A7R等用于测绘应用的设备

## 购买渠道

- [Foxtech FPV (ARF Combo)](https://www.foxtechfpv.com/foxtech-loong-2160-vtol.html) - 推荐
- [Alibaba](https://www.alibaba.com/product_detail/Loong-2160-Long-Endurance-VTOL-Mapping_1600280686653.html)

## 飞行控制器

以下选项已通过测试：

- [Auterion Skynode评估套件](../companion_computer/auterion_skynode.md)
- [Pixhawk 6C](../flight_controller/pixhawk6c.md)

## 附加配件

- Auterion 12S 电源模块  
- [Holybro PM08D 电源模块（Auterion PM 的替代品）](https://holybro.com/collections/power-modules-pdbs/products/pm08d-digital-power-module-14s-200a)  
- [GPS F9P（包含在 Skynode 评估套件中）](../gps_compass/rtk_gps_holybro_h-rtk-f9p.md)  
- [GPS M9N（F9P 的更便宜替代品）](../gps_compass/rtk_gps_holybro_h-rtk-m8p.md)  
- [空速传感器（包含在 Skynode 评估套件中）](https://www.dualrc.com/parts/airspeed-sensor-sdp33) — 建议用于提高安全性与性能  
- [空速传感器（更便宜的替代品）](https://holybro.com/products/digital-air-speed-sensor?pr_prod_strat=use_description&pr_rec_id=236dfda00&pr_rec_pid=7150470561981&pr_ref_pid=7150472462525&pr_seq=uniform)  
- [Lidar Lightware lw20-c（包含在 Skynode 评估套件中）](../sensor/sfxx_lidar.md) （可选）  
- [Lidar Seeed Studio PSK-CM8JL65-CC5（更便宜的替代品）](https://www.seeedstudio.com/PSK-CM8JL65-CC5-Infrared-Distance-Measuring-Sensor-p-4028.html) （可选）  
- [遥控（RC）系统](../getting_started/rc_transmitter_receiver.md)（根据个人偏好选择）  
- [地面站与无线电链路](https://holybro.com/collections/rc-radio-transmitter-receiver/products/skydroid-h12?variant=42940989931709)  
- [USB-C 延长线](https://www.digitec.ch/en/s1/product/powerguard-usb-c-usb-c-025-m-usb-cables-22529949?dbq=1&gclid=Cj0KCQjw2cWgBhDYARIsALggUhrh-z-7DSU0wKfLBVa8filkXLQaxUpi7pC0ffQyRzlng8Ph01h2R1gaAp0mEALw_wcB&gclsrc=aw.ds)  
- [I2C 分线器](https://www.3dxr.co.uk/autopilots-c2/the-cube-aka-pixhawk-2-1-c9/cube-cables-accessories-sensors-c15/cubepilot-i2c-can-splitter-jst-gh-4pin-p2840)  
- [3D 打印支架](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/vtol/foxtech_loong_2160/loong-3d-prints.zip)  
  - 1x 基板  
  - 1x 栈式固定件  
  - 1x 风扇支架  
  - 1x 遥控器支架  
  - 1x 顶板  
  - 1x 遥控天线适配器  
  - 1x USB-C 支架 1  
  - 1x USB-C 支架 2  
- [铜制螺纹嵌件](https://cnckitchen.store/products/gewindeeinsatz-threaded-insert-set-standard-200-stk-pcs)  
- [XT30 连接器](https://www.amazon.com/Connectors-Female-Pieces-Shrink-Battery/dp/B0875MBLNH/ref=sr_1_1?keywords=xt30+connector&qid=1700643604&sr=8-1)  
- [各种螺丝](https://de.aliexpress.com/item/1005005999729125.html?spm=a2g0o.productlist.main.1.7fe0c7fcvInMsM&algo_pvid=2e5373e9-747f-4a28-9739-cd59d05d64f1&aem_p4p_detail=202311220106396068090130108300006423842&algo_exp_id=2e5373e9-747f-4a28-9739-cd59d05d64f1-0&pdp_npi=4%40dis%21CHF%2114.42%213.72%21%21%2116.01%21%21%402101f04d17006439995917563eeeb0%2112000035246480339%21sea%21CH%210%21AB&curPageLogUid=24AixvgVOlG3&search_p4p_id=202311220106396068090130108300006423842_1)  
- [扎带](https://www.amazon.com/Superun-Cable-Tie-Kit-Assorted/dp/B07TMKJP5S/ref=sr_1_2?crid=968Z3XJK9N3J&keywords=zip%2Bties%2Bset&qid=1700644053&sprefix=zip%2Bties%2Bset%2Caps%2C155&sr=8-2&th=1)  
- [天线延长线 - 与您的无线电系统匹配](https://www.digikey.ch/de/products/detail/amphenol-rf/095-902-536-012/13246174)  
- [推荐电池（12S 22Ah）](https://genstattu.com/tattu-22ah-12s-battery.html)

## 工具

该构建使用了以下工具。

- 六角驱动套装
- 扳手套装
- [焊台](https://www.amazon.com/UY-CHAN-Programmable-Pocket-size-Soldering/dp/B07G71CKC4/ref=sr_1_7?crid=2S2XK6363XRDF&keywords=ts+80+soldering+iron&qid=1700644208&sprefix=ts+80%2Caps%2C151&sr=8-7)
- 胶水：热熔胶，5分钟环氧胶
- 胶带
- 3M双面胶([3M VHB胶带](https://www.amazon.in/3M-VHB-Tape-4910-Length/dp/B00GTABM3Y))
- 3D打印机
- [蓝色乐泰](https://www.amazon.com/Loctite-Heavy-Duty-Threadlocker-Single/dp/B000I1RSNS?th=1)

## 硬件集成

本文档描述了Auterion Skynode的集成方式。
Pixhawk的安装可以以类似方式进行。

### 准备工作

### 航电单元

![完整堆叠组装](../../assets/airframes/vtol/foxtech_loong_2160/02-stack.png)

#### 准备3D打印部件

::: info
使用电烙铁将螺纹套件压入3D打印部件中。
:::

1. 将10个M3螺纹套件插入底板，如图所示：

   ![带螺纹套件的底板](../../assets/airframes/vtol/foxtech_loong_2160/03-baseplate.jpg)

1. 将2个M3螺纹套件插入堆叠支架，如图所示：

   ![带螺纹套件的堆叠支架](../../assets/airframes/vtol/foxtech_loong_2160/04-stack-fixture.jpg)

1. 将2个M4螺纹套件插入风扇支架和无线电支架，如图所示。

   ![无线电支架](../../assets/airframes/vtol/foxtech_loong_2160/05-radio-mount.jpg)

   如果需要在风扇支架上添加40mm 5V风扇，请插入4个M3螺纹套件。

   ![风扇支架](../../assets/airframes/vtol/foxtech_loong_2160/06-fan-mount.jpg)

1. 将电缆连接器更换为舵机连接器，以便插入舵机轨道供电。

   ::: info
   如果使用高功率无线电，可能需要安装风扇。
   :::

   ![带风扇的风扇支架](../../assets/airframes/vtol/foxtech_loong_2160/07-fan-mount.jpg)

1. 从机体上移除原始安装板。
   将电缆粘贴在机身外部。

   ![空机身](../../assets/airframes/vtol/foxtech_loong_2160/08-preparations.jpg)

1. 将底板滑入机体内部。
1. 将堆叠支架螺接到底板上，并用胶带或笔标记堆叠支架位置。
1. 从机身中移除部件，使用热熔胶将堆叠支架固定到位。

![安装堆叠支架](../../assets/airframes/vtol/foxtech_loong_2160/09-stack-fixure.jpg)

### 40A电源模块

40A电源模块为使用Skynode时的机载电子设备提供电力（随Skynode评估套件提供）：

1. 移除40A电源模块的外壳。
1. 使用2颗M2x6mm螺丝将电源模块固定在底板底部。
1. 制作一根延长线，将XT60连接器扩展为安装在底板上的XT30连接器。
   通过这种方式，6S电池电源可通过随机体提供的预配置电缆直接插入XT30连接器。

   ![40A电源模块安装](../../assets/airframes/vtol/foxtech_loong_2160/10-40a-power-module.jpg)

如果需要，电源模块上的无线电端口10V输出也可通过安装在6S电池输入XT60旁边的XT30连接器暴露出来。

### 传感器

#### 皮托管

1. 该传感器可通过2颗M3x16mm螺丝安装在底板右前角位置。注意连接器朝向机身中心方向。

   ![安装的空速传感器](../../assets/airframes/vtol/foxtech_loong_2160/11-airspeed-sensor.jpg)

   仅使用前管（图片中未展示）；其他管可拆除，因为我们的经验表明机体内压力已足够作为静压。

1. 当堆叠结构安装在机身内部时，来自机翼的导管和空速传感器导管需要连接在一起。使用胶带（这是最简单的方法）将其粘合，之后使用热缩管加固连接部位。

   :::warning
   使用热源时需谨慎，因为泡沫在高温下会熔化。
   :::

#### 激光雷达

::: info
建议使用激光雷达！  
如果没有安装激光雷达，应禁用在悬停状态下使用固定翼执行器向前加速的功能（将 [VT_FWD_THRUST_EN](../advanced_config/parameter_reference.md#VT_FWD_THRUST_EN) 设置为 `0` 而不是 `1`）。
:::

1. 用胶带或笔标记安装激光雷达的位置。  
   在PVC外壳和泡沫中切割一个孔，以便激光雷达能安装到位。  

   ![准备好的激光雷达孔](../../assets/airframes/vtol/foxtech_loong_2160/12-lidar-01.jpg)  

1. 用热胶固定激光雷达。  

   ![已安装的激光雷达](../../assets/airframes/vtol/foxtech_loong_2160/13-lidar-02.jpg)

#### GPS/指南针

1. 使用双面胶将GPS安装在机体后部，位于后部卡扣下方。

   ![已安装的GPS](../../assets/airframes//vtol/foxtech_loong_2160/14-gps.jpg)

   GPS上的方向箭头可以忽略。
   方向将由飞控在标定过程中自动检测。

### 飞控

将Pixhawk或Skynode安装到基板上。

#### Pixhawk 6c/6c mini

1. 使用双面胶将飞控安装到底板上。

#### Skynode

1. 使用4个M3x8螺丝将Skynode安装到底板上。
   确保"A"的顶部朝向机体前方。
1. 将40A电源模块插入两个电源接口中的上方接口。
1. 将一个（或如需要两个）USB适配器插入Skynode背面的4针JST-GH接口，并将它们引向底板前方。
   用扎带将线缆固定到位。
1. 将I2C分线器贴在底板右前方（该分线器可用于连接ETH设备如数传电台）。
1. 将I2C分线器与Skynode背面的ETH端口连接。
1. 将两条40针线缆插入Skynode前方。
1. 插入USB-C扩展线并将其弯折到前方。
   弯折角度需要非常紧凑，以便底板能够安装到机体中。

![Installed Skynode](../../assets/airframes/vtol/foxtech_loong_2160/15-skynode.jpg)

#### 适配板

1. 将Pixhawk适配板螺丝固定在顶部面板上。

### 天线和遥控接收器

1. 如图片所示，将Skynode LTE天线粘贴在机身侧面：

   ![LTE-Antennas](../../assets/airframes/vtol/foxtech_loong_2160/16-lte-antennas.jpg)

1. 如果使用无线电数传模块，可以将天线安装在机身顶部。  
   前部可直接安装天线延长线：

   ![WIFI-Antennas-Front](../../assets/airframes/vtol/foxtech_loong_2160/17-antenna-front.jpg)

   后部可使用3D打印天线适配器。  
   适配器可用热胶固定在指定位置：

   ![WIFI-Antenna-Back](../../assets/airframes/vtol/foxtech_loong_2160/19-rear-antenna.jpg)

### 12S电源模块

此12S电源模块是电机的主要供电模块。  
它能够承受比用于供电航电系统的40A电源模块更高的电流，并且由于Loong在悬停阶段会使用高达120A的电流，因此需要该模块。  

12S电源模块将安装在电池顶部。  
将安装在机体内部的XT90插头插入电源模块。  
连接Skynode的电源线需要延长。  
这样可以确保从电源模块获取电池读数。  

该电源模块还可作为Skynode的5V备用电源。  

![12S-Power-Module](../../assets/airframes/vtol/foxtech_loong_2160/18-12s-power-module.jpg)

### 组装

组装步骤如下：

1. 将底板滑入机体中。
1. 将LTE-Antennas天线插入Skynode。
1. 将Fan-Mount和Radio-Mount螺钉固定到底板上。
1. 将底板完全滑回并用螺钉固定到堆叠支架上。
1. 将顶板放在堆叠结构顶部，并将Skynode的40针线缆穿过Pixhawk适配板前的两个孔洞。
1. 请确保将顶部连接器连接到具有'GPS1'输入的适配板上。

将执行器按以下顺序连接到Pixhawk适配板：

MAIN:

1. 拉力电机
1. 空置，或安装风扇时连接风扇
1. 右侧副翼
1. 左侧副翼
1. 右侧升降舵
1. 左侧升降舵
1. 方向舵

AUX:

1. 前侧右电机
1. 后侧左电机
1. 前侧左电机
1. 后侧右电机

如果需要将执行器连接到不同输出端口，需要修改执行器输出映射（参见[执行器配置](../config/actuators.md)）。

## 软件设置

### 选择飞行器框架

1. 打开 QGC，选择 **Q** 图标，然后选择 **飞行器设置**。
1. 选择 [飞行器框架](../config/airframe.md) 标签页
1. 从 _Standard VTOL_ 组中选择 [Generic Standard VTOL](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)，然后点击 **Apply and Restart**。

### 加载参数文件

接下来我们加载一个[参数文件](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/vtol/foxtech_loong_2160/loong.params)，该文件包含定义机体几何结构、输出映射和调校值的参数 —— 所以你无需手动设置！
如果你已经按照电机接线指南进行操作，可能除了传感器校准和调整配平时不需要进行更多配置。

加载文件步骤：

1. 下载[参数文件](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/vtol/foxtech_loong_2160/loong.params)
1. 选择[参数](../advanced_config/parameters.md#finding-updating-parameters)标签页，然后点击右上角的 **工具**
1. 选择 **从文件加载** 并选择你刚刚下载的 `loong.params` 文件
1. 重启机体

### 传感器选择

- 如果使用 [Lidar Lightware lw20-c（包含在Skynode评估套件中）](../sensor/sfxx_lidar.md)，需要将 [SENS_EN_SF1XX](../advanced_config/parameter_reference.md#SENS_EN_SF1XX) 设置为 6（SF/LW/20c）。
- 确保选择了正确的空速传感器。
  如果使用推荐的 [SDP33空速传感器](https://www.dualrc.com/parts/airspeed-sensor-sdp33)，则无需更改，因为 [SENS_EN_SDP3X](../advanced_config/parameter_reference.md#SENS_EN_SDP3X) 在参数文件中已启用（设置为 `1`）。

### 传感器校准

首先确保设置[飞行控制器的正确方向](../config/flight_controller_orientation.md)。这应为默认值（ROTATION_NONE）。

然后校准主传感器：

- [指南针](../config/compass.md)
- [陀螺仪](../config/gyroscope.md)
- [加速度计](../config/accelerometer.md)
- [空速](../config/airspeed.md)

### RC配置

[校准您的遥控器](../config/radio.md)并设置[飞行模式开关](../config/flight_mode.md)。

我们建议您为[飞行模式配置 > 我应该设置哪些飞行模式和开关？](../config/flight_mode.md#what-flight-modes-and-switches-should-i-set)中定义的模式分配遥控器开关。  
特别需要分配一个_VTOL Transition Switch_（垂直起降转换开关）、_Kill Switch_（紧急停止开关），以及用于选择[Stabilized mode](../flight_modes_fw/stabilized.md)和[Position mode](../flight_modes_fw/position.md)的开关。

### 执行器设置和ESC校准

:::warning
务必移除螺旋桨！
在执行器标签页中电机可能会意外启动。
:::

电机、舵面和其他执行器在QGroundControl的[执行器配置与测试](../config/actuators.md)中配置。

先前加载的[参数文件](#加载参数文件)意味着此界面应已正确设置：你只需根据特定机体调整微调。
如果电机/舵机连接到了不同的输出端，你需要在执行器输出部分修改输出到功能的映射关系。

要校准ESC，请在不连接机翼的情况下启动机体，进入QGC的**执行器**标签页。
启用电机测试，将要校准电机的滑块滑动到最大。
将机翼插入机身并等待蜂鸣序列完成（约5秒）。
然后将滑块滑动到最小。

#### 控制面

检查是否需要通过遥控器反转执行器：

- 切换到[手动模式](../flight_modes_fw/stabilized.md)
- 向右推动摇杆。
  右侧副翼应上偏，左侧副翼应下偏。
- 向后推动俯仰摇杆（升空）。
  两个V尾翼面都应上偏。
- 向右推动偏航摇杆。
  两个翼面都应向右偏转

现在调整平衡值，使所有翼面回到中立位置。

![舵机平衡](../../assets/airframes/vtol/omp_hobby_zmo_fpv/servo_trim.png)

#### 电机方向和姿态

将机体加电（螺旋桨未安装）并进入[稳定模式](../flight_modes_fw/stabilized.md)。
检查所有四旋翼电机是否以相似的怠速旋转，并验证旋转方向是否正确。
检查以下反应：

- 向右滚转操纵杆。
  左侧两个电机应加速
- 向后滚转操纵杆。
  前侧两个电机应加速
- 向右偏航操纵杆。
  右前和左后电机应加速

::: info
如果无法观察到反应，请稍微增加油门，因为[Airmode](../config_mc/pid_tuning_guide_multicopter.md#airmode-mixer-saturation)未在偏航轴启用。
:::

## 首次飞行

- 安装螺旋桨（螺丝使用蓝色Loctite固定）
- 检查重心（GG）
  通过用两根手指在机翼连接处的卡扣位置抬升机体来检查重心。机体应保持水平平衡。如果机体向尾部或机头倾斜，则需要将电池调整到相反方向。如果无法通过此方法平衡机体，需重新定位部分组件或添加配重以达到平衡
- 检查执行器方向和中性配平
- 在[稳定模式](../flight_modes_fw/stabilized.md)中检查控制面响应
  将机体切换到前飞模式
  - 右侧滚转时，右侧副翼应下偏，左侧副翼应上偏
  - 机头上仰时，两个升降副翼均应下偏
  - 右侧偏航（机头右偏）时，两个升降副翼均应左偏
- 如果使用[kill-switch](../config/safety.md#kill-switch)，请确保其工作正常且不会在飞行中意外触发！
- 在[稳定模式](../flight_modes_fw/stabilized.md)下解锁并检查电机是否响应指令（如左滚转时右侧电机油门增加）
- 在[稳定模式](../flight_modes_fw/stabilized.md)下起飞并执行基本机动
- 如果一切正常，可在[位置模式](../flight_modes_fw/position.md)下起飞并在约50米高度执行转换。如果出现异常，请立即使用转换开关切换回多旋翼模式