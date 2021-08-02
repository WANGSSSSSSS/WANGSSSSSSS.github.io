---
title: SimCLR
date: 2021-08-02 16:39:23
categories:
- cv
tags:
- cv
cover: image-20210802165356590.png
---

### A Simple Framework for Contrastive Learning of Visual Representations

![image-20210802173548787](SimCLR/image-20210802173548787.png)

## Framework

<img src="SimCLR/image-20210802164113743.png" alt="image-20210802164113743" style="zoom:67%;" /><img src="SimCLR/image-20210802164539870.png" alt="image-20210802164539870" style="zoom:67%;" />

## Methods

### data augmentation

**asymmetric** : using data augmentation to **one branch**, leave the other branch as identity

* crop 
* resize
* color distort

![image-20210802165356590](SimCLR/image-20210802165356590.png)

![image-20210802165230487](SimCLR/image-20210802165230487.png)

### object function

![image-20210802170139589](SimCLR/image-20210802170139589.png)

![image-20210802170639897](SimCLR/image-20210802170639897.png)

### projective head

![image-20210802170612685](SimCLR/image-20210802170612685.png)

## hypothesis

* the sort path of learning difference by color distribution, to eliminate this problem:

  **using color distortion to make distribution of crop different**

  <img src="SimCLR/image-20210802165158284.png" alt="image-20210802165158284" style="zoom:67%;" />

* the bigger batch make the gap between self-supervise and supervise disappear

* the more training epoch make the difference result from batch smaller  

* nonlinear head only learning the similirity of two branch, thus make the backbone gain more information.

![image-20210802170431850](SimCLR/image-20210802170431850.png)

* **semi-hard negative mining **

  weigh the negatives by their relative hardness.

