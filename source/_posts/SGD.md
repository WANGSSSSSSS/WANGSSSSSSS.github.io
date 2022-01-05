---
title: SGD
date: 2021-07-10 14:57:32
categories:
- ml
tags:
- ml
- optim
cover: image-20210710150612950.png
---

### SGD

![image-20210710150122211](SGD/image-20210710150122211.png)

### AdamW

![image-20210710150612950](SGD/image-20210710150612950.png)

### RMPprop

to solve some gradient oscillation occasion with only consider sign of gradient for mini-batch learning , where the error surface is a ellipse. typically speeking, a lot of gradients deviated from direction towards optimal point.

* moment
* only consider sign
* adjust learning rate according to gradient consistency

<img src="SGD/image-20220101170356329.png" alt="image-20220101170356329" style="zoom:67%;" />



