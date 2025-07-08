# GeneratorStatus (UORB 消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GeneratorStatus.msg)

```c
uint64 timestamp			# 自系统启动以来的时间（微秒）


uint64 STATUS_FLAG_OFF                              = 1       # 发电机关闭。
uint64 STATUS_FLAG_READY                            = 2       # 发电机已准备好发电。
uint64 STATUS_FLAG_GENERATING                       = 4       # 发电机正在发电。
uint64 STATUS_FLAG_CHARGING                         = 8       # 发电机正在为电池充电（发电功率足以供电负载并充电）。
uint64 STATUS_FLAG_REDUCED_POWER                    = 16      # 发电机处于降功率运行状态。
uint64 STATUS_FLAG_MAXPOWER                         = 32      # 发电机输出达到最大功率。
uint64 STATUS_FLAG_OVERTEMP_WARNING                 = 64      # 发电机接近最高工作温度，冷却不足。
uint64 STATUS_FLAG_OVERTEMP_FAULT                   = 128     # 发电机达到最高工作温度并已关机。
uint64 STATUS_FLAG_ELECTRONICS_OVERTEMP_WARNING     = 256     # 功率电子设备接近最高工作温度，冷却不足。
uint64 STATUS_FLAG_ELECTRONICS_OVERTEMP_FAULT       = 512     # 功率电子设备达到最高工作温度并已关机。
uint64 STATUS_FLAG_ELECTRONICS_FAULT                = 1024    # 功率电子设备发生故障并已关机。
uint64 STATUS_FLAG_POWERSOURCE_FAULT                = 2048    # 发电机供电源发生故障（例如机械发电机停止、系留电源中断、太阳能板处于阴影中、氢反应停止）。
uint64 STATUS_FLAG_COMMUNICATION_WARNING            = 4096    # 发电机控制器通信存在问题。
uint64 STATUS_FLAG_COOLING_WARNING                  = 8192    # 功率电子或发电机冷却系统错误。
uint64 STATUS_FLAG_POWER_RAIL_FAULT                 = 16384   # 发电机控制器供电轨发生故障。
uint64 STATUS_FLAG_OVERCURRENT_FAULT                = 32768   # 发电机控制器超过过电流阈值并关机以防止损坏。
uint64 STATUS_FLAG_BATTERY_OVERCHARGE_CURRENT_FAULT = 65536   # 发电机控制器检测到电池充电电流过高并关机以防止电池损坏。 |
uint64 STATUS_FLAG_OVERVOLTAGE_FAULT                = 131072  # 发电机控制器超过过电压阈值并关机以防止电压超限。
uint64 STATUS_FLAG_BATTERY_UNDERVOLT_FAULT          = 262144  # 电池电压过低（发电机无法启动）。
uint64 STATUS_FLAG_START_INHIBITED                  = 524288  # 发电机启动被抑制（例如安全开关锁定）。
uint64 STATUS_FLAG_MAINTENANCE_REQUIRED             = 1048576 # 发电机需要维护。
uint64 STATUS_FLAG_WARMING_UP                       = 2097152 # 发电机尚未准备好发电。
uint64 STATUS_FLAG_IDLE                             = 4194304 # 发电机处于空闲状态。


float32 battery_current            # [A] 电池充放电电流。正值表示放电，负值表示充电。NaN：未提供该字段。
float32 load_current               # [A] 供给无人机的电流。若电池电流不可用，则为发电机直流输出电流。正值表示放电，负值表示充电。NaN：未提供该字段
float32 power_generated            # [W] 当前发电功率。NaN：未提供该字段
float32 bus_voltage                # [V] 发电机处的母线电压（若电池母线由发电机控制且电压不同于主母线，则为电池母线电压）
float32 bat_current_setpoint       # [A] 电池电流目标值。正值表示放电，负值表示充电。NaN：未提供该字段

uint32 runtime                     # [s] 发电机自重启以来的运行时间。UINT32_MAX：未提供该字段。

int32 time_until_maintenance       # [s] 发电机下次需要维护的时间。负值表示维护已逾期。INT32_MAX：未提供该字段。

uint16 generator_speed             # [rpm] 发电机或交流发电机转速。UINT16_MAX：未提供该字段。

int16 rectifier_temperature        # [degC] 整流器或功率转换器温度。INT16_MAX：未提供该字段。
int16 generator_temperature        # [degC] 机械电机、燃料电池核心或发电机温度。INT16_MAX：未提供该字段。
```