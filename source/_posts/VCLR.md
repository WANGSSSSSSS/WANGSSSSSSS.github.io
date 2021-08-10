---
title: VCLR
date: 2021-08-08 10:00:59
categories:
- cv
tags:
- cv
cover: image-20210808100151804.png
---

#### Video Contrastive Learning with Global Context [[NCE-LOSS](../../02/SimCLR)]

this article is mainly contribute the following:

* global context sampling method for video level contrastive learning
* a video consensus  constrain for self supervised learning



![image-20210808100151804](VCLR/image-20210808100151804.png)

## model structure

 ![image-20210808100401105](VCLR/image-20210808100401105.png)

### Inter-Loss

the inter loss uses augmented samples $P_1^+$​ from anchor frame $Q_1^a$ ​and samples $\{P_2, P_3\}$​​ from the same video to formulate  positive 

​                                       <img src="VCLR/image-20210808101031383.png" alt="image-20210808101031383" style="zoom:67%;" /> 

### Intra-Loss

when using the intra loss, the samples from the same video will also be discrimination as negative.

<img src="VCLR/image-20210808101236873.png" alt="image-20210808101236873" style="zoom:67%;" />

### segment-Loss

* divide the whole video as K segments $\{ S_1, S_2, ... S_k\}$

* anchor tuple $t^a = \{ v_1, v_2, .. v_k\}, v_i \in S_i$

* the positive sample is from the same video segments, but consist of different frames

  $t^+ = \{ v_1^+, v_2^+, .. v_k^+\}, v_i^+ \in S_i$

![image-20210808101442478](VCLR/image-20210808101442478.png)

### order-Loss

guarantee the sampled frames follow the correct order, which is a property of video consensus, this constrain can be regard as a important regularization rule

<img src="VCLR/image-20210808101947943.png" alt="image-20210808101947943" style="zoom:67%;" />

<img src="VCLR/image-20210808102002835.png" alt="image-20210808102002835" style="zoom:67%;" />

<img src="VCLR/image-20210808102017754.png" alt="image-20210808102017754" style="zoom:67%;" />

