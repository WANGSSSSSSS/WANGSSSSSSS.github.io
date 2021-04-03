---
title: orbslam3
date: 2021-04-02 14:41:49
categories:
- cv
- slam
tags:
- cv
- slam
cover:
---

# Loop Closing 线程

回环检测线程用来估计运动中的累计误差，来校准全图，这部分也是区别于视觉里程计的部分。

运行结构图如下：

![image-20210403111221698](orbslam3/image-20210403111221698.png)

## 回环检测

