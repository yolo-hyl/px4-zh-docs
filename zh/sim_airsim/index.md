# AirSim 模拟器

:::warning
此模拟器由[社区支持和维护](../simulation/community_supported_simulators.md)。
它可能与当前版本的 PX4 一起工作，也可能不工作。

有关核心开发团队支持的环境和工具的信息，请参见[工具链安装](../dev_setup/dev_env.md)。
:::

[AirSim](https://microsoft.github.io/AirSim/) 是一个开源的跨平台无人机模拟器，基于 _Unreal Engine_ 构建。
它通过硬件在环（HITL）或软件在环（SITL）提供 Pixhawk/PX4 的物理和视觉真实模拟。

<lite-youtube videoid="-WfTr1-OBGQ" title="AirSim Demo"/>

<!-- datestamp:video:youtube:20170216:AirSim Demo -->

## PX4 设置

[PX4 在 AirSim 中的设置](https://microsoft.github.io/AirSim/px4_setup/) 描述了如何通过 [SITL](https://microsoft.github.io/AirSim/px4_sitl/) 和 [HITL](https://microsoft.github.io/AirSim/px4_setup/#setting-up-px4-hardware-in-loop) 将 PX4 与 AirSim 集成使用。

## 视频

#### 基于 WSL 2 的 AirSim 与 PX4

<lite-youtube videoid="DiqgsWIOoW4" title="AirSim with PX4 on WSL 2"/>

<!-- datestamp:video:youtube:20210401:AirSim with PX4 on WSL 2 -->

::: info
WSL 2 不是支持的 [PX4 Windows 开发环境](../dev_setup/dev_env_windows_cygwin.md)，主要原因是难以在正常 Windows 环境中显示 WSL 2 内运行的模拟器 UI。
此限制不适用于 AirSim，因为其 UI 在 Windows 中原生运行。
:::

#### Microsoft AirSim：研究与工业应用（PX4 开发者峰会 2020 虚拟会议）

<lite-youtube videoid="-YMiKaJYl44" title="Microsoft AirSim: Applications to Research and Industry"/>

<!-- datestamp:video:youtube:20200716:Microsoft AirSim: Applications to Research and Industry — PX4 Developer Summit Virtual 2020 -->

#### 使用 AirSim 和 PX4 进行自主无人机巡检（PX4 开发者峰会 2020 虚拟会议）

<lite-youtube videoid="JDx0MPTlhrg" title="Autonomous Drone Inspections using AirSim and PX4"/>

<!-- datestamp:video:youtube:20200716:Autonomous Drone Inspections using AirSim and PX4 — PX4 Developer Summit Virtual 2020 -->

## 更多信息

- [AirSim 文档](https://microsoft.github.io/AirSim/)
- [使用 AirSim 模拟自主无人机进行航空器检查](https://gaas.gitbook.io/guide/case-study/using-airsim-to-simulate-aircraft-inspection-by-autonomous-drones)（来自 Generalized Autonomy Aviation System (GAAS) 项目的案例研究）。