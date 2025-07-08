# 飞行控制器选择

飞行控制器是无人机体的"大脑"。  
PX4 可以运行在 [多种飞行控制器板](../flight_controller/index.md) 上。  

您应选择一款适合机体物理限制、预期执行的任务以及成本的板载控制器。  

<img src="../../assets/flight_controller/pixhawk6x/pixhawk6x_hero_upright.png" width="120px" title="Holybro Pixhawk6X"><img src="../../assets/flight_controller/cuav_pixhawk_v6x/pixhawk_v6x.jpg" width="200px" title="CUAV Pixhawk 6X" ><img src="../../assets/flight_controller/cube/orange/cube_orange_hero.jpg" width="250px" title="CubePilot Cube Orange" />  

## Pixhawk系列  

[Pixhawk系列](../flight_controller/pixhawk_series.md) 开源硬件飞行控制器基于 NuttX OS 运行 PX4。  
凭借多种外形规格，该系列针对不同使用场景和市场细分领域提供了专门版本。  

[Pixhawk标准自动驾驶仪](../flight_controller/autopilot_pixhawk_standard.md) 被用作 PX4 的参考平台。  
这些控制器由 PX4 开发团队支持和测试，强烈推荐使用。  

## 制造商支持的控制器  

其他飞行控制器属于 [制造商支持](../flight_controller/autopilot_manufacturer_supported.md) 类型。  
这包括基于 Pixhawk 标准开发（但未完全兼容）的飞控，以及许多其他类型控制器。  

请注意，制造商支持的控制器可能与 Pixhawk 标准控制器同样"优秀"（甚至更好）。  

## 适用于计算密集型任务的自动驾驶仪  

专用飞行控制器（如 Pixhawk）通常不适合通用计算或运行计算密集型任务。  
如需更强的计算能力，最常见做法是将这些应用运行在独立的 [伴飞计算机](../companion_computer/index.md) 上。  
部分伴飞计算机也可在自动驾驶仪板上的独立 DSP 上运行 PX4。  

同样，PX4 也可以原生运行在 Raspberry Pi 上（这种方式通常不如使用独立伴飞计算机或专用 DSP 那样"稳健"）：  

- [Raspberry Pi 2/3 Navio2](../flight_controller/raspberry_pi_navio2.md)  
- [Raspberry Pi 2/3/4 PilotPi Shield](../flight_controller/raspberry_pi_pilotpi.md)  

## 可运行 PX4 的商业无人机  

PX4 可用于许多流行的商业无人机产品，包括出厂即搭载 PX4 的产品，以及可通过更新安装 PX4 的产品（这将为您的机体添加任务规划功能和其他 PX4 飞行模式）。  

更多详情请参见 [完整机体](../complete_vehicles/index.md)。