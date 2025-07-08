# 二进制大小分析

`bloaty_compare_master` 构建目标可帮助您更好地理解代码更改对代码大小的影响。
当使用该目标时，工具链会下载特定固件的最新成功构建的 master 版本，并将其与本地构建进行比较（使用 [bloaty](https://github.com/google/bloaty) 二进制大小分析工具）。

:::tip
这有助于分析导致 `px4_fmu-v2_default` 触发 1MB flash 限制的更改。
:::

_Bloaty_ 必须位于您的路径中，并在 _cmake_ 配置时被找到。
PX4 [docker 文件](https://github.com/PX4/containers/blob/master/docker/Dockerfile_nuttx-bionic) 按如下方式安装 _bloaty_：

```sh
git clone --recursive https://github.com/google/bloaty.git /tmp/bloaty \
	&& cd /tmp/bloaty && cmake -GNinja . && ninja bloaty && cp bloaty /usr/local/bin/ \
	&& rm -rf /tmp/*
```

以下示例展示如何查看从 `px4_fmu-v2_default` 中移除 _mpu9250_ 驱动的影响。
首先在本地建立一个不包含该驱动的构建：

```sh
 % git diff
diff --git a/boards/px4/fmu-v2/default.px4board b/boards/px4/fmu-v2/default.px4board
index 40d7778..2ce7972 100644
--- a/boards/px4/fmu-v2/default.px4board
+++ b/boards/px4/fmu-v2/default.px4board
@@ -36,7 +36,7 @@
-               CONFIG_DRIVERS_IMU_INVENSENSE_MPU9250=y
+               CONFIG_DRIVERS_IMU_INVENSENSE_MPU9250=n
```

然后使用 make 目标进行比较（此处指定为 `px4_fmu-v2_default`）：

```sh
% make px4_fmu-v2_default bloaty_compare_master
...
...
...
     VM SIZE                                                                                        FILE SIZE
 --------------                                                                                  --------------
  [DEL]     -52 MPU9250::check_null_data(unsigned int*, unsigned char)                               -52  [DEL]
  [DEL]     -52 MPU9250::test_error()                                                                -52  [DEL]
  [DEL]     -52 MPU9250_gyro::MPU9250_gyro(MPU9250*, char const*)                                    -52  [DEL]
  [DEL]     -56 mpu9250::info(MPU9250_BUS)                                                           -56  [DEL]
  [DEL]     -56 mpu9250::regdump(MPU9250_BUS)                                                        -56  [DEL]
...                                        -336  [DEL]
  [DEL]    -344 MPU9250_mag::_measure(ak8963_regs)                                                  -344  [DEL]
  [DEL]    -684 MPU9250::MPU9250(device::Device*, device::Device*, char const*, char const*, cha    -684  [DEL]
  [DEL]    -684 MPU9250::init()                                                                     -684  [DEL]
  [DEL]   -1000 MPU9250::measure()                                                                 -1000  [DEL]
 -41.3%   -1011 [43 Others]                                                                        -1011 -41.3%
  -1.0% -1.05Ki [Unmapped]                                                                       +24.2Ki  +0.2%
  -1.0% -10.3Ki TOTAL                                                                            +14.9Ki  +0.1%
```

这显示从 `px4_fmu-v2_default` 中移除 _mpu9250_ 将节省 10.3 kB 的 flash 空间。
它还显示了 _mpu9250_ 驱动中各个部分的大小。