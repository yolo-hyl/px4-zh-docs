# VTOLs

PX4 使用 VTOL 一词来指代支持固定翼飞机("airplane")的前飞模式和直升机/多旋翼飞行器的垂直起降能力的机体。

![Vertical Technologies: Deltaquad QuadPlane VTOL](../../assets/airframes/vtol/vertical_technologies_deltaquad/hero.jpg)

VTOL 机体结合了多旋翼飞行器和固定翼飞行的优势：

- **垂直起降：** 即使经验不足的飞行员也能在几乎任何地点起降。
- **快速且高效的固定翼飞行：** 执行速度更快、航程更远、时间更长的任务，可携带更重的有效载荷。
- **悬停能力：** 为摄影、结构扫描等提供稳定的平台。

本节描述 PX4 支持的 VTOL 类型和配置，并提供组装、配置和飞行的高层指导。

## VTOL 类型

PX4 支持三种最重要的 VTOL 类型。

<div class="grid_wrapper three_column">
  <div class="grid_item">
    <div class="grid_item_heading"><a href="tailsitter.html" title="Tailsitter"><big>尾座式</big></a></div>
    <div class="grid_text">
    旋翼永久固定在固定翼位置。
    通过尾部起降。整个机体向前倾斜进入前飞状态。
    <img src="../../assets/airframes/vtol/wingtraone/hero.jpg" title="wingtraone" />
    <ul>
      <li>结构简单且坚固</li>
      <li>执行器数量最少</li>
      <li>在风中可能难以控制</li>
      <li>悬停和前飞效率存在权衡，因为使用相同的执行器</li>
    </ul>
    </div>
  </div>
<div class="grid_item">
  <div class="grid_item_heading"><a href="tiltrotor.html" title="Tiltrotor"><big>倾转旋翼机</big></a></div>
  旋翼旋转90度以实现多旋翼与前飞状态的转换。
  通过腹部起降。
  <div class="grid_text">
  <img src="../../assets/airframes/vtol/eflite_convergence_pixfalcon/hero.jpg" title="Eflight Confvergence" />
  <ul>
    <li>需要额外的执行器用于电机倾转</li>
    <li>机械结构复杂的倾转机构</li>
    <li>由于控制权限更高，悬停时比尾座式更容易控制</li>
  </ul>
  </div>
</div>
<div class="grid_item">
  <div class="grid_item_heading"><a href="standardvtol.html" title="Standard VTOL"><big>标准 VTOL</big></a></div>
  <div class="grid_text">
  多旋翼和前飞状态分别配备独立的旋翼/飞行控制系统。通过腹部起降。
  <img src="../../assets/airframes/vtol/vertical_technologies_deltaquad/hero_small.png" title="Vertical Technologies: Deltaquad" />
  <ul>
    <li>需要额外重量以容纳独立的悬停/前飞推进系统</li>
    <li>由于专用悬停/前飞执行器，控制最简单</li>
    <li>可悬停</li>
    <li>前飞推进可使用燃油发动机</li>
  </ul>
  </div>
 </div>
</div>

总体而言，随着机械复杂性的增加，机体更易于飞行，但成本和重量也随之增加。
每种类型都有优缺点，目前已有基于所有类型的成功商业案例。

在上述每种主要类型中，可能存在多种变体——例如电机数量、电机几何结构、飞行面等。
PX4 为许多常见机体配置提供了_airframe configurations_（机体配置）。
支持的配置列表见 [Airframes Reference > VTOL](../airframes/airframe_reference.md#vtol)。

::: info

- 如果所需机体配置未被支持，可能需要[添加新机体](../dev_airframes/adding_a_new_frame.md)（需要一定的[PX4 Development](../development/development.md)开发经验）。
- VTOL 代码库与其他所有机体的代码库相同，仅添加了额外的控制逻辑，特别是在转换过程中。

:::

## 飞行与飞行模式

VTOL飞机可以以多旋翼模式或固定翼机体模式飞行。
多旋翼模式主要用于起降，而固定翼模式用于高效飞行和/或任务执行。

当以多旋翼模式飞行时，VTOL机体的飞行模式与[multicopter](../flight_modes_mc/index.md)相同；当以固定翼模式飞行时，飞行模式与[fixed-wing](../flight_modes_fw/index.md)相同。

模式切换可以通过遥控器上的开关由飞行员手动触发，或在任务或自动模式需要时由PX4自动触发。

## 组装

::: info
有关商业和套件VTOL机体的信息请参见：[完整机体](../complete_vehicles/index.md)
:::

PX4控制的机体通常共享相同的核组件：飞控连接到电源系统、GPS、外部指南针（强烈推荐）、遥控系统（可选）和/或遥测无线电系统（可选）、以及空速传感器（对VTOL机体强烈推荐）。

飞控的输出端连接到机体电机电调和/或飞行控制舵机与执行器，这些设备由独立的电源供电。

飞控输出端与特定控制/电机的映射关系取决于所使用的机体框架，并在 [机体参考 > VTOL](../airframes/airframe_reference.md#vtol) 中指定。

组装信息包含在多个章节中：

- [基础组装](../assembly/index.md) 展示了多种流行[飞控](../flight_controller/index.md)的核心组件设置。
  对于没有指南的飞控，其安装方式通常类似（几乎总是包含类似的设置指南）。
- [外设](../peripherals/index.md) 包含其他外设的信息，包括[空速传感器](../sensor/airspeed.md)。
- [机体参考 > VTOL](../airframes/airframe_reference.md#vtol) 解释了不同机体配置中飞控输出必须连接到哪些飞行控制：
  - 如果有适用于您机体的配置，请选择它（这通常已预调校至可飞行状态，可能仅需微调）。
  - 否则选择一个与您的机体匹配的"Generic Airframe"（通用机体）。

此外，展示其他人如何设置不同类型机体的构建日志作为子主题提供。
例如参见 [FunCub QuadPlane](../frames_vtol/vtol_quadplane_fun_cub_vtol_pixhawk.md)。

## 配置

VTOL配置涉及多个章节：

- [基本配置](../config/index.md) - 所有机体类型通用的配置(传感器、安全系统、电池等)
- [VTOL特定配置](../config_vtol/index.md)
- [外设硬件](../peripherals/index.md) - 可选硬件和传感器的配置
- [高级配置](../advanced_config/index.md)：涵盖出厂调校、高级及可选配置的附加配置

## 视频

### 教育类

VTOL控制与空速故障检测（PX4开发者峰会2019）

<lite-youtube videoid="37BIBAzD6fE" title="VTOL control and airspeed fault detection"/>

<!-- 20190704 -->

### 尾座式

[UAV Works VALAQ Patrol尾座式无人机](https://www.valaqpatrol.com/valaq_patrol_technical_data/)

<lite-youtube videoid="pWt6uoqpPIw" title="UAV Works VALAQ"/>

[TBS Caipiroshka](../frames_vtol/vtol_tailsitter_caipiroshka_pixracer.md)

<lite-youtube videoid="acG0aTuf3f8" title="PX4 VTOL - Call for Testpilots"/>

### 倾转旋翼

[Convergence倾转旋翼](../frames_vtol/vtol_tiltrotor_eflite_convergence_pixfalcon.md)

<lite-youtube videoid="E61P2f2WPNU" title="E-flite Convergence Autonomous Mission Flight"/>

### 旋翼-固定翼混合VTOL

[FunCub旋翼-固定翼混合](../frames_vtol/vtol_quadplane_fun_cub_vtol_pixhawk.md)

<lite-youtube videoid="4K8yaa6A0ks" title="Fun Cub PX4 VTOL Maiden"/>

[Falcon Vertigo旋翼-固定翼混合](../frames_vtol/vtol_quadplane_falcon_vertigo_hybrid_rtf_dropix.md)

<lite-youtube videoid="h7OHTigtU0s" title="PX4 Vtol test"/>

[Ranger旋翼-固定翼混合](../frames_vtol/vtol_quadplane_volantex_ranger_ex_pixhawk.md)

<lite-youtube videoid="7tGXkW6d3sA" title="PX4 Autopilot - Experimental VTOL with Pixhawk and U-Blox M8N GPS"/>