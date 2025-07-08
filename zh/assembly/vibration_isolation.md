# 隔振

本文档说明如何判断振动水平是否过高，并列出了一些改善振动特性的简单措施。

## 概述

内置加速度计或陀螺仪的飞控板对振动敏感。高振动水平可能导致一系列问题，包括降低飞行效率/性能、缩短飞行时间以及增加机体磨损。极端情况下，振动可能引发传感器截断/故障，进而导致估计失败和失控飞行。

设计良好的机体在飞控安装位置会减弱/降低特定结构共振的振幅。为将振动降低到敏感组件可承受的水平，可能需要进一步的隔离措施（例如某些飞控必须使用抗振泡沫/支架安装到机体上，而其他飞控则具有内部隔离机制）。

## 振动分析

[使用Flight Review进行日志分析 > 振动](../log/flight_review.md#vibration) 说明如何通过日志确认振动是否是飞行问题的可能原因。

## 基本振动修复措施

以下是一些可能降低振动的简单措施：

- 确保机体上所有部件牢固安装（起落架、GPS天线等）。
- 使用平衡的螺旋桨。
- 确保使用高质量的螺旋桨、电机、电调和机体部件。
  每个部件都可能产生显著影响。
- 使用隔振方法安装飞控。
  许多飞控配有_安装泡沫_，可用于此目的，而其他飞控则具有内置的隔振机制。
- 作为_最后_的手段，调整[软件滤波器](../config_mc/filter_tuning.md)。
  相比软件中过滤振动，更应优先从源头减少振动。

## 参考资料

以下参考资料可能对您有帮助：

- [An Introduction to Shock & Vibration Response Spectra, Tom Irvine](http://www.vibrationdata.com/tutorials2/srs_intr.pdf)（免费论文）
- [Structural Dynamics and Vibration in Practice - An Engineering Handbook, Douglas Thorby](https://books.google.ch/books?id=PwzDuWDc8AgC&printsec=frontcover)（预览）