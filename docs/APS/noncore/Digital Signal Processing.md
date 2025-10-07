---
title: APS 非重点复习课程之数字信号处理
author: tinsir888
date: 2022/11/06
cover: /img/aps.png
katex: true
tags:
  - APS
  - 德国
  - APS 非重点复习课程
  - 数字信号处理
  - 留学
categories:
  - 留学申请
  - APS 审核复习
abbrlink: 730ca896
---

# Signal

Sampling: sensor→A/D

Digital signal VS analog signal

> analog: both X Y are contiguous. digital: both are discrete.

# Basic Operations on Digital Signal

$x[n]=\{\cdots,-0.2,2.2,1.1,0.2,-3.7,\cdots\}$

addition, multiplication, time shifting(delay), reversing(反折), stretching(拉伸)

difference(差分): $x'[n]=x[n+1]-x[n]$

accumulation(累加)

:warning:Convolution(卷积​，非常重要): $F(t)=x(n)*h(n)=\sum_kx[n-k]h[k]$

- commutative 交换律
- associative 结合律
- distributive 分配律

Circular convolution: $y(n)=x(n)\circledast h(n)=\sum_k(x[n-k]h[k])\times R_k(n)$

length of results of convolution between x[n] and h[k]: n+k-1.

# Signal Types

[details](https://tinsir888.github.io/posts/f4e2b65f.html)

pulse signal: $\delta(t)$

step signal: $u(n)$, $\delta(n)=u(n)-u(n-1)$

sine and cosine signal.

# Signaling System

## LTI

aka Linear Time Invariant System

$y[n]=\alpha x_1[n]+\beta x_2[n]$

accumulator is a typical LTI.

median filter is not LTI.

# Fourier Transform

aka fourier transform

|             FT type             |          object          |
| :-----------------------------: | :----------------------: |
|        Fourier Transform        | non-periodic, contiguous |
|         Fourier Series          |   periodic, contiguous   |
| Discrete Time Fourier Transform |  non-periodic, discrete  |
|     Discrete Fourier Series     |    periodic, discrete    |

DTFT: $X(e^{j\omega})=\sum_{n=0}^{N-1}x(n)e^{-j\omega n}$

DFS: $X(n)=\sum_{n=0}^{N-1}x(n)e^{-j\frac{2\pi}{N}kn}$

Inverse DFS: $x(n)=\frac{1}{N}\sum_{n=0}^{N-1}X(n)e^{j\frac{2\pi}{N}kn}$