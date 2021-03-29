---
title: Swin_transformer
date: 2021-03-29 11:12:07
categories:
- cv
- transformer
tags:
- cv
- transformer
cover: /images/image-20210329142709352.png
---

## Swin Transformer

这篇文章是微软亚洲研究院（MSRA）提出的基于transformer的文章，属于对transformer结构修改用来适应计算机视觉领域特征提取的文章，和resnetX，viT属于一类

### 论文观点：

#### 相关工作：

相关工作这块我不知道为啥要分为四个类型，我不是很区分的来后面三个

**CNN based backbone**:

基于卷积神经网络网络的backbone设计，结合着一些神奇的卷积层，比如dilateConv，PointConv， GroupConv，代表作为：VGG，AlexNet，ResNet等

**self-attention backbone**:

利用attention机制在NLP领域的成功，直接用attention机制替换一部分或者全部的卷积层，计算量比较大

**CNN & self-attention layer backbone**:

代表为：transformer结构的encoder-decoder

**Pure transformer backbone **:

代表为：ViT，DiT

### 网络结构：

![image-20210329142709352](/images/image-20210329142709352.png)

输入：color image = $[h,w,3]$

**patch patition**：将图片分为大小为$M*M$的多个小patch，这个patch相当于NLP中的token，就是后面处理的基本单位, 现在大小是$[h/M, h/M, M, M, 3]$

**linear-embedding:** 将每个patch内的特征向量拼接到一块,现在大小是$[h/M,w/M, M*M*3]$

**Swin transformer block**:特征提取的基本单位,包含[**window-MSA**，**MLP**，**MLP**，**GELU**，**Norm**]

**window-MSA**: 在小patch内作self-attention

**shift-window MSA:**

![image-20210329143747343](/images/image-20210329143747343.png)

对于原来的特征图，做一个向右下的$[M/2, M/2]$的偏移，这个偏移的作用是加强邻居patch的联系，然后出现了两个计算方案：（1）直接加padding，简单容易实现（2）用mask，计算量小 mask：灰色设为1,其他颜色的直接用0

**Shifted window in successive blocks**：

![image-20210329144511138](/images/image-20210329144511138.png)

### 实验效果：

#### ImageNet-1000

![image-20210329144617456](/images/image-20210329144617456.png)

### 消融实验

不是很想仔细看

### 总结：

我有很多疑问，他们代码没贴出来，我有什么办法，然后很符合顶会论文的个性，效果强悍的实验以及至少两个论点，不过他的论点很弱有没有：

- [ ] 为什么shift Window是向右下的，左上，单个方向效果呢？消融实验没有做
- [ ] attention的权值分配函数加了个bias，这个是因为window based attention在空间上有bias吗？

其余的，大幅度减少计算量的window MSA不是他们做的，只是个shift，感觉MSRA有点划水