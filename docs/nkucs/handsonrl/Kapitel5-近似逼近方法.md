---
title: 动手强化学习 第五章 近似逼近方法
author: tinsir888
date: 2023/2/11
cover: /img/handsonrl.PNG
katex: true
tags:
  - 笔记
  - 强化学习
categories:
  - 动手学习强化学习课程笔记
abbrlink: bf2e452b
---

# 参数化值函数近似

## 针对之前的模型

都是基于创建一个查询表，再表中维护状态值函数或状态-动作值函数。

当处理大规模的 MDP 时

- 状态/状态-动作空间非常大
- 连续的状态或动作空间

维护起来代价太大 

### 处理大规模 MDP 的解决方法

- 对状态/动作进行离散化或粪桶
- 构建参数化的值函数估计

## 对状态/动作进行离散化

例如：在一个二维平面空间中，使用网格对状态空间进行切分，从而转化成离散值

对于一个大型的 MDP，可以对状态值进一步分桶以进行采样聚合

### 优点

- 操作简洁直观
- 高效
- 在处理许多问题时有较好的效果

### 缺点

- 过于简单表示价值函数 V
- 可能为每个离散区间假设一个常数值
- 维度灾难

## 参数化价值函数

### 参数化值函数近似

- 构建参数化（可学习的）函数来近似值函数

  $V_\theta(s)\simeq V^\pi(s)$

  $Q_\theta(s,a)\simeq Q^\pi(s,a)$

  - θ 时近似函数的参数，可通过 RL 进行更新
  - 参数化的方法将现有可见的状态泛化到没有见过的状态上

### 值函数近似的主要形式

- 一些函数近似
  - 线性模型
  - 神经网络
  - 决策树
  - 最近邻
  - 傅里叶/小波基底
- 可微函数
  - 线性模型
  - 神经网络
- 希望模型适合在非静态的、非独立同分布的数据上训练

### 基于随机梯度下降 SGD 的值函数近似

- 目标：找到参数向量 θ 最小化值函数近似值与真实值之间的均方误差

  $J(\theta)=\mathbb E_\pi[\frac{1}{2}(V^\pi(s)-V_\theta(s))^2]$

- 误差减小的梯度方向

  $-\frac{\partial J(\theta)}{\partial \theta}=\mathbb E_\pi[(V^\pi(s)-V_\theta(s))\frac{\partial V_\theta(s)}{\partial \theta}]$

- 单次采样随机梯度下降

  $\theta\leftarrow\theta-\alpha\frac{\partial J(\theta)}{\partial\theta}$

  $=\theta+\alpha(V^\pi(s)-V_\theta(s))\frac{\partial V_\theta(s)}{\partial\theta}$

### 特征化状态

用一个特征向量表示状态

# 价值函数近似算法

## 状态值函数近似

用特征的线性组合表示价值函数 $V_\theta(s)=\theta^Tx(s)$

目标函数是参数的 θ 的二次函数 $J(\theta)=\mathbb E_\pi[\frac{1}{2}(V^\pi(s)-\theta^Tx(s))^2]$

因此随机梯度下降能够收敛到全局最优解上

> $\theta\leftarrow\theta-\alpha\frac{\partial J(\theta)}{\partial\theta}$
>
> $=\theta+\alpha(V^\pi(s)-V_\theta(s))x(s)$
>
> α 是步长，中间的差值是预测误差，x(s) 是特征值

使用蒙特卡洛状态值函数近似，蒙特卡洛预测至少能收敛到一个局部最优解

使用时序差分值函数近似

## 状态 - 动作值函数近似

对动作 - 状态值函数进行近似 $Q_\theta(s,a)\simeq Q^\pi(s,a)$

最小均方误差 $J(\theta)=\mathbb E_\pi[\frac{1}{2}(Q^\pi(s,a)-Q_\theta(s,a))^2]$

在单个样本上进行随机梯度下降

> $\theta\leftarrow\theta-\alpha\frac{\partial J(\theta)}{\partial\theta}$
>
> $=\theta+\alpha(Q^\pi(s,a)-Q_\theta(s,a))\frac{\partial Q_\theta(s,a)}{\partial\theta}$

### 线性状态 - 动作值函数近似

使用特征向量表示状态 - 动作对

线性情况下，参数化 Q 函数 $Q_\theta(s,a)=\theta^Tx(s,a)$

利用随机梯度下降更新

### 时序差分状态 - 动作值函数近似

对于蒙特卡洛学习，目标是累计奖励

对于时序差分，目标是当前奖励和未来预测奖励之和

# 策略梯度

## 参数化策略

$\pi_\theta(a|s)$，策略可以是确定的 $a=\pi_\theta(s)$，也可以是随机的 $\pi_\theta(a|s)=P(a|s;\theta)$。

- $\theta$ 是策略的参数
- 将可见的已知状态泛化到位置的状态上

## 基于策略的强化学习

优点

- 更好的收敛性质
- 高维度或连续动作空间中更有效
- 能学习出随机策略

缺点

- 通常或收敛到局部最优
- 评估一个策略通常不够高效率并且具有较大的方差

# 策略梯度

对于随机策略 $\pi_\theta(a|s)=P(a|s;\theta)$

降低低价值/奖励动作出现的概率，提高高价值/奖励动作出现的概率。

## 单步马尔可夫决策过程的策略梯度

考虑一个但不马尔可夫决策过程

- 起始状态 $s\sim d(s)$
- 决策过程在进一步决策后结束，获得奖励值 $r_{sa}$

策略的价值期望

$J(\theta)=\mathbb E_{\pi_\theta}[r]=\sum_{s\in S}d(s)\sum_{a\in A}\pi_\theta(a|s)r_{sa}$

$\frac{\partial J(\theta)}{\partial\theta}=\sum_{s\in S}d(s)\sum_{a\in A}\frac{\partial\pi_\theta(a|s)}{\partial\theta}r_{sa}$

## trick: 似然比 Likelihood Ratio

$\frac{\partial\pi_\theta(a|s)}{\partial\theta}=\pi_\theta(a|s)\frac{1}{\pi_\theta(a|s)}\frac{\partial\pi_\theta(a|s)}{\partial\theta}$

$=\pi_\theta(a|s)\frac{\partial\log\pi_\theta(a|s)}{\partial\theta}$

因此，$\frac{\partial J(\theta)}{\partial\theta}=\sum_{s\in S}d(s)\sum_{a\in A}\pi_\theta(a|s)\frac{\partial\log\pi_\theta(a|s)}{\partial\theta}r_{sa}$

$=\mathbb E_{\pi_\theta}[\frac{\partial\log\pi_\theta(a|s)}{\partial\theta}r_{sa}]$

这一结果可以通过 $d(s)$ 中采样状态 $s$ 和从 $\pi_\theta$ 中采样动作 $a$ 来近似估计

## 策略梯度定理

将似然比推导过程泛化到马尔可夫决策过程

长期价值函数 $Q^{\pi_\theta}(s,a)$ 替换 $r_{sa}$

起始状态目标函数 $J_1$，平均奖励目标函数 $J_{avR}$，平均价值目标函数 $J_{avV}$

定理：对任意可微的策略 $\pi_\theta(a|s)$，任意策略的目标函数 $J=J_1$,$J_{avR}$,$J_{avV}$，其策略梯度为：

$\frac{\partial J(\theta)}{\partial\theta}=\mathbb E_{\pi_\theta}[\frac{\partial\log\pi_\theta(a|s)}{\partial\theta}Q^{\pi_\theta}(s,a)]$

## 蒙特卡洛策略梯度（REINFORCE）

随机梯度上升

策略梯度定理

利用累计奖励值 $G_t$ 作为 $Q^{\pi_\theta}(s,a)$ 的无偏采样。

$\Delta\theta_t=\alpha\frac{\partial\log\pi_\theta(a_t|s_t)}{\partial\theta}G_t$

## Softmax 随机策略

非常常用的随机策略 $\pi_\theta(a|s)=\frac{e^{f_\theta(s,a)}}{\sum_{a'}e^{f_\theta(s,a')}}$

$f_\theta(s,a)$ 是 $\theta$ 参数化的状态-动作对得分函数，可预定义。

其对数似然梯度为 $\frac{\partial\log f_\theta(s,a)}{\partial\theta}-\mathbb E_{a'\sim\pi_\theta(a'|s)}[\frac{\partial f_\theta(s,a')}{\partial\theta}]$

# Actor-Critic

同时基于价值函数和策略梯度的算法

## REINFORCE存在的问题

1. 基于片段式数据的任务

   通常情况下，任务需要终止状态，REINFORCE才能直接计算累计折扣黄历

2. 低数据利用效率

   需要大量训练数据

3. 高训练方差（重要缺陷）

   从单个或多个片段中采样得到的值函数具有很高的方差

## AC算法的引入

为什么不建立一个可训练的值函数$Q_\phi$完成$Q(s,a)$的评估过程

演员 $\pi_\theta(a|s)$ 采取动作使评论家满意的策略

- 学会采取是评论家满意的动作
- $J(\theta)=\mathbb E_{s\sim p,\pi_\theta}[\pi_\theta(a|s)Q_\Phi(s,a)]$
- $\frac{\partial J(\theta)}{\partial\theta}=\mathbb E_{\pi_\theta}[\frac{\partial\log\pi_\theta(a|s)}{\partial\theta}Q_\Phi(s,a)]$

评论家 $Q_\phi(s,a)$ 学会准确估计演员策略所采取动作价值的值函数

- 学会估计当前演员策略的动作价值
- $Q_\phi(s,a)\simeq r(s,a)+\gamma\mathbb E_{s'\sim p(s'|s),a'\sim\pi_\theta(a'|s')}[Q_\phi(s',a')]$

## A2C, Advantageous Actor-Critic

通过减去一个基线函数来标准化评论家的打分，有点normalize的意思

- 更多信息指导：降低较差动作概率，提高较优动作概率
- 进一步降低方差

优势函数 Advantage Function

$A^\pi(s,a)=Q^\pi(s,a)-V^\pi(s)$

状态-动作值和状态值函数

$Q^\pi(s,a)=r(s,a)+\gamma\mathbb E_{s'\sim p(s'|s),a'\sim\pi_\theta(a'|s')}[Q_\phi(s',a')]$

$=r(s,a)+\gamma\mathbb E_{s'\sim p(s'|s)}[V^\pi(s')]$

因此，

$A^\pi(s,a)=r(s,a)+\gamma\mathbb E_{s'\sim p(s'|s)}[V^\pi(s')-V^\pi(s)]$

$\simeq r(s,a)+\gamma[V^\pi(s')-V^\pi(s)]$