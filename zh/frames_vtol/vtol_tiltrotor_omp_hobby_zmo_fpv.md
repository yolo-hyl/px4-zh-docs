

# OMP Hobby ZMO FPV

OMP Hobby ZMO是一款小型[倾转旋翼垂直起降](../frames_vtol/tiltrotor.md)飞行器，提供即飞套装(RTF kit)。

本构建指南展示如何安装飞行控制系统([Auterion Skynode评估套件](../companion_computer/auterion_skynode.md)、[Pixhawk 6C](../flight_controller/pixhawk6c.md)或[Pixhawk 6C mini](../flight_controller/pixhawk6c_mini.md))并设置PX4。

![组装完成的ZMO悬停1](../../assets/airframes/vtol/omp_hobby_zmo_fpv/airframe-hover.jpg)

![组装完成的ZMO悬停2](../../assets/airframes/vtol/omp_hobby_zmo_fpv/airframe-hover2.jpg)

## 概述

关键机体特性:

- 紧凑且便于携带
- 预装执行器
- 快速拆卸机翼连接系统
- 套件包含运输箱
- 约35分钟续航时间(取决于起飞重量)
- VTOL能力使机体能在固定翼无法飞行的区域起降
- 套件中包含电池和充电器
- 整体组装简便
- 前部预留FPV和/或运动相机安装空间

根据最终起飞重量，悬停时间可能会受限(机体悬停时机舱内空气流通有限，电调可能会过热)。

## 购买渠道

- [OMP-Hobby](https://www.omphobby.com/OMPHOBBY-ZMO-VTOL-FPV-Aircraft-With-DJI-Goggles-And-Remote-Controller-p3069854.html)
- [GetFPV](https://www.getfpv.com/omphobby-zmo-z3-vtol-fpv-1200mm-arf-plane-kit-no-fpv-system.html)
- [FoxtechFPV](https://www.foxtechfpv.com/zmo-pro-fpv-vtol.html)

## 飞控

已测试的选项包括：

- [Auterion Skynode 评估套件](../companion_computer/auterion_skynode.md)
- [Pixhawk 6C](../flight_controller/pixhawk6c.md) 配合 [PM02 V3](../power_module/holybro_pm02.md)
- [Pixhawk 6C mini](../flight_controller/pixhawk6c_mini.md) 配合 [PM02 V3](../power_module/holybro_pm02.md)

飞控的大致最大尺寸为：50x110x22mm

## 额外配件

- [GPS F9P (包含在Skynode评估套件中)](../gps_compass/rtk_gps_holybro_h-rtk-f9p.md)
- [GPS M9N (F9P的更经济替代方案)](../gps_compass/rtk_gps_holybro_h-rtk-m8p.md)
- [空速传感器 (包含在Skynode评估套件中)](https://www.dualrc.com/parts/airspeed-sensor-sdp33) — 推荐用于提高安全性和性能
- [空速传感器 (更经济替代方案)](https://holybro.com/products/digital-air-speed-sensor?pr_prod_strat=use_description&pr_rec_id=236dfda00&pr_rec_pid=7150470561981&pr_ref_pid=7150472462525&pr_seq=uniform)
- [Lidar Lightware lw20-c (包含在Skynode评估套件中)](../sensor/sfxx_lidar.md) (Optional)
- [Lidar Seeed Studio PSK-CM8JL65-CC5 (更经济替代方案)](https://www.seeedstudio.com/PSK-CM8JL65-CC5-Infrared-Distance-Measuring-Sensor-p-4028.html) (Optional)
- [5V BEC](http://www.mateksys.com/?portfolio=bec12s-pro)
- [遥控系统(RC)](../getting_started/rc_transmitter_receiver.md) (根据个人偏好选择)
- [30cm公头伺服延长线 10条装](https://www.getfpv.com/male-to-male-servo-extension-cable-twisted-22awg-jr-style-5-pcs.html)
- [USB-C延长线](https://www.digitec.ch/en/s1/product/powerguard-usb-c-usb-c-025-m-usb-cables-22529949?dbq=1&gclid=Cj0KCQjw2cWgBhDYARIsALggUhrh-z-7DSU0wKfLBVa8filkXLQaxUpi7pC0ffQyRzLng8Ph01h2R1gaAp0mEALw_wcB&gclsrc=aw.ds)
- [3M VHB胶带](https://www.amazon.in/3M-VHB-Tape-4910-Length/dp/B00GTABM3Y)
- [3D打印支架](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/vtol/omp_hobby_zmo_fpv/omp_hobby_zmo_3d_prints.zip)
  - 2个机翼连接支架
  - 1个空速传感器支架
  - 1个GPS支架
  - 1个Lidar支架
  - 1个Skynode支架
- [USB摄像头 (包含在Skynode开发套件中)](https://www.amazon.com/ELP-megapixel-surveillance-machine-monitor/dp/B015FIKTZC)
- 螺丝、插件、热缩管等

## 工具

本构建中使用了以下工具：

- 六角驱动工具套装  
- 扳手套装  
- 焊接站  
- 胶水：热熔胶、5分钟环氧胶  
- 胶带  
- 3M双面胶带（[3M VHB胶带](https://www.amazon.in/3M-VHB-Tape-4910-Length/dp/B00GTABM3Y)）  
- 砂纸  
- 3D打印机

## 硬件集成

### 准备工作

移除原始飞控、电调和机翼连接线。
同时移除螺旋桨。
这将有助于机体的操控，并降低因电机意外启动而导致受伤的风险。

ZMO FPV 的原始状态。

![ZMO FPV 的原始状态](../../assets/airframes/vtol/omp_hobby_zmo_fpv/zmo-01.jpg)

从机体上移除飞控和机翼连接器的 ZMO FPV。

![移除飞控和机翼连接器的 ZMO FPV](../../assets/airframes/vtol/omp_hobby_zmo_fpv/zmo-02.jpg)

### 电子调速器(ESC)

1. 拆下ESC的PWM信号线和接地引脚，并焊接舵机延长线到对应引脚上。  
   电缆长度应足够将线连接到飞控的FMU引脚。

1. 拆下后电机的三个母香蕉插头连接器（Pixhawk 6集成可能不需要此步骤）。

1. 使用4颗M2.5 x 12螺丝将ESC重新固定到位。

1. 缩短后电机线缆并按图示位置焊接。

1. 将信号线和GND线焊接至ESC的PWM输入端。

   ![ESC 01](../../assets/airframes/vtol/omp_hobby_zmo_fpv/esc-01.jpg)

1. 移除ESC上的母香蕉插头。  
   这将为安装飞控提供更多空间。

   ![ESC 02](../../assets/airframes/vtol/omp_hobby_zmo_fpv/esc-02.jpg)

1. 将后电机线缆焊接至ESC。  
   确保电机连接方向正确以获得正确的旋转方向。

   ![ESC 03](../../assets/airframes/vtol/omp_hobby_zmo_fpv/esc-03.jpg)

### 机翼连接器

当安装机翼时，若需直接连接机翼连接器，需要一些3D打印的支架来居中连接器。  
这一步并非必需，但会使操作更加简便，并且在野外安装飞机时减少一个需要担心的步骤。

1. 使用热熔胶或5分钟环氧树脂将机翼连接器粘接到3D打印部件中。  
1. 将带有连接器的3D打印部件粘接到机身中。  
   确保在胶水固化时连接器对齐。  

   对齐连接器最简便的方法是在胶水固化时安装机翼，但需确保机身和机翼之间没有胶水，否则机翼可能会被卡住。

将连接器粘接到3D打印部件中

![机翼连接器 01](../../assets/airframes/vtol/omp_hobby_zmo_fpv/wing-connector-01.jpg)

将连接器粘接到机身中。确保连接器正确对齐。

![机翼连接器 02](../../assets/airframes/vtol/omp_hobby_zmo_fpv/wing-connector-02.jpg)

### Pixhawk 适配板和BEC

1. 按照图片所示切割泡沫，为使用双面胶带安装Pixhawk适配板和BEC腾出空间。
   FMU板应安装在机身左侧（飞行方向的左侧）。
   将舵机接头和电池电压线焊接到BEC上。

   ![泡沫切割 01](../../assets/airframes/vtol/omp_hobby_zmo_fpv/foam-cut-01.png)
   ![Pixhawk 适配板安装 01](../../assets/airframes/vtol/omp_hobby_zmo_fpv/pixhawk-adapter-01.jpg)

1. 准备BEC以连接到IO板和电池。
   BEC也可以直接焊接到电调的电池焊垫上。

   ![BEC准备](../../assets/airframes/vtol/omp_hobby_zmo_fpv/bec-01.jpg)

1. 使用双面胶带安装BEC。

   ![BEC安装](../../assets/airframes/vtol/omp_hobby_zmo_fpv/bec-02.jpg)

### 电缆

1. 剪断舵机的连接器，并将舵机延长线焊接在电缆上。  
   请确保电缆长度足以到达Pixhawk适配板。  
   如果你拥有压线钳，也可以直接添加连接器而无需焊接。

1. 按以下顺序将舵机电缆插入适配IO板：

   - 1 - 左副翼  
   - 2 - 右副翼  
   - 3 - 左V尾  
   - 4 - 右V尾  
   - 5 - 左倾转  
   - 6 - 右倾转  

1. 按以下顺序将电机信号电缆插入FMU适配板：

   - 1 - 前左  
   - 2 - 前右  
   - 3 - 后

### 传感器

#### 皮托管

1. 首先检查皮托管是否能放入3D打印的支架中。
   如果可以，将皮托管支架粘合到位。

   为了对齐管子，将其穿过FPV前面板右侧的第二个孔。
   该支架可让您将管子推回机身内，以便在运输和操作时进行保护。
   传感器单元可通过双面胶安装在3D打印的支架顶部。

1. 将3D打印的支架粘合到位。

   ![Pitot tube 01](../../assets/airframes/vtol/omp_hobby_zmo_fpv/pitot-tube-01.png)

1. 传感器可安装在3D打印的支架顶部。

   ![Pitot tube 02](../../assets/airframes/vtol/omp_hobby_zmo_fpv/pitot-tube-02.png)

#### Lidar

如果需要，可以在机身前方安装Lidar。

安装Lidar的步骤：

1. 移除散热器
1. 将Lidar和3D打印的Lidar支架粘接到位。

![Lidar 01](../../assets/airframes/vtol/omp_hobby_zmo_fpv/lidar-01.jpg)

#### GPS/指南针

安装GPS的步骤如下：

1. 使用3颗M3x10螺丝将两个3D打印部件拧紧连接。  
1. 将GPS从塑料外壳中取出并拔下连接器。  
1. 将电缆穿过碳纤维翼梁。  
1. 使用5分钟环氧树脂将3D打印部件固定到位。  
   ![将GPS支架粘合到位](../../assets/airframes/vtol/omp_hobby_zmo_fpv/gps-01.jpg)  
1. 胶水固化后，使用4颗M2.5x10螺丝将GPS固定到板上。  
   ![用螺丝将GPS固定到支架](../../assets/airframes/vtol/omp_hobby_zmo_fpv/gps-02.jpg)

#### USB相机

1. 剪断相机的USB线缆，使其长度为15厘米。  
1. 将USB适配器线缆剪至25厘米，并将两根线缆焊接在一起。  
1. 安装相机时，需要在泡沫墙中切割一个孔洞。  

   ![USB相机01：在墙中穿线的孔。](../../assets/airframes/vtol/omp_hobby_zmo_fpv/camera-01.jpg)  

   然后可以使用双面胶将相机粘贴在墙上。

### 飞控

飞控可以安装在电调的上方。

#### Pixhawk 6c/6c mini

如果使用Pixhawk 6c或6c mini，只需将飞控通过双面胶固定到位。

#### Skynode

如果使用Skynode：

1. 将其放置在电调顶部，并在ZMO注塑塑料部件上标记后部两个安装位置。
1. 将Skynode从机体上取下，使用2.8 mm钻头在塑料部件上钻2个孔。
   ![后部Skynode安装孔](../../assets/airframes/vtol/omp_hobby_zmo_fpv/flight-controller-01.jpg)
1. 将Skynode放回原位，并用2x M3x10螺丝固定。

另一种方案是在孔中添加螺纹嵌件。
由于ZMO注塑部件非常薄，需要将其粘合固定。

1. 用2x M3x10螺丝将前部Skynode安装支架固定在Skynode上。
1. 然后在支架底部涂抹5 min环氧树脂，并在Skynode顶部放置重物直至胶水固化。
   要访问前部的2个安装螺丝，需从顶部向泡沫板中戳2个孔。

   ![前部Skynode安装支架](../../assets/airframes/vtol/omp_hobby_zmo_fpv/flight-controller-02.jpg)

### 天线和遥控接收器

::: info
如果安装了Skynode，则LTE可以用作遥测和视频链路。
如果使用Pixhawk，则需要不同的[遥测链接](../telemetry/index.md)。
一个低成本示例是[SiK遥测无线电](../telemetry/sik_radio.md)。
:::

1. 可在机体底部安装一个LTE天线。
   可通过电调散热器的开口将天线线缆穿过。

   ![LTE天线 01](../../assets/airframes/vtol/omp_hobby_zmo_fpv/lte-antenna-01.jpg)

1. 第二个天线可安装在机体电池舱左侧内部。
   遥控接收器也可置于电池舱左侧。

   ![LTE天线 2和遥控接收器](../../assets/airframes/vtol/omp_hobby_zmo_fpv/lte-antenna-02.jpg)

## 软件设置

### 选择机架

1. 打开QGC，选择 **Q** 图标，然后选择 **机体设置**。
1. 选择 [机架](../config/airframe.md) 选项卡

1. 从 _VTOL Tiltrotor_ 组中选择 [Generic Tiltrotor VTOL](../airframes/airframe_reference.md#vtol_vtol_tiltrotor_generic_tiltrotor_vtol)，然后点击 **应用并重启**。

### 加载参数文件

接下来我们将加载一个[参数文件](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/vtol/omp_hobby_zmo_fpv/omp_hobby_zmo.params)，该文件包含定义机体几何结构、输出映射和调参值的参数——这样您就无需手动配置了！  
如果您已按照电机接线说明进行操作，可能除了传感器校准和配平调整之外，不需要进行更多配置。

加载文件步骤：

1. 下载[参数文件](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/vtol/omp_hobby_zmo_fpv/omp_hobby_zmo.params)。
1. 选择[Parameters](../advanced_config/parameters.md#finding-updating-parameters)选项卡，然后点击右上角的 **Tools**。
1. 选择 **Load from file**，然后选择刚刚下载的 `omp_hobby_zmo.params` 文件。
1. 重启机体。

### 传感器选择

空速传感器可在[参数](../advanced_config/parameters.md#finding-updating-parameters)中启用。

- 如果使用[推荐的空速传感器SDP33](https://www.dualrc.com/parts/airspeed-sensor-sdp33)，需要启用`SENS_EN_SDP3X`。
- 如果使用[Lidar Lightware lw20-c (包含在Skynode评估套件中)](../sensor/sfxx_lidar.md)，需要将`SENS_EN_SF1XX`设置为6 (SF/LW/20c)。

### 传感器校准

首先确保设置[飞控的正确方向](../config/flight_controller_orientation.md)。  
这应该是默认设置(`ROTATION_NONE`)。

然后校准主传感器：

- [指南针](../config/compass.md)  
- [陀螺仪](../config/gyroscope.md)  
- [加速度计](../config/accelerometer.md)  
- [空速计](../config/airspeed.md)

### 遥控器设置

[校准遥控器](../config/radio.md)并设置[飞行模式开关](../config/flight_mode.md)。

我们建议您为[飞行模式配置 > 我应该设置哪些飞行模式和开关？](../config/flight_mode.md#what-flight-modes-and-switches-should-i-set)中定义的模式分配遥控器开关。  
特别需要分配一个_VTOL切换开关_、_禁用开关_，以及选择[Stabilized mode](../flight_modes_fw/stabilized.md)和[Position mode](../flight_modes_fw/position.md)的开关。

### 执行器设置

:::warning
请确保已移除螺旋桨！
在执行器标签页中，电机可能意外启动。
:::

电机、控制面和其他执行器在QGroundControl中通过[执行器配置与测试](../config/actuators.md)进行配置。

之前加载的[参数文件](#加载参数文件)意味着此界面应已正确设置：你只需调整机体特定的中立值。若电机/舵机连接到了与建议不同的输出端口，需要在执行器输出部分修改输出与功能的映射关系。

#### 倾斜舵机

1. 将机体切换到手动模式（可通过飞行模式开关操作，或在MAVLink shell中输入 `commander mode manual`）。
1. 检查电机是否朝上。
   如果电机朝前则需要反转对应的倾斜舵机（勾选每个舵机旁边的复选框）。

   ![Tilt Servo adjustment](../../assets/airframes/vtol/omp_hobby_zmo_fpv/tilt-limits-01.jpg)

1. 调整最小值（如果已反转：最大值），使旋翼推力能够朝后（多旋翼模式下偏航分配需要）。
1. 调整参数 `VT_TILT_MC`，使旋翼在输入为零时精确朝上。
1. 然后在MAVLink shell中输入 `commander transition` 来调整水平位置。

#### 控制面

检查执行器是否需要通过遥控器进行反向调整：

- 横滚杆向右。右副翼应上抬，左副翼应下压。
- 俯仰杆向后（上升）。两个V型尾翼应上抬。
- 偏航杆向右。两个舵面应向右移动。

现在调整微调值，使所有舵面处于中立位置。

![Servo trim](../../assets/airframes/vtol/omp_hobby_zmo_fpv/servo_trim.png)

#### 电机方向与安装方向

请确保已移除螺旋桨！！

- `电机1`：前左电机应顺时针旋转  
- `电机2`：前右电机应逆时针旋转  
- `电机3`：后部电机应逆时针旋转  

如果电机旋转方向错误，需要交换三根电机线中的两根。  
方向无法通过软件更改，因为机体不使用[DShot ESC](../peripherals/dshot.md)。

## 首次飞行

- 在[稳定模式](../flight_modes_fw/stabilized.md)下检查倾斜转子反应。将油门杆保持在最低位置并使机体着地。要启用倾斜舵机，需要解锁机体。
  - 指令向右偏航（机头向右）-> 左侧电机应向前倾斜，右侧电机应向后倾斜
  - 指令向左偏航（机头向左）-> 左侧电机应向后倾斜，右侧电机应向前倾斜
- 安装螺旋桨
- 检查重心（CG）
  切换机体至前飞模式
  用两个手指在机翼下方的标记处将机体向上托起以检查重心
  机体应水平平衡
  如果机体向尾部或机头方向倾斜，则需要将电池向相反方向移动
  如果无法通过此方法平衡机体，则需要重新定位某些组件或添加配重以平衡
- 检查执行器方向和中立点调整
- 在[稳定模式](../flight_modes_fw/stabilized.md)下检查控制面反应。切换机体至前飞模式
  - 向右滚转
    右侧副翼应向下，左侧副翼应向上
  - 向上俯仰（机头抬起）
    两侧升降舵应向下
  - 向右偏航（机头向右）
    两侧升降舵应向左
- 如果使用[kill-switch](../config/safety.md#kill-switch)，请确保其正常工作且不会在飞行中意外激活！
- 在[稳定模式](../flight_modes_fw/stabilized.md)下解锁并检查电机是否响应指令（例如：向左滚转会增加右侧电机推力）
- 在[稳定模式](../flight_modes_fw/stabilized.md)下起飞并进行基本机动
- 如果一切正常，可在[位置模式](../flight_modes_fw/position.md)下起飞，并在约50米高度进行转换
  如果出现任何问题，请立即切换回多旋翼模式（使用转换开关）