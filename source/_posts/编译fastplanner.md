---
title: 编译fastplanner
date: 2021-04-04 09:59:13
categories:
- environment setup
tags:
- environment setup
cover: image-20210404095959149.png
---

![Fast planner](编译fastplanner/image-20210404095959149.png)

## 遇到的问题

* **pcl**点云库与planner产生冲突，c++版本问题

![image-20210404100139419](编译fastplanner/image-20210404100139419.png)

![image-20210404100200266](编译fastplanner/image-20210404100200266.png)

解决办法：

![image-20210404100235120](编译fastplanner/image-20210404100235120.png)

添加编译选项，按照c++14编译