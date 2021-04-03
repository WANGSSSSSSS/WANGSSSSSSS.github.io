---
title: orbslam2
date: 2021-04-02 10:32:33
categories:
tags:
cover: image-20210401174702609.png
---

# Local Mapping 线程

localMapping 线程对局部地图进行维护，对于tracking线程来说，会竭尽全力的插入关键帧，而LocalMapping 线程就是对这些关键帧进行审核，对于符合条件的关键帧进行插入，同时根据这个关键帧观察到的地图点