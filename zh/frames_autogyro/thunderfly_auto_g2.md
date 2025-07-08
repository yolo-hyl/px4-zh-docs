# ThunderFly Auto-G2 自转旋翼机

*ThunderFly Auto-G2* 是一款基于 [Durafly™ Auto-G2 Gyrocopter](https://hobbyking.com/en_us/duraflytm-auto-g2-gyrocopter-w-auto-start-system-821mm-pnf.html) 遥控模型开发的自动驾驶仪控制自转旋翼机，其原始模型的多个部件被替换为可3D打印的部件。

![Auto-G2](../../assets/airframes/autogyro/auto-g2/autog2_title.jpg)

::: info
Auto-G2 自转旋翼机的机体最初由 [ThunderFly](https://www.thunderfly.cz/) 开发，目前已升级为 [TF-G2 平台](https://docs.thunderfly.cz/instruments/TF-G2)。
有关当前 [TF-G2 商用机体](https://www.thunderfly.cz/tf-g2.html) 的更多信息，请访问我们的网站。
:::

所有新增部件以开源项目形式托管在 [GitHub](https://github.com/ThunderFly-aerospace/Auto-G2)。
3D打印部件使用 [OpenSCAD](https://www.openscad.org/) 设计。

## 修改

Durafly Auto-G2 在原始设计中采用三叶片旋翼，叶片长度为 400 毫米，采用 CLARK-Y 翼型。
旋翼头仅允许在ROLL轴上倾斜。
自转旋翼通过方向舵和升降舵进行控制。
Durafly Auto-G2 自转旋翼盒包含聚苯乙烯机身、电调、电机（可能是800kV）、4个舵机、尾翼翼型、3个叶片与旋翼中心部件、金属骨架和预旋转器。

Durafly 模型的修改如下：
* 添加自动驾驶仪
* 双自由度旋翼头（俯仰、横滚）
* 具有安全断裂能力的双叶片旋翼
* 更大的起落架

### 自动驾驶仪

经过所有修改的飞机重量已经相当大。
因此建议使用低重量飞控（例如 [Holybro pix32](../flight_controller/holybro_pix32.md) 或 [CUAV nano](../flight_controller/cuav_v5_nano.md)）。

自动驾驶仪应安装在自转旋翼底部的3D打印减震垫上。
我们使用了在 [thingiverse](https://www.thingiverse.com/thing:160655) 找到的减震平台。

### 旋翼头

与原始自转旋翼相比，旋翼头经过修改，允许在横滚和俯仰轴上运动。
因此，旋翼不仅能够控制自转旋翼的转向，还能控制爬升。
即使与原始方向舵和升降舵控制相比，旋翼在低空速情况下仍可实现方向控制。

打印的旋翼头由三个部件组成。
底部部件通过 M2.5 螺丝固定在原始胶合板支架上。
第一和第二部件之间的 M3x35 螺丝创造了俯仰轴自由度，第二和第三部件之间的连接创造了横滚轴自由度。
后者轴由 M3x30 螺丝和自锁螺母组成。
从旋翼侧看，螺丝头配有大垫圈。

由 M3x50 高强度螺丝制成的旋翼轴穿过第三部件。
使用的轴承为 623 2Z C3 SKF。
在部件末端，通过 M2.5 螺丝将球杆连接到底部支架中的舵机。
建议将原始舵机更换为更高质量的舵机，因为它们较弱且在原始结构中相互协助。

![旋翼头](../../assets/airframes/autogyro/auto-g2/modif_rh.png)

### 双叶片旋翼

原始 Durafly Auto-G2 自转旋翼使用三叶片旋翼，本改装中改为双叶片旋翼。
原因在于减少振动和简化结构。
3D打印的中央部件设计为既可用于中国 Durafly 叶片，也可用于3D打印叶片。

旋翼中央部件由多个组件组成，其作用如下：
* 实现叶片挥舞（flapping）
* 具有在撞击地面时断裂的变形区。
  通过这种方式，旋翼通常只需更换一个组件即可快速修复。
* 方便设置叶片攻角。

#### HobbyKing 旋翼叶片

可以将打印的中央部件与原始叶片一起使用。
这些叶片可以在 [HobbyKing](https://hobbyking.com/en_us/duraflytm-auto-g-gyrocopter-821mm-replacement-main-blade-1pcs-bag.html) 购买。
HobbyKing 叶片的重心位置不同，因此需要正确平衡。

#### 3D打印旋翼叶片

也可以打印旋翼叶片。

打印的旋翼叶片仍在开发中，但初步测试显示其质量更好，主要得益于精确的形状和没有纵向沟槽。
然而，部分生产工艺仍需调整。

![叶片组装](../../assets/airframes/autogyro/auto-g2/modif_blade.png)

#### 平衡

适当的叶片平衡对于最小化振动非常重要。
叶片必须平衡到重心位于旋翼轴中心。

打印的叶片在生产过程中已经平衡，无需进一步平衡。

### 释放装置

如果要使用绞盘或牵引方式发射自转旋翼，需要打印释放装置。
它是一个配备舵机的小型盒子，用于拉出销钉并释放绳索。

整个部件使用热熔胶粘合，粘在发动机下方自转旋翼机身底部。
如果自转旋翼通过绳索牵引，其发动机必须关闭。
可以通过在发射装置开关闭合时在遥控器上将发动机输出置零来实现。

![释放装置](../../assets/airframes/autogyro/auto-g2/modif_release.png)

## 零部件清单

### 电子元件

* 飞控 ([Holybro pix32](../flight_controller/holybro_pix32.md), [CUAV nano](../flight_controller/cuav_v5_nano.md))
* GPS (GPS模块NEO-6M，带贴片天线)
* 空速传感器 ([SDP3x](https://www.sensirion.com/en/flow-sensors/differential-pressure-sensors/worlds-smallest-differential-pressure-sensor/))
* 可替换的更耐用舵机(可选)，([BlueBird BMS-125WV](https://www.blue-bird-model.com/products_detail/411.htm))
* 释放装置用附加舵机(可选)

### 机械部件

* 旋翼头轴承 (623 2Z C3)
* 螺旋桨 ([APC 10x7](https://www.apcprop.com/product/10x7e/))
* [螺旋桨适配器](https://mpjet.com/shop/gb/prop-adapters/184-collet-prop-adapter-19-mm-4-mm-shaft-m629-standard.html)

### 可打印部件

* 旋翼头：
  * [支架末端](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/111_1001.stl)
  * [变距部分](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/111_1002.stl)
  * [滚转部分](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/111_1003.stl)

* 旋翼：
  * [中心垫片顶部](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/111_1008.stl)
  * [中心垫片底部](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/111_1004.stl)
  * [带变形区的中心板](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/888_1001.stl)
  * [设定桨叶迎角的垫片](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/111_1005.stl)
  * [旋翼螺母](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/888_1002.stl)

* 旋翼桨叶(可选)
* 飞控支架
* [释放装置](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/888_1010.stl)
* [前轮](https://github.com/ThunderFly-aerospace/Auto-G2/blob/master/CAD/stl/888_1011.stl)

### 推荐备用件

* 质量更优的舵机(推荐 [BlueBird BMS-125WV](https://www.blue-bird-model.com/products_detail/411.htm)，原装舵机耐用性较差)
* 螺旋桨 ([APC 10x7](https://www.apcprop.com/product/10x7e/))
* 带变形区的旋翼中心板(3D打印)
* 旋翼桨叶 ([HobbyKing](https://hobbyking.com/en_us/duraflytm-auto-g-gyrocopter-821mm-replacement-main-blade-1pcs-bag.html) 或 3D打印)

## 视频

<lite-youtube videoid="YhXXSWz5wWs" title="[ThunderFly] 3D打印的自转旋翼转子"/>## 照片变更图集

![Auto-G2 1](../../assets/airframes/autogyro/auto-g2/autog2_1.jpg)
![Auto-G2 2](../../assets/airframes/autogyro/auto-g2/autog2_2.jpg)
![Auto-G2 3](../../assets/airframes/autogyro/auto-g2/autog2_3.jpg)
![Auto-G2 4](../../assets/airframes/autogyro/auto-g2/autog2_4.jpg)