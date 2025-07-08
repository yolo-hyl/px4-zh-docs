# 模块参考：估计器

## AttitudeEstimatorQ

源代码: [modules/attitude_estimator_q](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/attitude_estimator_q)

### 描述
姿态估计器Q。

### 用法 {#AttitudeEstimatorQ_usage}

```
AttitudeEstimatorQ <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## 空速估计器

源代码: [modules/airspeed_selector](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/airspeed_selector)

### 描述
此模块提供一个`airspeed_validated`话题，包含指示空速（IAS）、校准空速（CAS）、真实空速（TAS）以及当前估计是否无效，以及是基于传感器读数还是地速减去风速的信息。  
支持多个"原始"空速输入，当检测到传感器故障时，该模块会自动切换到有效传感器。为了故障检测以及从IAS到CAS的缩放因子估计，它运行多个风速估计器并发布这些结果。

### 用法 {#airspeed_estimator_usage}

```
airspeed_estimator <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## ekf2

源代码: [modules/ekf2](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/ekf2)

### 描述
使用扩展卡尔曼滤波器的姿态和位置估计器，适用于多旋翼和固定翼。  
相关文档请参阅[ECL/EKF 概览与调参](https://docs.px4.io/main/en/advanced_config/tuning_the_ecl_ekf.html)页面。  
`ekf2`可以以回放模式启动（`-r`）：在此模式下，它不访问系统时间，仅使用传感器话题的时间戳。

### 用法 {#ekf2_usage}

```
ekf2 <command> [arguments...]
 Commands:
   start
     [-r]        启用回放模式

   stop

   status        打印状态信息
     [-v]        详细模式（打印所有状态和完整协方差矩阵）

   select_instance 请求切换到新的估计器实例
     <instance>  指定目标估计器实例
```

## 局部位置估计器

源代码: [modules/local_position_estimator](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/local_position_estimator)

### 描述
使用扩展卡尔曼滤波器的姿态和位置估计器。

### 用法 {#local_position_estimator_usage}

```
local_position_estimator <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```

## 多旋翼悬停推力估计器

源代码: [modules/mc_hover_thrust_estimator](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mc_hover_thrust_estimator)

### 描述

### 用法 {#mc_hover_thrust_estimator_usage}

```
mc_hover_thrust_estimator <command> [arguments...]
 Commands:
   start

   stop

   status        打印状态信息
```