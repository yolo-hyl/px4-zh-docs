# VehicleImu (UORB message)

以国际单位制形式表示的IMU读数。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleImu.msg)

```c
# IMU readings in SI-unit form.

uint64 timestamp          # time since system start (microseconds)
uint64 timestamp_sample

uint32 accel_device_id          # Accelerometer unique device ID for the sensor that does not change between power cycles
uint32 gyro_device_id           # Gyroscope unique device ID for the sensor that does not change between power cycles

float32[3] delta_angle          # delta angle about the FRD body frame XYZ-axis in rad over the integration time frame (delta_angle_dt)
float32[3] delta_velocity       # delta velocity in the FRD body frame XYZ-axis in m/s over the integration time frame (delta_velocity_dt)

uint32 delta_angle_dt           # integration period in microseconds
uint32 delta_velocity_dt        # integration period in microseconds

uint8 CLIPPING_X = 1
uint8 CLIPPING_Y = 2
uint8 CLIPPING_Z = 4

uint8 delta_angle_clipping     # bitfield indicating if there was any gyro clipping (per axis) during the integration time frame
uint8 delta_velocity_clipping   # bitfield indicating if there was any accelerometer clipping (per axis) during the integration time frame

uint8 accel_calibration_count  	# Calibration changed counter. Monotonically increases whenever accelerometer calibration changes.
uint8 gyro_calibration_count   	# Calibration changed counter. Monotonically increases whenever rate gyro calibration changes.
```