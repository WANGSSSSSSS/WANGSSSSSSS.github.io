---
title: llvm_ir
date: 2021-05-15 19:13:58
categories:
- compiler
tags:
- compiler
cover:
---

## IR 设计

### 上层指令

上层IR假设有无穷多的寄存器，因此每个指令结果可以直接产生一个结果寄存器，这个结果寄存器和这个指令基本绑定，用这个结果运算等价于用这个指令运算，所以用指令地址表示寄存器号，上层IR指令包含下面这些类型：

<img src="llvm-ir/image-20210522212409706.png" alt="image-20210522212409706" style="zoom:67%;" />

* 分配指令

  当前分配只包括寄存器，我们假设寄存器多的用不完，LLVM中将变量先分配在内存中，然后用mem2reg完成将部分变量提取到寄存器中，然后进行SSA处理，我们这里采取相反的操作方式，一直保持变量在寄存器中，寄存器分配的时候用图着色方法分配，没有分配到的滚到内存中去。

  <img src="llvm-ir/image-20210522212432930.png" alt="image-20210522212432930" style="zoom:67%;" />

* 一元运算指令

  <img src="llvm-ir/image-20210522212928689.png" alt="image-20210522212928689" style="zoom:67%;" />

* 二元运算指令

  <img src="llvm-ir/image-20210522212946701.png" alt="image-20210522212946701" style="zoom:67%;" />

* 函数返回

  

  <img src="llvm-ir/image-20210522212912232.png" alt="image-20210522212912232" style="zoom:67%;" />

* 函数调用

  函数调用的包含实参和指向函数定义的指针

  <img src="llvm-ir/image-20210522213007951.png" alt="image-20210522213007951" style="zoom:67%;" />

* Break

  <img src="llvm-ir/image-20210522212852070.png" alt="image-20210522212852070" style="zoom:67%;" />

* Continue

  <img src="llvm-ir/image-20210522212840444.png" alt="image-20210522212840444" style="zoom:67%;" />

* 赋值语句

  <img src="llvm-ir/image-20210522212802216.png" alt="image-20210522212802216" style="zoom:67%;" />

* 比较指令

  <img src="llvm-ir/image-20210522212827828.png" alt="image-20210522212827828" style="zoom:67%;" />

### 基本块

每个块有自己的符号表，同时每个块可以访问父亲的符号表，查找的时候自底向上查找变量定义

<img src="llvm-ir/image-20210522213606981.png" alt="image-20210522213606981" style="zoom:67%;" />

### 符号表

包含函数符号表，变量符号表， 常量符号表？（常量传播用的上？）

<img src="llvm-ir/image-20210522213450698.png" alt="image-20210522213450698" style="zoom:67%;" />

<img src="llvm-ir/image-20210522213500835.png" alt="image-20210522213500835" style="zoom:67%;" />

