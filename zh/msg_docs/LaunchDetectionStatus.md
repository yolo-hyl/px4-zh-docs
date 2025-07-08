# LaunchDetectionStatus (UORB message)

发射检测状态机的状态（仅适用于固定翼）

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/LaunchDetectionStatus.msg)

```c
# 发射检测状态机的状态（仅适用于固定翼）

uint64 timestamp # 系统启动后的时间（微秒）

uint8 STATE_WAITING_FOR_LAUNCH 			= 0 # 等待发射
uint8 STATE_LAUNCH_DETECTED_DISABLED_MOTOR 	= 1 # 检测到发射，但保持电机禁用（例如在弹射器上时电机无法自由旋转）
uint8 STATE_FLYING 				= 2 # 检测到发射，使用正常起飞/飞行配置

uint8 launch_detection_state

```