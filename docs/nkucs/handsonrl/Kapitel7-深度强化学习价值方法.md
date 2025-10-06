---
title: 动手强化学习 第七章 深度强化学习价值方法
author: tinsir888
date: 2023/5/6
cover: /img/handsonrl.PNG
katex: true
tags:
  - 笔记
  - 强化学习
categories:
  - 动手学习强化学习课程笔记
abbrlink: c2c3d571
---

# 深度强化学习

## 价值和策略近似

是否可以通过深度神经网络直接近似价值/策略函数？

## 端到端强化学习

标准的 传统的 计算机视觉 需要 特征提取 中级特征提取 分类……

而 AlexNet 的提出，深度学习的出现，使得 计算机视觉称为端到端的

相同的，深度强化学习使强化学习算法能够以端到端的方式解决复杂问题

深度强化学习

1. 利用深度神经网络进行价值函数和策略近似
2. 使得算法能够以端到端的方式解决复杂问题

2013，DeepMind 首次提出，用深度强化学习玩 Atari 游戏。

## 深度学习和强化学习结合

- 价值函数和策略编程深度神经网络
- 高维的参数空间
- 难以稳定训练
- 容易过拟合
- 需要大量数据
- 需要高性能计算
- CPU 收集经验数据 - GPU 训练神经网络 trade-off

## 深度强化学习的分类

- 基于价值：深度 Q 网络及其变种

- 基于随机策略的方法：策略梯度、自然策略梯度、信任区域策略优化 TRPO、近端策略优化 PPO、A3C
- 确定性策略梯度 DPG，DDPG

# 深度 Q 网络

## DQN

### 复习 Q learning

- 不直接更新策略
- 基于值的方法
- Q learning 学习一个由 θ 作为参数的函数 $Q_\theta(s,a)$

而深度 Q 网络的直接想法

- 使用神经网络逼近 $Q_\theta(s,a)$
- 算法不稳定
  - 连续采样得到的 SASR 不满足独立分布，因为 Q 频繁更新

解决办法

- 经验回放
- 使用双网络结构：评估网络和目标网络

### 经验回放

存储训练过程中的每一步 SASR 于数据库中，采样时服从均匀分布

优先经验回放

- 衡量标准
  - 以 Q 函数值与 Target 值的差异来衡量学习的价值
  - 为了使各样本都有机会被采样
- 选中的概率
- 重要性采样 采用权重

### 目标网络

$Q_\theta(s,a)$

- 使用较旧的参数，记为 $\theta^-$，每隔 C 步和训练网络的参数同步一次

目标网络是慢更新的网络，相对比较稳定

### 算法流程

1. 收集数据，使用 ε-greedy 的策略进行探索，得到的状态动作组 sars 放如经验池 replay-buffer
2. 采样：从数据库中采样 k 个动作状态组
3. 更新网络：
   - 采样得到的数据计算 loss
   - 更新 Q 函数网络 θ
   - 每 C 次迭代更新以此目标网络的 $\theta^-$

## Double DQN

### Q-Learning 中的过估计

Q 函数的过高估计 over estimation

- Target 值，如果当前估计有偏差，max 函数会放大这个偏差

过高估计的原因

- $\mathbb E[\max(X_1,X_2)]\geq\max(\mathbb E(X_1),\mathbb E(X_2))$

- $\max_{a'\in A}(s_{t+1},a')\geq\max(\mathbb E(R|s_{t+1},a_1,\theta'),\mathbb E(R|s_{t+1},a_2,\theta'),\cdots)$

Q 函数的值被视作在状态 s‘，动作 a' 下的回报期望值

Q 函数的过高估计随着候选行动数量增大变得更严重

### 解决过估计问题

使用不同的网络来估值和决策

两个 Q 网络：$Q_\theta$ 用于选择动作；$Q_{\theta'}$ 用于评估更新 label

## Dueling DQN

假设动作值函数服从某分布 $Q(s,a)\sim\mathcal N(\mu,\sigma)$

显然：$V(x)=\mathbb E[Q(s,a)]=\mu$

同样有 $Q(s,a)=\mu+\epsilon(s,a)$（有偏估计）

$\epsilon(s,a)=Q(s,a)-V(s)$ 也被称为优势函数 Advantage Function

### 优势函数

$A^\pi(s,a)=Q^\pi(s,a)-V^\pi(s)$

$Q^\pi(s,a)=\mathbb E[R_t|s_t=s,a_t=a,\pi]$

$V^\pi(s)=\mathbb E_{a\sim\pi(s)}[Q^\pi(s,a)]$

不同优势函数的聚合形式

- $Q(s,a;\theta,\alpha,\beta)=V(s;\theta,\beta)+(A(s,a;\theta,\alpha)-\max_{a'\in|A|}A(s,a';\theta,\alpha))$
- $Q(s,a;\theta,\alpha,\beta)=V(s;\theta,\beta)+(A(s,a;\theta,\alpha)-\frac{1}{|A|}A(s,a';\theta,\alpha))$

### 优点

- 处理与动作关联较小的状态
- 状态值函数的学习较为有效：一个状态值对应多个优势函数值

