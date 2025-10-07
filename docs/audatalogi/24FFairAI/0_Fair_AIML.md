---
title: Fair AI/ML 0 Overview
author: tinsir888
date: 2024/9/2
cover: /img/AUFairAI.jpg
katex: true
tags:
  - 笔记
  - 算法博弈论
  - 人工智能
categories:
  - AUDatalogi
  - 算法博弈论
  - AI/ML 公平性
abbrlink: 21c5a30c
---

This semester, I will do a project again with Iannis.

I took some time to review the tutorial [Fairness in AI/ML via Social Choice](https://www.cs.toronto.edu/~nisarg/tutorials/fair-AI-SC.html) by Evi Micha & Nisarg Shah.

Social Choice Theory: Aggregating individual preferences into "good" collective decisions

# Fairness in Social Choice

很熟悉了，可以快速掠过。

:warning:这里考虑的是 DIVISIBLE ITEMS.

## EF, PROP

Fair division: Agents $N$, divisible resources $M$, utility function $u_i$.

Non-negative utilities (good), non-positive utilities (chores).

Proportionality, envy-freeness.

## The Core

A core is a subset of agents, among which they can redistribute their entitlements.

But redistributed entitlements shouldn't be a **Pareto improvement**.

2 types of the core

1. Resource-scaling version

   No group of agents $S$ should be able to find any allocation $B$ of their proportionally entitled share of the resources that is Pareto improvement.
   $$
   \not\exists S\subseteq N,\frac{|S|}{|N|}\cdot M\to B:u_i(B_i)\gt u_i(A_i)\forall i\in S
   $$

2. Utility-scaling version

   No group of agents $S$ should be able to find any allocation $B$ of the resources that is a Pareto improvement even after proportional utility-scaling.
   $$
   \not\exists S\subseteq N,M\to B:\frac{|S|}{|N|}\cdot u_i(B_i)\gt u_i(A_i)\forall i\in S
   $$

- Two versions are equivalent for some setting.
- They are different when utilities are not linear additive.
- If there is no scalable resource, there is no way to define resource-scaling version. But it is more appealing.

## Nash Social Welfare

NSW is geometric mean of agent utilities.
$$
NSW(A)=\left(\prod_{i\in N}u_i(A_i)\right)^{1/|N|}
$$
There are some other welfare functions:

- Utilitarian social welfare: $USW(A)=\frac{1}{|N|}\cdot\sum_{i\in N}u_i(A_i)$

  ~~为什么要起这么怪的名字，叫 average social welfare 不好嘛！~~

- Egalitarian social welfare: $ESW(A)=\min_{i\in N}u_i(A_i)$

  ~~明明可以叫 least social welfare 的……~~

:thinking:Theorem \[Varian '74\]: Any allocation maximizing NSW is envy-free in the core.

:thinking:Theorem \[Orlin '10\]: An allocation maximizing the NSW can be computed in strongly polynomial time.

## Application of Core: Committee Selection

There are set of voters $N$, set of candidates $M$.

Each agent $i$ approves a subset of candidates $A_i\subseteq M$.

- The utility for each voter is defined as the number of intersection of committee member and its approval.
  $$
  u_i(W)=|W\cap A_i|
  $$

- The goal is to find $W\subseteq M$ with $|W|\le k$ with given $k$.

$W$ is in the core if there is no $S\subseteq N$ and $T\subseteq M$ with $|T|\le\frac{|S|}{|N|}\cdot k$ (==Here the candidates are resources, so its **resource-scaling version**==) s.t. $u_i(T)\gt u_i(W)\forall i\in S$.

## Advantages

It's broadly defined

- It often depends only on the definition of **AGENTS** and **PREFERENCES**.
- Applicable to any setting as long as you define these two pieces of information.

Notions such as the core achieve group fairness to all possible groups.

- No need to pre-specify the groups.
- scale automatically with group size and cohesiveness, without additional parameters.

# Envy-Freeness in ML

## Classification

The model

- Population of individuals given by a distribution $D$ over $X$.

  individual $i$ represent data point $x_i\in X$.

- Classifier $f:X\to Y$ maps every individual to a classification outcome

Types of classification:

- Hard binary
- Hard multi-class
- Soft binary
- Soft multi-class

Objective: minimize loss function

Utility function should be somehow related to loss function.

## How to measure fairness in ML?

### Individual Fairness

\[Dwork, Hardt, Pitassi, Reingold, Zemel, 2012\]

Similar individuals should be treated similarly

Classifier $f$ is individual fair if
$$
\forall x,y\in N,D(f(x),f(y))\le d(x,y)
$$

### Envy-Freeness

\[Balcan, Dick, Noothigattu, Procaccia, 2019\]

Equal individuals shouldn't envy each other.

Classifier $f$ is envy-free if
$$
\forall x,y\in N,u_x(f(x))\ge u_x(f(y))
$$
Envy-freeness is too strong for deterministic classifiers

- loss of optimal deterministic EF classifier $\ge1$.
- Loss of optimal randomized EF classifier $\le1/\gamma$.

~~一直没看懂这一部分在讲个啥~~

### Preference-Informed IF

\[Kim, Korolova, Rothblum, Yona, 2019\]

Similar individuals shouldn't envy each other too much.

Classifier is PIIF if
$$
\forall x,y\in N,\exists z\in Y,D(z,f(y))\le d(x,y)\land u_x(f(x))\ge u_x(z).
$$

### Approximate EF

Equal individuals shouldn't envy each other too much.

Classifier $f$ is approximately EF if $\forall x,y\in N,u_x(f(x))\ge u_x(f(y))-\epsilon$.

OR

Classifier $f$ is approximately EF if $\forall x,y\in N,u_x(f(x))\ge u_x(f(y))-d(x,y)$

### Average Group Envy-Freeness

Equal **groups** shouldn't envy-each other too much on average.

Classifier $f$ is approximately average group EF w.r.t. groups $G_1,G_2$ if
$$
\mathbb E_{x\sim G_1,y\sim G_2}[u_x(f(y))-u_x(f(x))]\le 0
$$
Applicable for decision-making with limited resources

- e.g. deciding on loan or bail applications
- envy can not be prevented

Can be imposed for several pairs of groups simultaneously

## Recommendations

\[Do, Corbett-Davies, Atif, Usunier, 2023\]

Model

- Individuals represented by data points in set $X$
- A set items $Y$
- A set of contexts $C$

Recommendation policy $\pi$

- $\pi_x(y|c)$ is probability of recommending item $y$ to user $x$ given a context $c$.

Utility function: $u_x(\pi_x)=\mathbb E_{c\sim C_m,y\sim\pi_x(\cdot|c)}[v_x(y|c)]$.

Envy-freeness: $\forall x,x'\in X,u_x(\pi_x)\ge u_x(\pi_{x'})-\epsilon$

### Two-Sided Fairness

!!! info

    为什么是 two-sided 呢？因为可以给用户定义效用函数，也可以给要推荐的商品定义效用函数。



\[Biswas, Patro, Ganguly, Gummadi, Chakraborty, 2023\]

Many-to-many matching

- each user is recommended $k$ products
- each product may be recommended to a different number of users

Relevance of products to users given by $V:X\times Y\to\mathbb R$

Recommendation policy $\pi$

- Each user $x$ is recommended $\pi_x\subseteq Y$ with $|\pi_x|=k$
- Let $Y_x^*$ be the top-k products for user $x$ by relevance

Utilities

- Utility to user $x$ given by $u_x(\pi_x)=\frac{\sum_{y\in\pi_x}V(x,y)}{\sum_{y\in\pi_x^*}V(x,y)}$
- Utility to product $y$ given by $E_y(\pi)$, the number of users $y$ is exposed to

Two-sided fairness

- Fairness for users: EF1
  $$
  \forall x,x'\in X,\exists y\in\pi_{x'}:u_x(\pi_x)\ge u_x(\pi_{x'}\diagdown\{y\})
  $$

- Fairness for products: minimum exposure $\overline E$
  $$
  \forall y\in Y,E_y(\pi)\ge\overline E
  $$

- 

Theorem: There exists an efficient algorithm that achieves EF1 among all users and the minimum exposure guarantee among at least $m-k$ products.

## Non-Centroid Clustering

\[Ahmadi, Awasthi, Khuller, Kleindessner, Morgenstern, Sukprasert, Vakilian, 2022\]

\[Aamand, Chen, Liu, Silwal, Sukprasert, Vakilian, Zhang, 2023\]

Goal: Partition the agents into $k$ clusters, i.e., $C=(C_1,\cdots,C_k)$

Model

- Set of agents $N$ partitioned into $C=(C_1,\cdots,C_k)$.
- Cluster containing agent $i$ denoted by $C(i)$.
- Distance metric $d:N\times N\to\mathbb R_{\ge 0}$.

$\alpha$-EF: For each $i\in N$ and $j\in[k]$ with $i\not\in C_j$: either $C(i)=i$ or
$$
\frac{1}{|C(i)|-1}\sum_{i'\in C(i)}d(i,i')\le\frac{\alpha}{|C_j|}\sum_{i'\in C_j}d(i,i')
$$
Theorem: 

A 1-envy-free clustering **does not always exist**, but an $O(1)$-envy-free clustering always exist and can be computed efficiently.

### Another Setting

\[Li, M, Nikolov, S, 2023\]

Model

- Set of agents $N$ partitioned into $C=(C_1,\cdots,C_k)$.
- Cluster containing agent $i$ denoted by $C(i)$.
- Binary costs $d:N\times N\to\{0,1\}$

Theorem: There exists a balanced clustering (satisfying $|C(i)|=|C(j)|\pm1\forall i,j$) s.t. for all $i\in N$ and $j\in[k]$, $\sum_{i'\in C(i)}d(i,i')\le\sum_{i'\in C_j}d(i,i')+\tilde O(\sqrt{n/k})$.

- If we divide by the size of the clusters, then the additive term becomes $o(1)$.

# Nash Social Welfare in ML

## Multi-Armed Bandits

多臂赌博机，遗憾最小化算法

$k$ arms, $\mu_1,\mu_2,\cdots,\mu_k$, $\mu^*=\arg\max_{j\in[k]}\mu_j$.

Exploration vs Exploitation

Regret $R_T=T_{\mu^*}-\sum_{t=1}^T\mu(t)$

## Multi-Agent Multi-Armed Bandits

[Hossain, M, S, 2021]

Agents $N$

Each arm $j\in[k]$ for each agent $i\in N$ have different data distribution $\mu_{ij}$.

For each agent $\mu_i^*=\arg\max_{j\in[k]}\mu_{ij}$.

:question:What is a fair policy?

Distribution $p=[p_1,\cdots,p_k]$ gives expected reward $\sum_{j=1}^kp_j\cdot\mu_{ij}$ to agent $i$

Maximizing welfare functions

1. Utilitarian welfare $\sum_{i=1}^n\sum_{j=1}^kp_j\cdot\mu_{ij}$
2. Egalitarian welfare $\min_{i\in N}\sum_{j=1}^kp_j\cdot\mu_{ij}$
3. Nash welfare $\prod_{i=1}^n\sum_{j=1}^kp_j\cdot\mu_{ij}$

Regret: $R_T=NSW(p^*,\mu)-\sum_{t=1}^TNSW(p(t),\mu)$

:thinking:Theorem: A variation of UCB achieves the optimal $\Theta(\sqrt T)$ regret.

## Fair Exploration

\[Barman, Khan, Maiti, Sawarni, 2023\]

Agent $t$ arrives at time $t$

We chose distribution $P_t$, which gives the agent utility $E_{j\sim P_t}[\mu_j]$

Regret: $R_T=\mu^*-(\prod_{t=1}^TE_{j\sim P_t}[\mu_j])^{1/T}$

Theorem: A variation of UCB achieves near-optimal regret in terms of $T$.



[Baek, Farias, 2021]

Agent $t$ arrives at time $t$ and belongs to group $g_t$, where $g_t=g$ with probability $p_g$

Policy: choose arm $a_t$ at time $t$

Regret for group $g:u_T^g(\pi)=\sum_{t\in[T]:g_t=g}(\mu^*-\mu_{a_t})$

Utility to group $g:u^g(\pi)=R_T^g(\pi^0)-R_T^g(\pi)$, where $\pi^0$ is the policy minimizing the overall regret

Nash social welfare: $NSW(\pi)=\prod_gu^g(\pi)$

Theorem: A version of UCB exactly optimizes NSW.

# Core in ML

## Federated Learning

[Chaudhury, Li, Kang, Li, Mehta, 2022]

FL: Choose $f_\theta:\mathbb R^d\to\mathbb R$ from $F=\{f_\theta:\theta\in P\subseteq\mathbb R^d\}$

Utility of each agent: $u_i(\theta)=M-\mathbb E_{(x,y)\sim D_i}[\ell(f_\theta(x),y)]$

Goal: Choose $\theta$ that is fair for all agents

Core: A parameter vector $\theta\in P$ is in the core if for all $\theta'\in P$ and $S\subseteq N$, it holds
$$
u_i(\theta)\ge\frac{|S|}{|N|}u_i(\theta')\forall i\in S,
$$
with at least one strict inequality.

Pareto Optimality

Proportionality

:thinking:Theorem: When the agents' utilities are continuous and the set of maximizer of any conical combination of the agents' utilities is convex, a parameter vector $\theta\in P$ in the core always exists.

:thinking:Theorem: When the agents' utilities are concave, then the parameter vector $\theta\in P$ that maximizes the NEW is in the core:
$$
\text{maximize }\prod_{i\in N}u_i(\theta)\text{ subject to }\theta\in P\\
\text{maximize }\sum_{i\in N}\log(u_i(\theta))\text{ subject to }\theta\in P
$$

## Clustering

### In ML

Goal: Analyze datasets to summarize their characteristics

Objects in the same group are similar

### In Economics

Goal: Allocate a set of facilities that serve a set of agents. (e.g. hospital)

### Centroid Clustering

Input

- Set $N$ of $n$ data points
- Set $M$ of $m$ feasible cluster centers
- $\forall i,j\in N\cup M$: we have $d(i,j)$ which forms a *Metric Space*
  - $d(i,i)=0\forall i\in N\cup M$
  - $d(i,j)=d(j,i)\forall i,j\in N\cup M$
  - $d(i,j)\le d(i,k)+d(k,j)\forall i,j,k\in N\cup M$

Output

- A set $C\subseteq M$ of $k$ centers
- Each data points is assigned to its closest cluster center $C(i)=\arg\min_{c\in C}d(i,c)$

### Some Objective Functions

$k$-median: minimizes the sum of the distances

$k$-means: minimizes the sum of the square of the distances

$k$-center: minimizes the maximum distance

## Fairness in Clustering

\[Chen, Fain, Lyu, Munagala, 2019\]

Fair clustering through proportional entitlement

- Every group of $n/k$ agents deserves its own cluster center

Definition in Committee Selection: $W$ is the core if

- For all $S\subseteq N$ and $T\subseteq M$

- If $|S|\ge|T|\cdot n/k$, then $|A_i\cap W|\ge|A_i\cap T|$ for some $i\in S$

  "If a group can afford $T$, then $T$ should not be a strict Pareto improvement for the group"

Definition in Clustering: $C$ is in the core if

- For all $S\subseteq N$ and $y\subseteq M$

- If $|S|\ge n/k$, then $d(i,C(i))\le d(i,y)$ for some $i\in S$

  "If a group can afford a center $y$, then $y$ should not be a strict Pareto improvement for the group"

## Core in Centroid Clustering

\[Chen, Fain, Lyu, Munagala, 2019\]

A clustering solution in the core does not always exist

Approximation core

$\alpha$-core: A solution $C$ is in the $\alpha$-core with $\alpha\ge1$ if there is **no** group of points $S\subseteq N$ with $|S|\ge n/k$ and $y\in M$ s.t.
$$
\forall i\in S,\alpha\cdot d(i,y)\lt d(i,C(i))
$$
:thinking:Theorem. There exists an algorithm Greedy Capture that returns a clustering solution in the $(1+\sqrt2)$-core under any metric space. For arbitrary metric spaces and $\alpha\lt 2$, a clustering solution in the $\alpha$-core is not guaranteed to exist.

:thinking:Theorem.

- For $L_2$, Greedy Capture returns a solution in the $2$-core
- For $L_1$ and $L_\infty$, GC does not always return a $(\lt1+\sqrt2)$-core
- For $L_2$ and $\alpha<1.154$, a clustering solution in the $\alpha$-core is not guaranteed to exist
- For $L_1$ and $L_\infty$, and $\alpha\lt1.4$, a clustering solution in the $\alpha$-core is not guaranteed to exist

## Core in Non-Centroid Clustering

Non-Centroid Clustering

- Partition the individuals into $k$ clusters
- loss for each cluster: for $S\subseteq N$ and $i\in S,\ell_i(S)\ge0$.

$\alpha$-core: A solution $C$ is in the $\alpha$-core with $\alpha\ge1$, if there is **no** group of points $S\subseteq N$ with $|S|\ge n/k$ s.t.
$$
\forall i\in S,\ell_i(S)\lt\ell_i(C(i))
$$
Average loss: for each $S\subseteq N,\ell_i(S)=\frac{1}{|S|}\sum_{i'\in S}d(i,i')$

:thinking:Theorem

- Greedy Capture returns a clustering solution in the $(n/k)$-core under any metric space
- For arbitrary metric space and $\alpha\lt1.366$, a clustering solution in the $\alpha$-core is not guaranteed to exist

Open question: Does a clustering solution in the $O(1)$-core always exist?