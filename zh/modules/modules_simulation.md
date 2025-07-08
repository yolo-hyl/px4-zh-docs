# 模块参考：模拟器

## simulator_sih

源码地址：[modules/simulation/simulator_sih](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/simulator_sih)

### 描述
该模块为四旋翼和固定翼飞行器提供完全运行在硬件飞控中的模拟器。

该模拟器订阅"actuator_outputs"主题，该主题由控制分配模块提供的执行器PWM信号。

该模拟器发布带有真实噪声干扰的传感器信号，以实现状态估计算法的闭环。

### 实现
模拟器通过矩阵代数实现运动方程。
姿态表示采用四元数。
积分方法采用前向欧拉法。
大部分变量在.hpp文件中声明为全局变量以避免栈溢出。

### 用法 {#simulator_sih_usage}

```
simulator_sih <command> [arguments...]
命令:
  start

  stop

  status        打印状态信息
```