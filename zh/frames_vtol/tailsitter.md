# 尾坐式 VTOL

**尾坐式 VTOL（Tailsitter VTOL）** 通过尾部垂直起降，但在正常飞行时会翻转成固定翼姿态。尾坐式 VTOL 的旋翼在向前飞行时是固定不动的。

尾坐式 VTOL 通常比 [其他类型的 VTOL](../frames_vtol/index.md) 机械结构更简单，因此可能更易于制造和维护。然而它们在气动设计上更复杂，尤其是在悬停和模式转换时的飞行和调参难度较高，尤其在有风条件下。

## 尾坐式 VTOL 机架类型

:::: tabs

::: tab 双旋翼尾坐式（Duo Tailsitter）
使用升降副翼（elevons）从悬停切换到固定翼飞行的双旋翼 VTOL。

![Wingtra: WingtraOne VTOL 双旋翼尾坐式](../../assets/airframes/vtol/wingtraone/hero.jpg)

- 前进飞行效率更高
- 悬停飞行更难操控，尤其在有风条件下
- 悬停和转换模式更难调参
- 机身更紧凑

:::

::: tab 多旋翼尾坐式（VTOL Tailsitter）
可选配升降副翼的 VTOL，通过旋翼（及可能的升降副翼）实现模式转换。

![Skypull SP-1 VTOL 四旋翼尾坐式](../../assets/airframes/vtol/skypull/skypull_sp1.jpg)

- 悬停模式更易操控且稳定性更高
- 机身结构不紧凑（运输更困难）
- 支持 "X" 和 "+" 布局（参见机架参考文档）

:::

::::

双旋翼尾坐式通常在巡航飞行中效率更高（4 个小型螺旋桨的效率低于 2 个大型螺旋桨），且物理结构更紧凑。然而，由于其在悬停模式下气动复杂度更高，因此在悬停和模式转换的调参上难度更大。四旋翼尾坐式在悬停模式下更易操控，且在有风条件下更稳定。PX4 对两种类型均提供支持。

## 设置与飞行

VTOL 的设置和飞行操作详见 [VTOL](../frames_vtol/index.md) 主文档。

::: info
所有 VTOL 的设置步骤基本相同。主要差异在于电机接线方式以及部分配置调参细节。
:::

## 构建日志

以下是针对尾坐式机架的 PX4 设置分步指南：

- [TBS Caipiroshka 尾坐式 VTOL 构建 (Pixracer)](../frames_vtol/vtol_tailsitter_caipiroshka_pixracer.md)

:::tip
建议同时查阅其他 PX4 VTOL 和多旋翼（Copter）的构建日志（大部分设置步骤相同）。
:::

## 视频

本节包含尾坐式 VTOL 特有的视频（通用 VTOL 视频请参见 [VTOL](../frames_vtol/index.md)）。

### 双旋翼（Duo）

---

[TBS Caipiroshka](../frames_vtol/vtol_tailsitter_caipiroshka_pixracer.md) - 尾坐式起飞（特写）、悬停、平飞、模式转换。

<lite-youtube videoid="acG0aTuf3f8" title="PX4 VTOL - Call for Testpilots"/>

---

[Woshark](http://www.laarlab.cn/#/) _PX4 尾坐式原型_ - 起飞、转换、降落。

<!-- 提供者：slack 用户 xdwgood: https://github.com/PX4/PX4-user_guide/issues/2328#issuecomment-1467234118 -->
<!-- 更新问题：https://github.com/PX4/PX4-user_guide/issues/3007 -->

<lite-youtube videoid="gjHj6YsxcZk" title="PX4 Autopilot Tailsitter"/>

### 四旋翼（Quad）

[UAV Works VALAQ Patrol 尾坐式](https://www.valaqpatrol.com/valaq_patrol_technical_data/) - 起飞、转换、降落。

<lite-youtube videoid="pWt6uoqpPIw" title="UAV Works VALAQ"/>

## 图库

<div class="grid_wrapper three_column">
  <div class="grid_item">
    <div class="grid_item_heading"><big><a href="https://wingtra.com/mapping-drone-wingtraone/">WingtraOne</a></big></div>
    <div class="grid_text">
    <img src="../../assets/airframes/vtol/wingtraone/hero.jpg" title="Wingtra: WingtraOne VTOL Duo Tailsitter" alt="wingtraone" />
    </div>
  </div>
  <div class="grid_item">
    <div class="grid_item_heading"><big><a href="https://www.skypull.technology/">Skypull</a></big></div>
    <div class="grid
_text">
      <img title="Skypull SP-1 VTOL QuadTailsitter" src="../../assets/airframes/vtol/skypull/skypull_sp1.jpg" />
    </div>
  </div>
  <div class="grid_item">
    <div class="grid_item_heading"><big><a href="../frames_vtol/vtol_tailsitter_caipiroshka_pixracer.html">TBS Caipiroshka</a></big></div>
    <div class="grid_text">
      <img title="TBS Caipiroshka" src="../../assets/airframes/vtol/caipiroshka/caipiroshka.jpg" />
    </div>
  </div>
  <div class="grid_item">
    <div class="grid_item_heading"><big><a href="http://uav-cas.ac.cn/WOSHARK/">Woshark</a></big></div>
    <div class="grid_text">
      <img title="Woshark" src="../../assets/airframes/vtol/xdwgood_ax1800/hero.jpg" />
    </div>
  </div>
  <div class="grid_item">
    <div class="grid_item_heading"><big><a href="https://www.valaqpatrol.com/valaq_patrol_technical_data/">UAV Works VALAQ Patrol Tailsitter</a></big></div>
    <div class="grid_text">
      <img title="UAV Works VALAQ Patrol Tailsitter" src="../../assets/airframes/vtol/uav_works_valaq_patrol/hero.jpg" />
    </div>
  </div>
</div>