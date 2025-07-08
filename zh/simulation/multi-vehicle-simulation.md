# 多机体仿真

PX4 支持使用以下模拟器进行多机体仿真：

- [使用Gazebo的多机体仿真](../sim_gazebo_gz/multi_vehicle_simulation.md)（支持与不支持ROS两种情况）
- [使用Gazebo Classic的多机体仿真](../sim_gazebo_classic/multi_vehicle_simulation.md)（支持与不支持ROS两种情况）
- [使用FlightGear的多机体仿真](../sim_flightgear/multi_vehicle.md)
- [使用JMAVSim的多机体仿真](../sim_jmavsim/multi_vehicle.md)

选择模拟器时需考虑以下因素：待仿真的机体类型、对仿真精度的要求（以及需要支持哪些功能），以及同时需要仿真多少机体：

- FlightGear 是精度最高的模拟器，因此也是资源占用最重的。如果需要高精度仿真但不需要同时模拟太多机体，可以选择该模拟器。此外，当需要同时模拟不同类型机体时，也可以使用该模拟器。
- [Gazebo](../sim_gazebo_gz/index.md) 的精度和资源占用均低于FlightGear，但支持许多FlightGear不支持的功能。相比FlightGear，Gazebo可以同时模拟更多机体，并且允许同时模拟不同类型机体。仅支持Ubuntu 20.04及更高版本。注意，这是[Gazebo Classic](../sim_gazebo_classic/index.md)（下文）的继任版本。
- [Gazebo Classic](../sim_gazebo_classic/index.md) 的精度和资源占用均低于FlightGear，但支持许多FlightGear不支持的功能和机体类型。相比FlightGear，它可以同时模拟更多机体，并且允许同时模拟不同类型机体。
- JMAVSim 是一个资源占用非常轻量的模拟器，但仅支持多旋翼无人机。如果需要同时模拟大量多旋翼无人机且对精度要求不高，建议使用该模拟器。