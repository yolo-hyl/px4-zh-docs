# TBS Caipiroshka

Caipiroshka VTOL 是对 *TBS Caipirinha* 的小幅改进型号。

::: info
*TBS Caipirinha* 已被替代且不再销售。
这些说明 *应该* 适用于更新后的机体：[TBS Caipirinha 2](https://team-blacksheep.com/products/prod:tbs_caipi2_pnp)。
零件清单中的一些组件也进行了更新。
:::

<lite-youtube videoid="acG0aTuf3f8" title="PX4 VTOL - Call for Testpilots"/>

## 零件清单

* TBS Caipirinha 机翼（已停售 - 尝试 [TBS Caipirinha 2](https://team-blacksheep.com/products/prod:tbs_caipi2_pnp)) 
* 3D打印左右电机支架 (<a href="https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/vtol/caipiroshka/motor_mounts.zip" target="_blank">设计文件</a>)
* CW 8045 螺旋桨 ([Eflight store](https://www.banggood.com/GEMFAN-Carbon-Nylon-8045-CWCCW-Propeller-For-Quads-1-Pair-p-950874.html))
* CCW 8045 螺旋桨 ([Eflight store](https://www.banggood.com/GEMFAN-Carbon-Nylon-8045-CWCCW-Propeller-For-Quads-1-Pair-p-950874.html))
* 2x 1800 kV 120-180W 电机
  * [ePower 2208](https://www.galaxus.ch/en/s5/product/epower-22081400-fuer-2-3-lipo-imax-rc-motors-8355913)
  * [Armattan 2208 1800kV Multirotor Motor](https://www.amazon.com/Armattan-2208-1800kV-Multirotor-Motor/dp/B00UWLW0C8)
    <!-- 等效替代必须匹配：kV (1800)、电机尺寸 (2208) 和锂电池节数 (3S)。 -->
* 2x 20-30S 电调
  * [GetFPV](https://www.getfpv.com/lumenier-30a-blheli-s-esc-opto-2-4s.html)
* BEC (3A, 5-5.3V)（仅当使用无法为输出轨道提供5V电源的电调时需要）
* 3S 2200 mA 锂电池
  * Team Orion 3S 11.1V 50 C ([Hobbyshop store](https://www.hobbyshop.ch/modellbau-elektronik/akku/team-orion-lipo-2200-3s-11-1v-50c-xt60-ori60163.html))
* [Pixracer 自动驾驶仪板 + 电源模块](../flight_controller/pixracer.md)
* [数字空速传感器](https://hobbyking.com/en_us/hkpilot-32-digital-air-speed-sensor-and-pitot-tube-set.html)

## 组装

下图展示了完全组装后的 Caipiroshka 样例。

![Caipiroshka](../../assets/airframes/vtol/caipiroshka/caipiroshka.jpg)

以下是一些关于如何构建机体的一般性建议。

### 自动驾驶仪

将自动驾驶仪安装在机体重心附近中央位置。

### 电机支架

打印2个电机支架（STL文件链接在零件清单中）。
将每个电机支架安装在机翼两侧，使电机轴线大致穿过副翼的中心（见图示）。
上图中两个电机支架的水平距离为56cm。
在机翼上标记正确位置后，可以在机翼上下表面粘贴标准透明胶带覆盖接触区域。
然后在此区域涂抹热熔胶并粘接电机支架。
在机翼表面和热熔胶之间加胶带的目的是，可以通过撕掉胶带轻松移除电机支架而不会损坏机翼。
这对于更换损坏的电机支架非常有用。

### 电机控制器

可将电机控制器直接粘接或用扎带固定在电机支架的平面上。
需要将电源线引向电池舱，可用旧电烙铁在泡沫中熔出通道。
将两个电机控制器的电源线在电池舱连接并焊接插头。
这将使电机控制器能够连接到电源模块。
如果没有能为自动驾驶仪输出轨道提供5V的电机控制器，则需要使用外部电源（BEC）。

### GPS

GPS可安装在机体最尾部中央位置。这有助于将重量转移到机尾，因为两个电机、相机和更大的电池可能会使机体过于机头重。
此外，与12V电源线保持较远距离有助于减少外部磁力计的电磁干扰。

### 空速传感器

将皮托管安装在机翼一侧的外侧边缘附近。
确保皮托管不受螺旋桨气流影响。如果管子到电机轴线的水平距离大于螺旋桨半径则可以接受。
使用例如旧电烙铁在机翼上开槽以容纳皮托管、管路和实际传感器（见图示）。
开槽以便将电缆穿过机翼连接到其他组件。

### 传感器与I2C总线连接

空速传感器和外部磁力计（位于GPS外壳中）需要连接到自动驾驶仪的I2C总线。
因此需要使用I2C分路器（如零件清单中所示）。将分路器板连接到自动驾驶仪的I2C总线。
然后使用标准I2C线缆将外部磁力计和空速传感器连接到分路器板。
上图中分路器板位于GPS单元左侧。

### 副翼

使用透明胶带将副翼固定在机翼背面。可按照Team Blacksheep为TBS Caiprinha机体提供的组装手册中的说明操作。

### 通用组装规则

在将所有组件安装到机翼之前，先用胶带固定在大致位置，并检查机翼的重心是否在TBS Caipirinha组装手册中规定的推荐范围内。
根据想要搭载的附加组件（例如前置GoPro或更大电池），可能需要调整组件位置。

## 机体配置

### 电机与舵机配置

| 通道 | 频率 | 连接设备                  |
|------|------|---------------------------|
| MAIN5 | 50 Hz | 右侧（外侧）副翼舵机       |
| MAIN6 | 50 Hz | 左侧（内侧）副翼舵机       |