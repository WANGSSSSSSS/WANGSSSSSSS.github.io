---
title: gumbel-sfmx Trick
date: 2021-11-28 16:15:47
categories:
- ml
tags:
- ml
cover:
---

## 关于一个argmax的求导技巧

argmax是一个不可导的函数，有时候还必须对这个部分求导数，比如一个例子就是，**dVAE**, 根据encoder得到的分布函数，在codebook采样来得到新的特征，这个采样一般就是argmax，然后用新的特征干一些事情，这个时候，梯度只能传递到codebook,  而不能传递到encoder. 目前有两个解决办法：

* detach [[VQ-GAN]]() [[VQ-VAE]]()，我不知道起什么名字好
  $$
  \mathcal Z_d = en(\mathcal X) + (\ E[en(\mathcal X)] - en(\mathcal X) \ ).detach()
  $$

* softmax 采样

我主要想，总结一下第二种方法, 来源于文章：

THE CONCRETE DISTRIBUTION: A CONTINUOUS RELAXATION OF DISCRETE RANDOM VARIABLES

CATEGORICAL REPARAMETERIZATION  WITH GUMBEL-SOFTMAX

### Gumbel distribution

#### PDF

$$
\ln f(x) = -x - e^{-x} \\
 f(x) =  \exp(-(x+e^{-x}))
$$



#### CDF

$$
\ln F(x; \mu , \beta) = -e^{-(x-\mu)\over \beta} \\
F(x;0,0) = \exp(e^{-x})
$$

#### Property

$$
E(\mathcal X ) = \mu - \beta \ln(\ln2) 
$$



