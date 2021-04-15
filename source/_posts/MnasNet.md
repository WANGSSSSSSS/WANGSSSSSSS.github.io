---
title: MnasNet
date: 2021-04-14 22:00:52
categories:
- cv
- nas
tags:
- cv
- nas
cover: image-20210414220146133.png
---

#### MnasNet: Platform-Aware Neural Architecture Search for Mobile

![image-20210414220146133](MnasNet/image-20210414220146133.png)

首先确定优化函数以及约束：

准确度在Trainer训练过程中就可以得到，latency这是在手机上直接测试，而不是FLOPs指标

![image-20210415150214518](MnasNet/image-20210415150214518.png)

转化为软约束问题：

![image-20210415150313661](MnasNet/image-20210415150313661.png)

<img src="MnasNet/image-20210415150325951.png" alt="image-20210415150325951" />

这样设置可以对参数调节然后达到软约束和硬约束的变化

<img src="MnasNet/image-20210415150836700.png" alt="image-20210415150836700" style="zoom:67%;" />

使用强化学习最大化reward在搜索空间上搜索：

![image-20210415150405444](MnasNet/image-20210415150405444.png)

结果：

![image-20210415150431545](MnasNet/image-20210415150431545.png)

得到的最优解：

![image-20210415150505166](MnasNet/image-20210415150505166.png)

**这篇文章只放出了结果没有放出搜索过程代码，真狗**