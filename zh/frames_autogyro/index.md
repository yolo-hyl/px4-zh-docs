# 自转旋翼机框架

<LinkedBadge type="warning" text="Experimental" url="../airframes/#experimental-vehicles"/>

:::warning
对自转旋翼机框架的支持为[实验性](../airframes/index.md#experimental-vehicles)。
欢迎维护者志愿者通过[贡献](../contribute/index.md)新功能、新框架配置或其他改进！
:::

[自转旋翼机](https://en.wikipedia.org/wiki/Autogyro)是一种[旋翼机](https://en.wikipedia.org/wiki/Rotorcraft)。
与其他设计相比，具有以下优势：

- 能够仅使用非常短的跑道起降（相比固定翼机体）。
- 对天气条件（尤其是阵风）具有高抗性。
- 拥有非动力旋翼，使其能够以自转模式运行（也是直升机的应急模式之一）。
  因此在发生故障时无需主动改变飞行模式（不需要降落伞或其他主动工作设备）。
  其飞行因此在任何时候都具有固有的稳定性。
- 起降时不会产生导致扬尘的[下洗气流](https://en.wikipedia.org/wiki/Downwash)。
- [低升阻比](https://en.wikipedia.org/wiki/Lift-to-drag_ratio)可通过构造参数进行调整。
  这一特性非常有用，因为无人自转旋翼机在发生故障时无法飞行很远距离（与常规飞机不同），但飞行仍然是安全的，机体也不会坠落（与多旋翼或直升机典型情况不同）。

## 支持的框架

PX4支持多种自转旋翼机机身。
支持的配置集可参考[机身参考 > 自转旋翼机](../airframes/airframe_reference.md#autogyro)。

### DIY框架

本节包含若干自转旋翼机框架的组装和配置日志/指南。

- [ThunderFly Auto-G2 (Holybro pix32)](../frames_autogyro/thunderfly_auto_g2.md) - 改装自转旋翼机遥控模型

### 预装PX4的完整框架

本节列出完全组装且随时可飞（RTF）的机体，已预装PX4。

- [ThunderFly TF-G2](https://docs.thunderfly.cz/instruments/TF-G2) - 无人自转旋翼机开发套件
- [ThunderFly TF-G250](https://docs.thunderfly.cz/instruments/TF-G250) - 大气探测气象自转旋翼机