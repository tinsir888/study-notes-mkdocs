---
title: Fair AI/ML 8 Split and Merge Algorithm on Average Cost
author: tinsir888
date: 2025/2/18
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
abbrlink: 5449ffa2
---

硕士论文就跟 Non-Centroid Clustering 杠上了。我和导师都希望能

- 探索 Split and Merge 算法的更多性质
- 扩展 Split and Merge 算法到更一般的情况

仍然只考虑：

1. binary distance

   the distance of each pair of data point is either $1$ or $2$.

2. non-metric

   no restriction of triangle inequality

3. asymmetric distance

   $dis(a,b)\neq dis(b,a)$

# Equivalent Setting

Those three conditions is equivalent to **APPROVAL VOTING**.

- Each data point is a voter.
- Each voter approve a subset of other data points

## Cost Definition

The maximum and average costs are therefore defined equivalently and accordingly.

The maximum cost:

$$
cost(i)=\begin{cases}
1\text{ if }\exists j\in C_i,i\text{ doesn't approve }j\\
0\text{ otherwise}
\end{cases}
$$

The minimum cost:

$$
cost(i)=\frac{\#j\in C_i\text{, s.t. }i\text{ doesn't approve}}{|C_i|}
$$

!!! success

    It's not hard to prove that Split and Merge algorithm also computes a clustering in the core with **BINARY, ASYMMETRIC, NON-METRIC** "distance".



# On Average Cost

:dart:Theorem (On Average Cost): Split and Merge algorithm computes a clustering in $\lceil n/k\rceil$-core.

> Assume there exist a subset $V$ of $\lceil n/k\rceil$ agents where each agent would improve more than multiplicative factor $\lceil n/k\rceil$.
>
> In Split and Merge algorithm, the average cost of data point $i$ is at most $\frac{|C_i|-1}{|C_i|}$.
>
> Therefore, for each subset of $\lceil n/k\rceil$, there exist one agent does not improve its cost (from $1$ to $0$). **(Consider maximum cost, Split and Merge computes the solution in the core, which means for each subset of $\lceil n/k\rceil$, not all agent would be strictly better off.)**
>
> Thus, the average cost of this agent is at most $1$. Possible improved average cost is at least $\frac{1}{\lceil n/k\rceil}$, meaning that the improvement can not be greater than factor of $\lceil n/k\rceil$.

Can we improve this $\lceil n/k\rceil$ factor? No idea... :thinking:

# Breakthrough: A Variation of Split and Merge Computes Better Approximation w.r.t. Average Cost

Non-metric, asymmetric and binary distance.

Average cost function.

1. Construct directed graph.

2. Split phase: Do iteratively

   - If there exist $\lceil n/k\rceil$-clique, put them in one cluster and discard them.

   - else if there exist such a cluster, put then in one cluster and discard them:

     The **largest** group of points s.t. no points is at distance $1$ from $\sqrt{\lceil n/k\rceil}$ other points.

     ~~// This step is as hard as MAXCLIQUE.~~

3. Merge phase: Pick the smallest clusters, merge them in order to have at most $k$ clusters.

:dart:Theorem ($\sqrt{\lceil n/k\rceil}$-core w.r.t. average cost): This variation outputs a clustering solution in $\sqrt{\lceil n/k\rceil}$-core.

> Assume there exist a subset $V$ of $\lceil n/k\rceil$ agent improving more than $\sqrt{\lceil n/k\rceil}$. In the variation, average cost of an agent $i$ is $\le\frac{|C_i|-1}{|C_i|}\lt1$.
>
> For each subset of $\lceil n/k\rceil$ agents, there exist one agent having average cost $\le\frac{\sqrt{\lceil n/k\rceil}}{\lceil n/k\rceil}$. (By the greedy property of second level of split phase)
>
> The possible improved average cost is at least $\frac{1}{\lceil n/k\rceil}$. Meaning that improvement can not be more than $\sqrt{\lceil n/k\rceil}$, this leads to contradiction.

# Following Directions on Average Cost

From the paper, we learn that for **symmetric, metric, arbitrary** distance, the lower bound of core approximation factor is $1.3$.

:thinking:But what if we consider binary distance remove symmetric and metric limitation? Could the core be non-empty in this case?

:thinking:What about graphs with certain structures? (scale-free, small-world networks...)