---
title: 动手强化学习 第三章 值函数估计
author: tinsir888
date: 2022/8/14
cover: /img/handsonrl.PNG
katex: true
tags:
  - 笔记
  - 强化学习
categories:
  - 动手学习强化学习课程笔记
abbrlink: 4730b98c
---

# 蒙特卡洛方法

## 模型无关的强化学习

- 在现实问题中，通常没有明确给出**状态转移**和**奖励函数**

  比如，我们只看到了一些 episodes（采样）：

  - Episode1: $s_0^{(1)}\xrightarrow{a_0^{(1)},R(s_0)^{(1)}}s_1^{(1)}\xrightarrow{a_1^{(1)},R(s_1)^{(1)}}s_2^{(1)}\cdots s_T^{(1)}$

  - Episode2: $s_0^{(2)}\xrightarrow{a_0^{(2)},R(s_0)^{(2)}}s_1^{(2)}\xrightarrow{a_1^{(2)},R(s_1)^{(2)}}s_2^{(2)}\cdots s_T^{(2)}$

- 模型无关的强化学习直接从经验中学习**值 value**和**策略 policy**，无需构建 MDP。

- 关键步骤

  1. 估计值函数
  2. 优化策略

## 值函数估计

- 在基于模型的强化学习中，值函数通过动态规划计算获得。

  $V^\pi(s)=R(s)+\gamma\sum\limits_{s'\in S}p_{s\pi(s)}(s')V^{\pi}(s')$

- 在模型无关的强化学习中

  - 无法直接获得 $P_{sa}$ 和 $R$
  - 但我们可以根据已有的经验来估计值函数
    - Episode1: $s_0^{(1)}\xrightarrow{a_0^{(1)},R(s_0)^{(1)}}s_1^{(1)}\xrightarrow{a_1^{(1)},R(s_1)^{(1)}}s_2^{(1)}\cdots s_T^{(1)}$

    - Episode2: $s_0^{(2)}\xrightarrow{a_0^{(2)},R(s_0)^{(2)}}s_1^{(2)}\xrightarrow{a_1^{(2)},R(s_1)^{(2)}}s_2^{(2)}\cdots s_T^{(2)}$

## 蒙特卡洛方法

Monte-Carlo methods 是一类广泛的计算算法

- 依赖于重复随机抽样来获得数值结果

例子

- 估算圆周率
- 围棋对弈：AlphaGo

## 蒙特卡洛价值估计

- 目标：从策略 $\pi$ 下的经验片段学习 $V^\pi$

  $s_0^{(i)}\xrightarrow{a_0^{(i)},R(s_0)^{(i)}}s_1^{(i)}\xrightarrow{a_1^{(i)},R(s_1)^{(i)}}s_2^{(i)}\cdots s_T^{(i)}\sim\pi$

- 累积奖励（return）是总折扣奖励

  $G_t=R_{t+1}+\gamma R_{t+2}+\gamma^2R_{t+3}+\cdots+\gamma^{T-1}R_T$

- 值函数（value function）是期望累计奖励

  $V^\pi(s)=\mathbb E[R(s_0)+\gamma R(s_1)+\cdots|s_0=s,\pi]$

  $=\mathbb E[G_t|s_t=s,\pi]$

  $\simeq\frac{1}{N}\sum_{i=1}^NG_t^{(i)}$

  - 使用策略 $\pi$ 从状态 $s$ 采样 $N$ 个片段
  - 计算平均累计奖励

- 蒙特卡洛策略评估使用**经验均值累计奖励**而不是**期望累计奖励**

### 实现蒙特卡洛值估计

实现

- 使用策略 $\pi$ 采样片段

  $s_0^{(i)}\xrightarrow{a_0^{(i)},R(s_0)^{(i)}}s_1^{(i)}\xrightarrow{a_1^{(i)},R(s_1)^{(i)}}s_2^{(i)}\cdots s_T^{(i)}\sim\pi$

- 在一个片段中**每个时间步长 t 的状态 s** 都被访问

  - 增量计数器 $N(s)\leftarrow N(s)+1$
  - 增量总累计奖励 $S(s)\leftarrow S(s)+G_t$
  - 值被估计为累计奖励的均值 $V(s)=\frac{S(s)}{N(s)}$
  - 由大数定律得 $V(s)\rightarrow V^\pi(s)\ as\ N(s)\rightarrow\infty$

### 增量蒙特卡洛更新

- 每个片段结束后逐步更新 $V(s)$

- 对于每个状态 $S_t$ 和对应累计奖励 $G_t$

  $N(S_t)\leftarrow N(S_t)+1$

  $V(S_t)\leftarrow V(S_t)+\frac{1}{N(S_t)}(G_t-V(S_t))$

- 对于非稳定问题（即环境会随时间发生变化），我们可以跟踪一个现阶段的平均值（即不考虑过久之前的片段）

  $V(S_t)\leftarrow V(S_t)+\alpha(G_t-V(S_t))$

### 实现值估计

思路：$V^\pi(s)\simeq\frac{1}{N}\sum_{i=1}^NG_t^{(i)}$

实现：$V(S_t)\leftarrow V(S_t)+\alpha(G_t-V(S_t))$

- 直接从经验片段中学习

- model-free，模型无关的：为止马尔可夫决策过程的状态转移/奖励

- 从**完整**的片段中进行学习：没有使用 bootstrapping 方法

- 最简单的思想：值 value = 平均累计奖励 mean return

- 警告:warning:：**只能讲蒙特卡洛方法应用于可分片段的马尔可夫决策过程中！**

  即，所有的片段都有终止状态

# 模型无关控制方法

## 模型无关的控制应用场景

- 一些能够被建模成马尔可夫决策过程的问题

  电梯、平行泊车、船舶操纵、生物反应器、直升机、飞机物流、Atari和星际争霸、投资组合管理、围棋对弈……

- 对于大部分真实世界的问题：

  - MDP 模型为未知，但能从经验中采样
  - MDP 模型为已知，但规模太大难以直接使用，只能通过采样

- 模型无关的控制能解决上述问题

## 在线策略和离线策略学习

两类模型无关的强化学习

1. 在线策略学习 On-Policy
   - Learning on the job
   - 利用策略 $\pi$ 的经验采样不断学习改进策略 $\pi$
2. 离线策略学习 Off-Policy
   - Look over someone's shoulder
   - 利用另一个策略 $\mu$ 的经验采样不断学习改进策略 $\pi$

## 状态值和状态-动作值

$G_t=R_{t+1}+\gamma R_{t+2}+\cdots+\gamma^{T-1}R_T$

- 状态值

  马尔可夫决策过程的状态值函数 $V^\pi(s)$ 是指从状态 $s$ 开始，执行策略 $\pi$ 的期望累计奖励为

  $V^\pi(s)=\mathbb E_\pi[G_t|S_t=s]$

- 状态-动作值

  马尔可夫决策过程的状态-动作值函数 $Q^\pi(s,a)$ 是指从状态 $s$ 开始，执行 $a$ 动作之后，执行策略 $\pi$ 的期望累计奖励

  $Q^\pi(s,a)=\mathbb E_\pi[G_t|S_t=s,A_t=a]$

## 贝尔曼期望方程

- 状态值函数 $V^\pi(s)$ 可被分解为即时奖励加上后续状态的折扣值

  $V^\pi(s)=\mathbb E_\pi[R_{t+1}+\gamma V^\pi(S_{t+1})|S_t=s]$

- 状态-动作值函数 $Q^\pi(s,a)$  也能被分解为

  $Q^\pi(s,a)=\mathbb E_\pi[R_{t+1}+\gamma Q^\pi(S_{t+1},A_{t+1})|S_t=s,A_t=a]$

- 因此

  - $V^\pi(s)=\sum_{a\in A}Q^\pi(s,a)$
  - $Q^\pi(s,a)=R(s,a)+\gamma \sum_{s'\in S}P_{sa}(s')V^\pi(s')$

## 模型无关的策略迭代

- 给定状态值函数 $V(s)$ 和状态-动作值函数 $Q(s,a)$，模型无关的策略迭代应使用状态-动作值函数

- 基于状态值函数 $V(s)$ 的贪心策略改进需要建立马尔可夫决策过程的模型

  $\pi^{new}(s)=\arg\max_{a\in A}\{R(s,a)+\gamma\sum_{s'\in S}P_{sa}(s')V^\pi(s')\}$

  - 我们不知道状态转移概率 $P_{sa}(s')$

- 基于状态-动作值函数 $Q(s,a)$ 的贪心策略改进是模型无关的

  $\pi^{new}(s)=\arg\max_{a\in A}Q(s,a)$

## 使用状态-动作值函数的广义策略迭代

- 策略评估：蒙特卡洛策略估计 $Q=Q^\pi$
- 策略改进：贪心策略改进

## ε-greedy 策略探索

- 确保持续探索最简单的想法

- 所有 m 个动作都以非零概率进行尝试

- 以 1-ε 的概率，选择贪心动作

- 以 ε 的概率，随机选择一个动作

  $\pi(a|s)=\frac{\epsilon}{m}+1-\epsilon$ 当 $a^*=argmax_{a\in A}Q(s,a)$

  $\pi(a|s)=\frac{\epsilon}{m}$ otherwise

## ε-greedy 策略改进

定理：

- 对于任意 ε-greedy 的策略 $\pi$，关于 $Q^\pi$ 的 ε-greedy 策略 $\pi'$ 是 $\pi$ 的一个改进，即 $V^{\pi'}(s)\ge V^\pi(s)$

$V^{\pi'}(s)=Q^\pi(s,\pi'(s))=\sum_{a\in A}\pi'(a|s)Q^\pi(s,a)$

$=\frac{\epsilon}{m}\sum_{a\in A}Q^\pi(s,a)+(1-\epsilon)\max_{a\in A}Q^\pi(s,a)$

$\ge\frac{\epsilon}{m}\sum_{a\in A}Q^\pi(s,a)+(1-\epsilon)\sum_{a\in A}\frac{\pi(a|s)-\frac{\epsilon}{m}}{1-\epsilon}Q^\pi(s,a)$

$=\sum_{a\in A}\pi(a|s)Q^\pi(s,a)=V^\pi(s)$

## 蒙特卡洛控制

对于每个片段 episode

- 策略评估：蒙特卡洛策略估计 $Q≈Q^\pi$
- 策略改进：ε-greedy 策略改进

## 蒙特卡洛控制 vs. 时序差分控制

- 时序差分（TD）学习相比蒙特卡洛（MC）有许多优点
  - 更低的方差
  - 在线策略
  - 基于不完整的序列
- 自然的想法：在控制中，使用时序差分代替蒙特卡洛
  - 使用时序差分来更细状态-动作值函数
  - 使用 ε-greedy 策略改进
  - 每个时间步长更新状态-动作值函数

## 重要性采样

- 估计一个不同分布的期望

  $\mathbb E_{x\sim p}[f(x)]=\int_xp(x)f(x)dx$

  $=\int_xq(x)\frac{p(x)}{q(x)}f(x)dx$

  $=\mathbb E_{x\sim q}[\frac{p(x)}{q(x)}f(x)]$

- 将每个实例的权重重新分配为 $\beta(x)=\frac{p(x)}{q(x)}$

## 使用重要性采样的离线策略蒙特卡洛

- 使用策略 $\mu$ 产生的累计奖励评估策略 $\pi$

- 根据两个策略之间的重要性比率（importance ratio）对累计奖励 $G_t$ 加权

- 每个片段乘以重要性比率

  $\{s_1,a_1,r_2,s_2,a_2,\cdots,s_T\}\sim\mu$

  $G_t^{\pi/\mu}=\frac{\pi(a_t|s_t)\pi(a_{t+1}|s_{t+1})}{\mu(a_t|s_t)\mu(a_{t+1}|s_{t+1})}\cdots\frac{\pi(a_T|s_T)}{\mu(a_T|s_T)}G_t$

- 更新值函数以逼近修正的累计奖励值

  $V(s_t)\leftarrow V(s_t)+\alpha(G_t^{\pi/\mu}-V(s_t))$

  - 无法在 $\pi$ 非零而 $\mu$ 为 0 时使用
  - 重要性采样将**显著增大方差**（variance）

## 使用重要性采样的离线策略时序差分

- 使用策略 $\mu$ 产生的时序差分目标评估策略 $\pi$

- 根据重要性采样对时序差分目标 $r+\gamma V(s')$ 加权

- **仅需要一步**来进行重要性采样验证

  $V(s_t)\leftarrow V(s_t)+\alpha(\frac{\pi(a_t|s_t)}{\mu(a_t|s_t)}(r_{t+1}+\gamma V(s_{t+1}))-V(s_t))$

  - 重要性采样修正：$\frac{\pi(a_t|s_t)}{\mu(a_t|s_t)}$
  - 时序差分目标：$r_{t+1}+\gamma V(s_{t+1})$

# 时序差分学习

## 时序差分学习

$G_t=R_{t+1}+\gamma R_{t+2}+\gamma^2R_{t+3}+\cdots=R_{t+1}+\gamma V(S_{t+1})$

$V(S_t)\leftarrow V(S_t)+\alpha(R_{t+1}+\gamma V(S_{t+1})-V(S_t))$

观测值 $R_{t+1}$，对未来的猜测 $\gamma V(S_{t+1})$

- 时序差分方法直接从经验片段中进行学习
- 时序差分是**模型无关**的
  - 不需要预先获取 MDP 的状态转移/奖励
- 通过自举法（bootstrapping），时序差分从**不完整的片段中学习**
- 时序差分更新当前预测值使之接近估计累计奖励（非真实值）

## 蒙特卡洛 MC VS 时序差分 TD

相同的目标：从策略 $\pi$ 下的经验片段学习 $V^\pi$

- 增量地进行每次蒙特卡洛过程

  - 更新值函数 $V(S_t)$ 使之接近准确累计奖励 $G_t$

    $V(S_t)\leftarrow V(S_t)+\alpha(G_t-V(S_t))$

- 最简单的时序差分学习算法：

  - 更新 $V(S_t)$ 使之接近估计累计奖励 $R_{t+1}+\gamma V(S_{t+1})$

    $V(S_t)\leftarrow V(S_t)+\alpha(R_{t+1}+\gamma V(S_{t+1})-V(S_t))$

  - 时序差分目标： $R_{t+1}+\gamma V(S_{t+1})$

  - 时序差分误差：$\delta_t=R_{t+1}+\gamma V(S_{t+1})-V(S_t)$

## 蒙特卡洛和时序差分的优缺点

时序差分能够在知道最后结果之前进行学习

- 能够在每一步之后进行学习
- 蒙特卡洛必须等待片段结束，直到累计奖励已知

时序差分能够无需最后结果而进行学习

- 时序差分能够从不完整序列中学习，而蒙特卡洛只能从完整序列中学习
- 时序差分在连续*（无终止的）*环境下工作
- 蒙特卡洛只能在片段化*（有终止）*环境下工作

### 蒙特卡洛 MC

蒙特卡洛具有高方差，无偏差 $V(S_t)\leftarrow V(S_t)+\alpha(G_t-V(S_t))$

- 良好的收敛性质
  - 使用函数近似时依然如此
- 对初始值不敏感
- 易于理解和使用

### 时序差分 TD

时序差分具有低方差，有偏差 $V(S_t)\leftarrow V(S_t)+\alpha(R_{t+1}+\gamma V(S_{t+1})-V(S_t))$

- 通常比蒙特卡洛更高效
- 时序差分最终收敛到 $V^\pi(S_t)$
  - 但使用函数近似时并不总是如此
- 比蒙特卡洛对初始值更加敏感

## 偏差 Bias 和方差 Variance 的权衡

- 累计奖励 $G_t=R_{t+1}+\gamma R_{t+2}+\cdots+\gamma^{T-1}R_T$ 是 $V^\pi(S_t)$ 的无偏估计
- 时序差分真实目标 $R_{t+1}+\gamma V^\pi(S_{t+1})$ 是 $V^\pi(S_t)$ 的无偏估计
- 时序差分目标 $R_{t+1}+\gamma V(S_{t+1})$ 是 $V^\pi(S_t)$ 的有偏估计

- 时序差分目标具有比累计奖励更低的方差
  - 累计奖励——取决于多步随机动作，多步状态转移和多步奖励
  - 时序差分目标——取决于单步随机动作，单步状态转移和单步奖励

## 多步时序差分学习

- 对于有时间约束的情况，我们可以跳过 n 步预测的部分，直接进入模型无关的控制

- 定义 n 步累计奖励

  $G_t^{(n)}=R_{t+1}+\gamma R_{t+2}+\cdots+\gamma^{n-1}R_{(t+n)}+\gamma^nV(S_{t+n})$

- n 步时序差分学习

  $V(S_t)\leftarrow V(S_t)+\alpha(G_t^{(n)}-V(S_t))$