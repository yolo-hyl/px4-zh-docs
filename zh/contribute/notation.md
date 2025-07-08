# 术语

本指南的文本和图表中使用了以下术语、符号和装饰器。

## 符号说明

- 粗体变量表示向量或矩阵，非粗体变量表示标量。
- 每个变量的默认坐标系是局部坐标系：$\ell{}$。
  右上标[superscripts](#superscripts)表示坐标系。
  如果没有右上标，则默认使用局部坐标系 $\ell{}$。
  旋转矩阵是一个例外，其中右下标表示当前坐标系，右上标表示目标坐标系。
- 变量和下标可以使用相同的字母，但它们的含义总是不同。

## 缩写

| 缩写     | 全称                                                                                                                                                                      |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AOA      | 迎角。也称为_alpha_。                                                                                                                                                    |
| AOS      | 侧滑角。也称为_beta_。                                                                                                                                                   |
| FRD      | 坐标系，其中X轴指向机体前方，Y轴指向机体右侧，Z轴指向下方，遵循右手定则。                                                                                                  |
| FW       | 固定翼（飞机）。                                                                                                                                                         |
| MC       | 多旋翼。                                                                                                                                                                 |
| MPC 或 MCPC | 多旋翼位置控制器。MPC 也用于模型预测控制。                                                                                                                             |
| NED      | 坐标系，其中X轴指向真北，Y轴指向东，Z轴指向下，遵循右手定则。                                                                                                              |
| PID      | 具有比例、积分和微分作用的控制器。                                                                                                                                       |## 符号

| 变量                          | 描述                                                                                                                                                     |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $x,y,z$                         | 沿坐标轴x、y和z的平移。                                                                                                       |
| $\boldsymbol{\mathrm{r}}$       | 位置向量: $\boldsymbol{\mathrm{r}} = [x \quad y \quad z]^{T}$                                                                                            |
| $\boldsymbol{\mathrm{v}}$       | 速度向量: $\boldsymbol{\mathrm{v}} = \boldsymbol{\mathrm{\dot{r}}}$                                                                                      |
| $\boldsymbol{\mathrm{a}}$       | 加速度向量: $\boldsymbol{\mathrm{a}} = \boldsymbol{\mathrm{\dot{v}}} = \boldsymbol{\mathrm{\ddot{r}}}$                                                 |
| $\alpha$                        | 攻角（AOA）。                                                                                                                                          |
| $b$                             | 翼展（从翼尖到翼尖）。                                                                                                                                    |
| $S$                             | 翼面积。                                                                                                                                                      |
| $AR$                            | 展弦比: $AR = b^2/S$                                                                                                                                      |
| $\beta$                         | 侧滑角（AOS）。                                                                                                                                        |
| $c$                             | 翼弦长。                                                                                                                                              |
| $\delta$                        | 气动控制面偏转角。正向偏转产生负力矩。                                                              |
| $\phi,\theta,\psi$              | 欧拉角（滚转、俯仰和偏航）。                                                                                                            |
| $\Psi$                          | 姿态向量: $\Psi = [\phi \quad \theta \quad \psi]^T$                                                                                                      |
| $X,Y,Z$                         | 沿坐标轴x、y和z的力。                                                                                                                         |
| $\boldsymbol{\mathrm{F}}$       | 力向量: $\boldsymbol{\mathrm{F}}= [X \quad Y \quad Z]^T$                                                                                                  |
| $D$                             | 阻力。                                                                                                                                                     |
| $C$                             | 横向力。                                                                                                                                               |
| $L$                             | 升力。                                                                                                                                                     |
| $g$                             | 重力。                                                                                                                                                        |
| $l,m,n$                         | 绕坐标轴x、y和z的力矩。                                                                                                                       |
| $\boldsymbol{\mathrm{M}}$       | 力矩向量 $\boldsymbol{\mathrm{M}} = [l \quad m \quad n]^T$                                                                                                 |
| $M$                             | 马赫数。可忽略模型飞机的马赫数影响。                                                                                                               |
| $\boldsymbol{\mathrm{q}}$       | 四元数的向量部分。                                                                                                                                      |
| $\boldsymbol{\mathrm{\tilde{q}}}$ | 哈密顿姿态四元数（参见下方`1`）                                                                                                                 |
| $\boldsymbol{\mathrm{R}}_\ell^b$  | 旋转矩阵。将向量从$\ell{}$坐标系旋转到$b{}$坐标系 $\boldsymbol{\mathrm{v}}^b = \boldsymbol{\mathrm{R}}_\ell^b \boldsymbol{\mathrm{v}}^\ell$ |
| $\Lambda$                       | 前缘后掠角。                                                                                                                                       |
| $\lambda$                       | 翼梢比: $\lambda = c_{tip}/c_{root}$                                                                                                                       |
| $w$                             | 风速。                                                                                                                                                  |
| $p,q,r$                         | 绕机体坐标轴x、y和z的角速度。                                                                                                                       |
| $\boldsymbol{\omega}^b$         | 机体坐标系下的角速度向量: $\boldsymbol{\omega}^b = [p \quad q \quad r]^T$                                                                              |
| $\boldsymbol{\mathrm{x}}$       | 通用状态向量。                                                                                                                                           |

- `1` 哈密顿姿态四元数。$\boldsymbol{\mathrm{\tilde{q}}} = (q_0, q_1, q_2, q_3) = (q_0, \boldsymbol{\mathrm{q}})$。<br> $\boldsymbol{\mathrm{\tilde{q}}}{}$ 描述相对于局部坐标系$\ell{}$的姿态。当需要将机体坐标系向量转换为局部坐标系向量时，可使用以下运算：$\boldsymbol{\mathrm{\tilde{v}}}^\ell = \boldsymbol{\mathrm{\tilde{q}}} \, \boldsymbol{\mathrm{\tilde{v}}}^b \, \boldsymbol{\mathrm{\tilde{q}}}^*{}$（或使用$\boldsymbol{\mathrm{\tilde{q}}}^{-1}{}$代替$\boldsymbol{\mathrm{\tilde{q}}}^*{}$，若$\boldsymbol{\mathrm{\tilde{q}}}{}$非单位四元数）。$\boldsymbol{\mathrm{\tilde{v}}}{}$表示_四元数化_向量: $\boldsymbol{\mathrm{\tilde{v}}} = (0,\boldsymbol{\mathrm{v}})$

### 下标 / 索引

| 下标 / 索引 | 描述                                                      |
| -------------------- | ---------------------------------------------------------------- |
| $a$                  | 副翼。                                                         |
| $e$                  | 升降舵。                                                        |
| $r$                  | 方向舵。                                                          |
| $Aero$               | 气动的。                                                     |
| $T$                  | 推力。                                                    |
| $w$                  | 相对空速。                                               |
| $x,y,z$              | 向量沿坐标轴x、y和z的分量。            |
| $N,E,D$              | 向量沿北、东和下方向的分量。               |

<a id="superscripts"></a>

### 上标 / 索引

| 上标 / 索引 | 描述                                     |
| ---------------------- | ----------------------------------------------- |
| $\ell$                 | 局部坐标系。默认用于PX4相关变量。 |
| $b$                    | 机体坐标系。                                     |
| $w$                    | 风坐标系。                                     |## 装饰器

| 装饰器       | 描述          |
| ------------ | ------------- |
| $()^*$       | 共轭复数。     |
| $\dot{()}$   | 时间导数。     |
| $\hat{()}$   | 估计值。       |
| $\bar{()}$   | 均值。         |
| $()^{-1}$    | 矩阵逆。       |
| $()^T$       | 矩阵转置。     |
| $\tilde{()}$ | 四元数。       |