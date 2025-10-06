---
title: 计算机视觉 W37 Neural Networks
author: tinsir888
date: 2023/9/11
cover: /img/AUcv.jpg
katex: true
tags:
  - 笔记
  - 计算机视觉
categories:
  - AUDatalogi
  - 计算机视觉
abbrlink: f0afc3ee
---

# Decision boundaries

motivation for non-linear transformation

在线性的 logistic 回归中，$h_w(x)$ 大于 0.5 时认为是，小于 0.5 时认为不是

有的时候数据分布不适合使用逻辑回归模型（比如二维平面上一类包住了另一类）

简单采用 adding polynomial terms 的方法并不合适，需要泛化性更强的方法。

解决办法：神经网络

universal approximation theorem 用简单的台阶函数去拟合（？）

# Neural networks

人工神经网络

- algorithm that try to mimic the brain
- SOTA
- AlexNet: bid data & GPUs
- perform well on many tasks

## artificial neurons

aka perceptron

$y=1\ if\ \sum_{i=1}^nw_ix_i-\theta\ge0$, $y=0\ if\ \sum_{i=0}^nw_ix_i-\theta\lt0$

$\theta$ 被称作偏置 bias

$w$ 是一种 template, if x matches the template well, the inner product will be larger than the threshold.

普通的线性函数，需要经过 non-linear 的 activation function (e. g. sigmoid).

input layer, hidden layer, output layer

前向传播

某一神经节点 $a^{(1)}=x$

线性计算 $z^{(j+1)}=W^{(j)}a^{(j)}+b^{(j)}$

经过激活函数 $a^{(j+1)}=\sigma(z^{(j+!)})$

> 思考：如果 $j$ 层有 $s_j$ 个神经元，$j+1$ 层有 $s_{j+1}$ 个神经元。那么 $W^{(j)}$ 的大小是？
>
> $s_{j+1}$ 行 $\times$$s_{j}$ 列

相当于输入 x 和第 W 矩阵的对应一行的内积加上偏置，再进入一个激活函数。

NNs learn useful features representations.

MLP multilayer perceptron 多层感知机，就是一个多层的全连接神经网络。

# XNOR with NNs

nn can learn complex non-linear functions.

non-XOR tasks

# Multi-class classification with NNs

task: learn to predict K classes from given training data

output layer: $P(y=k|x)$ k-th element of $h_{W,b}(x)$

train label aka ground truth: one-hot vectors

# Loss functions

cross-entropy for softmax regression

$J(W)=-\sum_i\sum_k\mathbf{1}\{y^{(i)}=k\}\log(P(y^{(i)}=k|x^{(i)}))$

loss of neural network

$J(W,b)=-\frac{1}{n}\sum_i\sum_ky_k^{(i)}\log(h_{W,b}(x^{(i)})_k)$

# Optimization - back propagation

Gradient computation 为什么要用反向传播？因为直接计算梯度很耗时

使用 quadratic loss 进行推导比较简单

$J(W,b)=-\frac{1}{2n}(h_{W,b}(x^{(i)})_k-y_k^{(i)})^2$

需要计算 $\frac{\partial J(W,b)}{\partial W_{i,j}^{(l)}}$ 和 $\frac{\partial J(W,b)}{\partial b_i^{(l)}}$

第一步：计算的时候前向传播

第二步：算出 loss，根据损失函数进行求导（链式法则）

$\delta_j^{(l)}$ 表示第 l 层第 j 个神经元的 error

$\delta^{(l)}$ 是一个向量

假设神经网络有四层：

$\delta^{(4)}=(a^{(4)}-y^{(k)})\odot\sigma'(z^{(4)})$

$\delta^{(i)}=(W^{(i)})^T\delta^{(i+1)}\odot\sigma'(z^{(i)})$

$\odot$ 表示矩阵或向量对应元素相乘

there is no $\delta^{(1)}$，因为第一层代表输入 x

得到 $\Delta W$ 和 $\Delta b$：

$\Delta W_{ij}^{(l)}=\Delta W_{ij}^{(l)}+a_j^{(l)}\delta_i^{(l+1)}$ for $i,j$ in layers $l=1\cdots L-1$

$\Delta b_i^{(l)}=\Delta b_i^{(l)}+\delta_i^{(l+1)}$ for $i$ in layers $l=1\cdots L-1$

更新网络参数