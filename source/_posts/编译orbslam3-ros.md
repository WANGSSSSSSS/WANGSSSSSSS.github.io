---
title: 编译orbslam3+ros
date: 2021-03-30 11:28:20
categories:
- environment setup
tags:
- slam
- opencv
- ros
cover: /images/image-20210330113709306.png
---

### Orb slam3 + ros

记录一下编译过程中碰到的问题，一些问题，我真是想也想不明白

- [x] **orb slam3**为什么还在用opencv3，而ros已经是opencv4.2了
- [x] 为什么orb slam代码规范这么差，多线程编译的时候，报错都被冲到不知道哪里去了。

第一个问题：

##### cv::IMREAD_UNCHANGED

第二个问题：

##### std::map 使用不规范（const* ，*const）

好了就这样吧，我看到一些文件里，代码已经快10000行了，我哭了

我能看完吗？天呐！

![image-20210330113709306](/images/image-20210330113709306.png)