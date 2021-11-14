---
title: OVI-segment
date: 2021-11-14 16:13:19
categories:
 - cv
tags:
 - cv
 - video
cover: image-20211114162111978.png
---

### Occluded Video Instance Segmentation

[[PDF](https://arxiv.org/pdf/2102.01558v4.pdf)] [[CODE](https://github.com/qjy981010/CMaskTrack-RCNN)] 

### Motivation

While human vision system can understand those occluded instance by contextual reasoning and association, can video understanding system alleviate the difficulty of occlusion condition instance understanding?

![image-20211114162604221](OVI-segment/image-20211114162604221.png)

### DataSet

* Youtube-VIS
* OVIS (**proposed**)

![image-20211114161911648](OVI-segment/image-20211114161911648.png)

### Ideology

use reference frame to calibrate the occluded information. 

![image-20211114162111978](OVI-segment/image-20211114162111978.png)

**Maybe optical flow estimation is  crucial to calibrate ref frame to query frame ?**

### Experiment

![image-20211114162313784](OVI-segment/image-20211114162313784.png)

