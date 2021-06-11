---

title: openmp
date: 2021-05-08 12:34:55
categories:
- os
- openmp
tags:
- os
- parallel
- openmp
cover: image-20210508130211335.png
---

## 并行计算

利用openmp实现并行计算，了解openmp的编程模型以及并行计算的特点

openmp 适用于下面两种编程模型（统一内存访问和多处理机）：

![image-20210508130211335](openmp/image-20210508130211335.png)

在高性能计算中，通常mpi和openmp混合使用，mpi负责多处理机的主机层面的并行交互

![image-20210508130419024](openmp/image-20210508130419024.png)

openmp的编程模型基于线程实现并行，通过fork和join来控制线程，openmp在内存方面共享内存，



### 矩阵计算

串行计算矩阵的运算，以及利用openmp来进行并行编程

#### 矩阵加法

串行矩阵加法：

<img src="openmp/image-20210508125758522.png" alt="image-20210508125758522" style="zoom: 67%;" />

并行矩阵加法：

<img src="openmp/image-20210508125856054.png" alt="image-20210508125856054" style="zoom:67%;" />

性能对比：

* 正常串行加法：

  <img src="openmp/image-20210508124255793.png" style="zoom:67%;" />

* 一个外层循环

  <img src="openmp/image-20210508125549340.png" alt="image-20210508125549340" style="zoom:67%;" />

  调节环境变量，设置并行线程数为8，发现速度还能提升：

  <img src="openmp/image-20210508131108439.png" alt="image-20210508131108439" style="zoom: 67%;" />

  <img src="openmp/image-20210508131051368.png" alt="image-20210508131051368" style="zoom:67%;" />

#### 矩阵减法

串行矩阵减法：

<img src="openmp/image-20210508131156588.png" alt="image-20210508131156588" style="zoom:67%;" />

并行矩阵减法：

<img src="openmp/image-20210508131222581.png" alt="image-20210508131222581" style="zoom:67%;" />

#### 矩阵乘法

串行矩阵乘法：

<img src="openmp/image-20210508131246613.png" alt="image-20210508131246613" style="zoom:67%;" />

并行矩阵乘法：

<img src="openmp/image-20210508131328797.png" alt="image-20210508131328797" style="zoom:67%;" />

运行效率：性能提升了5倍

![image-20210508132503540](openmp/image-20210508132503540.png)

#### 矩阵点乘

串行矩阵点乘：

<img src="openmp/image-20210508132536350.png" alt="image-20210508132536350" style="zoom: 50%;" />

并行矩阵点乘：

<img src="openmp/image-20210508132551073.png" alt="image-20210508132551073" style="zoom:67%;" />

#### 矩阵除法



### 排序算法

#### 插入排序

<img src="openmp/image-20210508132909825.png" alt="image-20210508132909825" style="zoom:67%;" />

排序结果：

<img src="openmp/image-20210508133347132.png" alt="image-20210508133347132" style="zoom:67%;" />

采用omp并行之后：（排序结果出错了，想想逻辑的确不对，这块不能并行）

<img src="openmp/image-20210508133808537.png" alt="image-20210508133808537" style="zoom: 50%;" />

#### 快速排序

<img src="openmp/image-20210508132940108.png" alt="image-20210508132940108" style="zoom:67%;" />

排序结果：

<img src="openmp/image-20210508133428535.png" alt="image-20210508133428535" style="zoom:67%;" />

并行化：

<img src="openmp/image-20210508141237206.png" alt="image-20210508141237206" style="zoom:67%;" />

并行化结果：

<img src="openmp/image-20210508134950972.png" alt="image-20210508134950972" style="zoom:50%;" />

#### 归并排序

<img src="openmp/image-20210508133008818.png" alt="image-20210508133008818" style="zoom:67%;" />

排序结果：

<img src="openmp/image-20210508133318900.png" alt="image-20210508133318900" style="zoom:67%;" />

并行化：

<img src="openmp/image-20210508140122458.png" alt="image-20210508140122458" style="zoom: 67%;" />

并行化结果：

<img src="openmp/image-20210508140235643.png" alt="image-20210508140235643" style="zoom: 50%;" />

