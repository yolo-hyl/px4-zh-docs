# NavigatorMissionItem（UORB消息）


[源文件](https://github.com/PX4/PX4-Autopilot/blob/main/msg/NavigatorMissionItem.msg)

```c
uint64 timestamp                 # 系统启动后经过的时间（微秒）

uint16 sequence_current          # 当前任务项的序列

uint16 nav_cmd

float32 latitude
float32 longitude

float32 time_inside              # 飞行器应在半径范围内停留的时间（秒）
float32 acceptance_radius        # 默认任务到达半径（米）
float32 loiter_radius            # 盘旋半径（米），0表示VTOL悬停，负值表示逆时针方向
float32 yaw                      # 以NED坐标系表示的偏航角（弧度）-PI..+PI，NAN表示不改变偏航角
float32 altitude                 # 海拔高度（米，AMSL）

uint8 frame                      # 任务坐标系
uint8 origin                     # 任务项来源（机载或MAVLink）

bool loiter_exit_xtrack          # 退出盘旋点的位置：0表示盘旋点中心，1表示退出位置
bool force_heading               # 需要到达指定航向
bool altitude_is_relative        # 高度是否相对起始点
bool autocontinue                # 是否自动继续下一个航点
bool vtol_back_transition        # 是否属于VTOL返航序列的一部分

```