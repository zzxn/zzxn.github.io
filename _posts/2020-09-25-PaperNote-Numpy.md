---
title: 【论文笔记】Array programming with NumPy
tags: 论文 工程 nature 2020
key: bc355fb14a1b9a7838fe9b2566b52708
---

(nature 2020) Array programming with NumPy

## 概述

这篇论文多维数组计算库Numpy的核心概念及其Python科学计算生态系统中的位置。
Numpy诞生于2005年，建立在它的两位前辈Numeric和Numarray的基础之上，结合了两个库的优点，使得社区重新统一。
到2020年，Numpy已经成为几乎每个Python科学/数值计算库的基础组成部分，包括SciPy、Matplotlib、pandas、scikit-learn、scikit-image等，并且因为Numpy数组内禀的简单性，它已经成了Python事实上的数组交换格式。
现在，为了适应新的计算硬件（GPU/分布式系统等）等特殊需求，各种不同的多维数组实现正在不断出现，但是它们都可以使用Numpy的大部分API进行操作，或者至少与Numpy的API非常相似以减少学习成本。Numpy已经成为了Python数组API的参考标准和中心协调机制。

## Numpy核心概念

![image-20200925113457749](/assets/images/mdref/image-20200925113457749.png)

### 数组（ndarray）数据结构

> 这里的数组指的是多维数组或者张量（Tensor）。

* data（数据）：是一个指向内存的指针，所有数据保存在内存中
* dtype（数据类型）：float/str/object...
    * （不同精度的）实数/复数
    * 字符串
    * 时间戳
    * 指向Python对象的指针
* shape（形状）：例如彩色视频数据的shape是`(T, W, H, 3)`
* strides（步长）：描述各个轴的数据byte数量间隔，例如一个4×3的矩阵，每个元素是8 bytes，那么步长就是`(24, 8)`

### 操作数组的方式

* indexing：Python的index和切片
* operators：`+` `-` `*` `/` `@`等操作符
* array-aware functions （functions able to manipulate array）
    * 包括：create/reshape/sort/append/线性代数计算...
    * 使用Intel MKL等进行加速

## Numpy在Python科学计算生态系统中的地位

![image-20200925113525572](/assets/images/mdref/image-20200925113525572.png)

如Fig.2所示，Numpy处于最基础的位置，并且Numpy通过其数组互操作协议使得它可以与新产生的数组计算库（PyTorch等）保持很高的互操作性。

## 总结

> NumPy is no longer merely the foundational array library underlying the scientific Python ecosystem, but it has become the standard API for tensor computation and a central coordinating mechanism between array types and technologies in Python.
