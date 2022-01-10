---
title: Practical Object Detection with Scale-Sensitive Network
date: 2022-01-06 15:38:35
categories:
- cv
tags:
- cv
cover: image-20220106163709441.png
---

**Practical Object Detection with Scale-Sensitive Network**

object detection is one of the most important and fundamental question in computer vision area, while the dynamic range of object boxes hinders the forwarding of accuracy. To alleviate this issue, a lot of receptive field algorithm are proposed, the most famous idea is to introduce the dilation into conv operation, but the static configuration of dilation is expert-knowledge needed and is not optimal compared to end2end learning architecture, thus dynamic operation via interpolation have emerged  , like DCN(deformable conv) STN, in order to embrace the dynamic property. But the interpolation is not compatible with TVM or hardware accelerator, thus is not enough for realtime application.

![image-20220106163709441](pod/image-20220106163709441.png)

### Architecture

#### Global Scale Learner

as demonstrated  above, this module employs a linear layer to predict an appropriate dilation factor.

#### Scale Decomposition

![image-20220106163910341](pod/image-20220106163910341.png)

#### weight transfer

<center class="half">
    <img src="pod/image-20220106163926333.png" alt="image-20220106163926333" style="zoom: 67%;" />
    <img src="pod/image-20220106163941628.png" alt="image-20220106163941628" style="zoom:67%;" />
</center>







