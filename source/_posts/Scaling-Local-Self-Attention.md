---
title: Scaling_Local_Self-Attention
date: 2021-03-29 15:29:36
categories:
- cv
- attention
tags:
- attention
cover: /images/image-20210329154023833.png
---

## Scaling Local Self-Attention

这篇文章我觉得比MSRA的那篇transformer的有趣多了，我觉得看论文不能盯着实验就不放手了，MSRA那篇对我来说，仿佛他们就是调参成功，用几个不适合的论点凑了一篇论文。

对于在MSRA的swin transformer的一些消融实验上的疑惑，这篇文章竟然都提到了，最有意义的是这篇我感觉是对CNN体系和attention体系两者联系的一个讨论，当然我的观点也有局限性，我只能看到当前的论文，对于我不知道的前人的思考，我容易当成是作者的想法。

### 论文观点

这篇文章用一些attention的操作，来替换卷积中出现的操作，思路上很合理，提到了local convolution，pooling一些不同的操作，对于卷积和attention建立了一个共同的模型

![image-20210329153810655](/images/image-20210329153810655.png)

$f(i,j,a,b)$对应的是参数，在cnn中就是卷积核，在attention中：

![image-20210329153940808](/images/image-20210329153940808.png)

#### 卷积操作

![image-20210329154023833](/images/image-20210329154023833.png)

作者想法应该是把imag都当成self，这样就是self-attention的运算，如果对于2*2的块来看，这块对应的应该是一个cross-attention，最重要的是，这个操作描述了卷积的局部特征，halo对应的就是偏移强度

注释：这里解决了我在MSRA的那篇工作里的疑惑，多个方向都做偏移，做多个强度的偏移，这样才是合理的，可以对比的，要不然研究是魔法吗？计算机是黑科技吗？

#### 池化（down sample）定义

![image-20210329154256986](/images/image-20210329154256986.png)

self-attention中的down sample定义的其实有点僵硬。

### 网络设计

用上面的思路，做一个attention的神经网络，就提上了日程。文章中网络名称叫做：**HaloNet**

![image-20210329155325548](/images/image-20210329155325548.png)

### 实验

resnet50：

![image-20210329155301868](/images/image-20210329155301868.png)

比effcientNet参数量少，精度还略高一点，不过我还是认为两者差不多说法应该合适一点

![image-20210329155049507](/images/image-20210329155049507.png)

上面两个实验**证明了attention代替CNN的可行性**

基本实验已经够了，谷歌不辞辛苦的又给我们看了，data augmentation的技巧还能涨点，这个实验是要说明什么呢？原文做了没提。

#### 推进实验

为什么把卷积换成attention就会涨点？所以我们要研究一下，哪一层换了就会涨点异常！

![image-20210329160037148](/images/image-20210329160037148.png)

其他就是打榜效果了：贴一下吧，我太喜欢这篇了

detection：

![image-20210329160232790](/images/image-20210329160232790.png)

###  总结

其实已经走到这一步了，解决non local的问题，直接引入一个dilate self-attention，扩大感受野，不是很舒服吗? 难不成你谷歌也是准备再水一篇？我觉得NAS技术现在也可以过来了，用Nas搜索attention的权值函数，是不是又是一个sota？