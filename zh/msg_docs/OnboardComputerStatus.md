# OnboardComputerStatus (UORB消息)

ONBOARD_COMPUTER_STATUS消息数据

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/OnboardComputerStatus.msg)

```c
# ONBOARD_COMPUTER_STATUS消息数据
uint64 timestamp # [us] 系统启动后经过的时间（微秒）
uint32 uptime	 # [ms] 机载计算机启动后的运行时间（毫秒）

uint8 type	 # 机载计算机类型 0: 任务计算机主用，1: 任务计算机备用1，2: 任务计算机备用2，3: 计算节点，4-5: 计算备用，6-9: 载荷计算机。

uint8[8] cpu_cores # 组件中CPU核心的使用率（百分比）
uint8[10] cpu_combined # 最近10个100毫秒片段的CPU总使用率
uint8[4] gpu_cores # 组件中GPU核心的使用率（百分比）
uint8[10] gpu_combined # 最近10个100毫秒片段的GPU总使用率
int8 temperature_board # [degC] 板卡温度
int8[8] temperature_core # [degC] CPU核心温度
int16[4] fan_speed # [rpm] 风扇转速
uint32 ram_usage # [MB] 组件系统中已使用的RAM数量
uint32 ram_total # [MB] 组件系统中总的RAM数量
uint32[4] storage_type # 存储类型：0: HDD，1: SSD，2: EMMC，3: SD卡（不可拆卸），4: SD卡（可拆卸）
uint32[4] storage_usage # [MB] 组件系统中已使用的存储空间
uint32[4] storage_total # [MB] 组件系统中总的存储空间
uint32[6] link_type # [Kb/s] 链路类型：0-9: UART，10-19: 有线网络，20-29: Wifi，30-39: 点对点专有，40-49: Mesh专有
uint32[6] link_tx_rate # [Kb/s] 组件系统的出站网络流量
uint32[6] link_rx_rate # [Kb/s] 组件系统的入站网络流量
uint32[6] link_tx_max # [Kb/s] 组件系统的出站网络容量
uint32[6] link_rx_max # [Kb/s] 组件系统的入站网络容量

```