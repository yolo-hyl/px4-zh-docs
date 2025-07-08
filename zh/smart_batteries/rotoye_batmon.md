# Rotoye Batmon

[Rotoye Batmon](https://rotoye.com/batmon/) 是一套可为现成锂离子电池和LiPo电池添加智能电池功能的套件。
该产品可作为独立单元购买，也可作为工厂组装的智能电池组件。

::: info
撰写本文时，您只能通过[构建PX4的自定义分支](#build-px4-firmware)来使用Batmon。
代码行中的支持正在等待[PR审批](https://github.com/PX4/PX4-Autopilot/pull/16723)。
:::

![Rotoye Batmon 板](../../assets/hardware/smart_batteries/rotoye_batmon/smart-battery-rotoye.jpg)

![预组装的Rotoye智能电池](../../assets/hardware/smart_batteries/rotoye_batmon/smart-battery-rotoye-pack.jpg)

## 购买渠道

[Rotoye Store](https://rotoye.com/batmon/): Batmon套件、定制智能电池及配件

## 接线/连接

Rotoye Batmon系统使用带有I2C引脚的XT-90电池连接器，以及一个光隔离板来传输数据。

![板连接](../../assets/hardware/smart_batteries/rotoye_batmon/smart-battery-rotoye-connection.png)

更多细节请参考[此处](https://github.com/rotoye/batmon_reader)

## 软件设置

### 构建PX4固件

1. 克隆或下载[Rotoye的PX4分支:](https://github.com/rotoye/PX4-Autopilot/tree/batmon_4.03)
   ```sh
   git clone https://github.com/rotoye/PX4-Autopilot.git
   cd PX4-Autopilot
   ```
1. 切换到 *batmon_4.03* 分支
   ```sh
   git fetch origin batmon_4.03
   git checkout batmon_4.03
   ```
1. [构建并上传固件](../dev_setup/building_px4.md)到您的目标板

### 配置参数

在 *QGroundControl* 中：
1. 设置以下[参数](../advanced_config/parameters.md)：
   - `BATx_SOURCE` 设置为 `External`，
   - `SENS_EN_BAT` 设置为 `true`， 
   - `BAT_SMBUS_MODEL` 设置为 `3:Rotoye`
1. 打开[MAVLink控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)
1. 在控制台中启动[batt_smbus驱动](../modules/modules_driver.md)。
   例如，要在同一总线上运行两个BatMons：
   ```sh 
   batt_smbus start -X -b 1 -a 11 # 外部总线1，地址0x0b  
   batt_smbus start -X -b 1 -a 12 # 外部总线1，地址0x0c
   ```

## 更多信息

[快速入门指南](https://rotoye.com/batmon-tutorial/) (Rotoye)