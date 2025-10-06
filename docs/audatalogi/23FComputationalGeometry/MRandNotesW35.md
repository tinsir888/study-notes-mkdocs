---
title: 计算几何 W35 Introduction to Algorithm Engineering
author: tinsir888
date: 2023/8/29
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: d326008c
---

# 课程简介

计算几何：理论和实验，aka Computational Geometry: Theory and Experimentation

Week 35 - Week 49，共 14 周（除去秋假一周）

每周都有阅读材料，简单记录一下课前阅读笔记。



第一周（Week 35）内容：Introduction to Algorithm Engineering

# 阅读材料

## Sanders

title: Algorithm Engineering – An Attempt at a Definition

by Peter Sanders (Universität Karlsruhe)

Algorithm engineering: cycle of design, analysis, implementation, experimental evaluation.

algorithm engineering = experimental algorithmics??? too limited

design: not just care about asymptotic worst efficiency, but also have to look at the constant factors.

analysis: gap between theory and practice.

algorithm libraries: portable, easy to use

## NotToDo

title: How Not To Do It

by Ian P. Gent (University of Edinburgh), Toby Walsh (INRIA-Lorraine)

Getting Started

- *Don't trust yourself*: bugged code may give better performance than the correct one

- *Do make it fast enough*: as fast as possible

Experimental Design

- *Do collect all data possible*: every branches, every procedures should be taken into consider
- *Do it all again*: reproducibility
- *Do it often and do it big*: perform lots of experiments on large problems
- *Do be stupid*: stupid experiments give fascinating results.

Analysis of Data

- *Do loot at the raw data*
- *Do look for good views*: finding a good view of data
- *Don't reject the obvious*

Presentation of Results

- *Do present statistics*: give min, mean, max, median, standard deviation
- *Do report negative results*: as valuable as reporting positive results
- *Don't push deadlines*: 

## Hooker

title: Testing Heuristics: We Have It All Wrong

by J. N. Hooker (Carnegie Mellon University)

The evils of competitive testing

- makeing the competition fair: machines' differences, coding skills
- choice of test problems: benchmark problem

A more scientific alternative

- branching rule

what to measure: things predicted by the model

## How Branch Mispredictions Affect Quicksort

by Kanela Kaligosi (Max Planck Institut für Informatik) and Peter Sanders (Universität Karlsruhe)

Quicksort

- random pivot, median-of-three, α-skewed pivot

Branch Prediction Schemes

* static branch prediction

  α-skewed pivot selection, not take dynamic behavior of program

* dynamic branch prediction

  - 1-bit predictor: look the last time
  - 2-bit predictor: look back twice
  - k-bit predictor: look back k times

conclusion

- median-of-3 pivot selection bring no significant benefits in practice: increase of branch mispreddictions

## Tradeoffs Between Branch Mispredictions and Comparisons for Sorting Algorithms

Gerth Stølting Brodal and Gabriel Moruz (Aarhus Universitet Datalogi)

optimal comparison based sorting algorithm: Θ(n log n)

一大堆公式看着都烦，不想看了

## Skewed Binary Search Trees

Gerth Stølting Brodal and Gabriel Moruz (Aarhus Universitet Datalogi)

skewed BST: a constant $\alpha\in(0,\frac{1}{2}]$, s. t. for each node v there is a fixed ratio between the num of nodes in the subtree rooted in the left child and the subtrees rooted at v.

又是一大段公式证明，看不懂

hardware discussion

又在讨论体系结构的东西

branch misprediction

memory layouts

# 课堂笔记

## Intro to AE

算法工程简介

20世纪四五十年代发明的几种排序算法

复习一下渐进时间复杂度 O Θ Ω

快速排序时间复杂度最差情况下是 O(n^2)

实际操作中又是还需要关注常数是否过于离谱

## Quick Histroy & BG

硬件的发展：缓存和存储层次、分支预测、多核、GPU、分布式云计算等等

暴力算法耗时，但是好实现

the gap between theory and practice!

## Overview of AE

- 对算法进行详细研究
- 测试&执行
- 分离并控制有影响的参数
- 建模
- 统计分析
- bridge the gap between theory and practice

涉及的层次

- 系统结构
- 算法和数据结构
- 高级语言的微调 tuning
- 低级语言的微调
- 系统软件
- 平台和硬件

## What's not AE

- algorithmic horse race（田忌赛马式的对比）

- 建立最快的算法

- 实现一些算法并看谁最快

而是要关注

- best practices
- 理解模型、假设、评估、解释结果、形成理论

## Models of computation

理论假设的 RAM

- 所有的内存都是寄存器
- 基础的 CPU 操作被认为是 O(1) 的
- 寄存器简单的读写被认为是 O(1) 的

计算几何中的 RAM

- 理论上认为存储器存的实数
- 所有的简单运算（加减乘除开 k  次方根）被认为是 O(1) 的
- 好处：理论简单化，易于设计分析算法
- 坏处：不显示，disparity between theory and practice

In reality

- 寄存器、一级缓存、二级缓存、RAM、固态硬盘、机械硬盘

  越往后需要的时钟周期越多，相邻两级存储相差数十到数千倍不等

- 机械硬盘非常慢，访存延迟特别高（盘片旋转+寻道时间）

## IO Model

假设 B 大小存储块中有 N 个数字

问题一：找最大值

Max 设为负无穷，从左到右挨着扫一遍，更新 Max 即可。**I/O Compexity: O(n)**

问题二：快速排序中找到一个枢纽值 P 进行左右 交换 （Partition） 操作

如果存储大小 ≥ 2*B，也是 n 次块内读，n 次块内写 **I/O Compexity: O(n)**

问题三：快速排序

$f(n)=O(n\log n)$

快排的 IO 复杂度：$O(\frac{N}{B}\log\frac{N}{B})$

堆排的 IO 复杂度: $O(N \log N)$

## On Experiments

what to measure?

- CPU time **sometimes could be misleading: machine dependent, OS and other factors**
- Cycles, clock time
- cache misses, I/O, etc.

- counters

## Modeling performance and other metrics

检查 running time

- O(log n) 当 n 加倍时，运行时间增加一个常数
- O(n) 当 n 加倍时，运行时间加倍
- O(n^2) 当 n 加倍时，运行时间加四倍
- O(n log n) 除去 n，当成 log n 检查
- O(sqrt(n)) 当 n 加四倍时，运行时间加倍

**注意在数据规模较小时，常数的影响 dominates！**