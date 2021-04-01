---
title: Vision Transformers for Dense Prediction
date: 2021-03-30 17:37:12
categories:
- cv
tags:
- cv
- transformer
cover: /images/image-20210330174411785.png

---

##　Vision Transformers for Dense Prediction

这篇结构上挺清晰的，到目前为止，我还没看大名鼎鼎的ＶＩＴ结构，这篇看的时候让我对ｖｉｔ由很疑惑到变得感觉很不错，很自然，这篇就不从原文看了，我记录我自己的理解

### 网络结构

这篇是做深度估计的，以前参与过stereo的工作，对于深度估计还算不陌生，深度估计其实和显著性目标检测，分割遇到的问题都很相似，就是提取特征在全局与局部的平衡，网络层数不够深，提取到的语义信息不充分，网络层数深，必然会影响分辨率。

![architecture](/images/image-20210330174411785.png)



网络结构还是很清晰的，将图片embed之后就是Transformer，这部分构成了整体encode，后面的reassmable 模块和fusion模块就是decode，忽略Transformer的存在，这部分还是和UNet，FPN结构很像

**DPT** = **encoder{embed + Transformer}** + **decoder{reassmable + fusion}**

#### embed 模块：

第一种：直接将图片分块，就这么简单

第二种：使用CNN（resnet-50）提取图片特征，然后再接Transformer

无论那种都是分成patch之后，将每个patch变成一个token，这是transformer的基本单位，

token的数量为 $H*W/p^2 + 1$，多的那个是ＶＩＴ结构的要求，这个多出来的一般就是分类任务的向量

#### Transformer 模块：

正常的mutiple head self attention

#### reassmable 模块：

用来把token的向量排列，转换到图像的二维排列上，$H*W/p^2 + 1 \to (H/p, W/p)$

![assmable 模块](/images/image-20210330175556206.png)

##### 过程：

* **read**: 

  三种操作的效果对比：

  ![](/images/image-20210330180520239.png)

  * 直接去掉那个VIT结构中用来分类任务的全局语义向量

    ![](/images/image-20210330175921485.png)

  * 加法

    ​	![](/images/image-20210330180018178.png)

  * cat+投影

    ![](/images/image-20210330175941655.png)

    

* 重新排序，构成二维特征图

* resample，将特征图放大

#### Fusion 模块

![fusion module](/images/image-20210330180239274.png)

### 实验效果

* 深度估计：

![深度估计效果](/images/image-20210330180400674.png)

![深度估计精度对比](/images/image-20210330180732941.png)

* 分割：

![分割效果](/images/image-20210330180420247.png)