# 固定翼滑跑控制 (UORB消息)

固定翼滑跑起飞/降落的辅助控制字段

将信息从FixedWingModeManager传递到FixedWingAttitudeController

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FixedWingRunwayControl.msg)

```c
# Auxiliary control fields for fixed-wing runway takeoff/landing

# Passes information from the FixedWingModeManager to the FixedWingAttitudeController

uint64 timestamp # [us] time since system start

bool wheel_steering_enabled		# Flag that enables the wheel steering.
float32 wheel_steering_nudging_rate	# [norm] [@range -1, 1] [FRD] Manual wheel nudging, added to controller output. NAN is interpreted as 0.

```