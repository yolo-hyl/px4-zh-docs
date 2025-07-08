# 在PX4上进行Hardfault调试

## 硬故障定义
Hardfault（硬故障）是当CPU尝试执行无效指令或访问无效内存地址时触发的异常。这通常表明存在严重的系统错误，需要通过调试工具进行排查。

## 视频教程
<video src="https://example.com/px4-hardfault-debugging.mp4" title="在PX4上进行Hardfault调试"></video>
<video src="https://example.com/px4-debugging-tips.mp4" title="PX4调试技巧"></video>

## NuttX堆栈调试
NuttX操作系统维护了两个堆栈：
- **中断堆栈**：用于处理硬件中断
- **任务堆栈**：用于线程执行

堆栈地址范围：
```assembly
R0: 20000f48 0a91ae0c 20020d00 20020d00 2001f578 080ca038 000182b8 20017cc0
R8: 2001ed20 2001f4e8 2001ed20 00000005 20020d20 20020ce8 0808439f 08087c4e
xPSR: 61000000 BASEPRI: 00000000 CONTROL: 00000000
EXC_RETURN: ffffffe9
```

## 调试步骤

### 加载二进制文件
要解码硬故障，请将完全相同的二进制文件加载到调试器中：
```bash
arm-none-eabi-gdb build/px4_fmu-v2_default/px4_fmu-v2_default.elf
```

### 分析执行路径
在GDB提示符下，从R8中的最后指令开始分析（以`0x080`开头的地址）：
```gdb
(gdb) info line *0x0808439f
Line 77 of "../src/modules/systemlib/mavlink_log.c" 
   starts at address 0x8084398 <mavlink_vasprintf+36>
   and ends at 0x80843a0 <mavlink_vasprintf+44>.
```

```gdb
(gdb) info line *0x08087c4e
Line 311 of "../src/modules/uORB/uORBDevices_nuttx.cpp"
   starts at address 0x8087c4e <uORB::DeviceNode::publish(orb_metadata const*, void*, void const*)+2>
   and ends at 0x8087c52 <uORB::DeviceNode::publish(orb_metadata const*, void*, void const*)+6>.
```

### 寄存器状态
关键寄存器状态解析：
```assembly
R0: 20000f48 0a91ae0c 20020d00 20020d00 2001f578 080ca038 000182b8 20017cc0
R8: 2001ed20 2001f4e8 2001ed20 00000005 20020d20 20020ce8 0808439f 08087c4e
xPSR: 61000000 BASEPRI: 00000000 CONTROL: 00000000
EXC_RETURN: ffffffe9
```

## 内存地址分析
堆栈回溯显示在`mavlink_log.c`中尝试发布数据时发生异常：
```assembly
20020f60: 2001f577 0809c577 2001ed20 2001f4d8 2001f498 0805e077 2001f568 20024540
20020f80: 00000000 00000000 00000000 0000260b 3d093a57 00000000 2001f540 2001f4f0
```

## 闪存地址映射
关键闪存地址解析：
```assembly
0x0808439f -> ../src/modules/systemlib/mavlink_log.c 第77行
0x08087c4e -> ../src/modules/uORB/uORBDevices_nuttx.cpp 第311行
```

通过GDB分析可定位具体代码位置，建议使用`disassemble`命令查看完整调用链。执行顺序从左到右，最后一步发生硬故障前的调用路径为：
`mavlink_vasprintf` -> `uORB::DeviceNode::publish`