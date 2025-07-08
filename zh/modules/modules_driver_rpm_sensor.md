# 模块参考：转速传感器（驱动）

## pcf8583

源码地址: [drivers/rpm/pcf8583](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/rpm/pcf8583)

### 用法 {#pcf8583_usage}

```
pcf8583 <command> [arguments...]
 命令:
   start
     [-I]        使用内部 I2C 总线
     [-X]        使用外部 I2C 总线
     [-b <val>]  板级专用总线（默认=所有）（外部 SPI：第 n 条总线
                 （默认=1））
     [-f <val>]  总线频率（单位：kHz）
     [-q]        静默启动（未发现设备时不提示）
     [-a <val>]  I2C 地址
                 默认：80
     [-k]        初始化（探测）失败时持续重试

   stop

   status        打印状态信息
```