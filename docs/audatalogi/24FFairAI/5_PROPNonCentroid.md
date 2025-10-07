---
title: Fair AI/ML 5 PROP in Non-Centroid Clustering
author: tinsir888
date: 2024/10/31
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
abbrlink: '397e7496'
---

Read the paper [Proportional Fairness in Non-Centroid Clustering](https://www.cs.toronto.edu/~emicha/papers/fair-noncentroid-clustering.pdf) by Caragiannis et al.

This paper was accepted in NeurIPS 2024!

# Introduction

$n$ points, $k$ clusters $C=(C_1,\cdots,C_k)$, distance metric $d$.

$k$-means objective: $\sum_{i=1}^k\frac{1}{|C_i|}\cdot\sum_{x,y\in C_i}d(x,y)^2$.

Scenario in which there are no cluster centers.

Question:

1. Can we obtain PROP guarantee for non-centroid clustering?
2. Do the algorithms for centroid clustering also work for non-centroid one?
3. Can we audit the PROP of a given algorithm?

## Contribution

Each agent $i$ has a loss function $\ell_i$, the loss under clustering $C$ is $\ell_i(C(i))$. $C(i)$ denotes the cluster containing her.

- $\ell_i$ can be arbitrary
- $\ell_i$ can be distance

PROP fairness guarantee:

1. the **core**
2. and its relaxation, fully justified representation **FJR**.

Summary of results

| Loss functions |      Core UB      | Core LB  |           FJR           |
| :------------: | :---------------: | :------: | :---------------------: |
|   Arbitrary    |     $\infty$      | $\infty$ |           $1$           |
|    Average     | $O(n/k)$ polytime |  $1.3$   | $1$ ($4$ in polytime) |
|    Maximum     |   $2$ polytime    |   $1$    | $1$ ($2$ in polytime) |

FJR as a relaxation of the core. This paper propose `GreedyCohesiveClustering`, which is an adaptation of Greedy Cohesive Rule from social choice theory.

## Related Work

PROP by Chen et al.

PROP revisit by Micha and Shah

FJR in centroid clustering by Aziz et al.

# Model

$[t]=\{1,\cdots,t\}$ for $t\in\N$. Given a set $N$ of $n$ agents, and the desired number of clusters $k$.

Each agent $i\in N$ has an associated loss function $\ell_i:2^N\diagdown2^{N\diagdown i}\to\R_{\ge0}$, where $\ell_i(S)$ is the cost to agent $i$ for being part of group $S$. A $k$-clustering $C=(C_1,\cdots,C_k)$ is a partition of $N$ into $k$ clusters, where $C_t\cap C_{t'}=\emptyset$ for $t\neq t'$ and $\cup_{t=1}^kC_t=N$. For simplicity, $C(i)$ is the cluster containing $i$.

The loss of $i$ is $\ell_i(C(i))$.

## Loss function

- A distance metric over $N$ is given by $d:N\times N\to\R_{\ge0}$, which satisfies normality, symmetry and triangle inequality.
- Arbitrary losses. The loss $\ell_i(S)$ is arbitrary non-negative number for each agent $i\in N$ and cluster $S\ni i$.
- Average loss. Given a distance metric $d$ over $N$, $\ell_i(S)=\frac{1}{|S|}\sum_{j\in S}d(i,j)$ for each agent $i\in N$ and cluster $S\ni i$. Agent $i$ prefers the agents in her cluster to be close to her on average.
- Maximum loss. Given a distance metric $d$, $\ell_i(S)=\max_{j\in S}d(i,j)$. Agent $i$ prefers that no agent in her cluster to be too far from her.

# Core

If no group of agents $S\subseteq N$ can choose another outcome that

1. They are entitled to choose based on **their proportion** of the whole population $|S|/|N|$
2. Every member of group $S$ is happier

:book:Definition 1 ($\alpha$-Core). For $\alpha\ge1$, a $k$-clustering $C=(C_1,\cdots,C_k)$ is said to be in the $\alpha$-core if there is no group of agents $S\subseteq N$ with $|S|\ge n/k$ s.t. $\alpha\cdot\ell_i(S)\lt\ell_i(C(i))$ for all $i\in S$. $1$-core is the core.

If a group $S$ exists that becomes a violation of the $\alpha$-core guarantee, we say that it deviates and refer to it as the deviating coalition.

:dart:Theorem 1. For arbitrary losses, there exists an instance in which no $\alpha$-core clustering for any finite $\alpha$.

:dart:Theorem 2. For the average loss, there exists an instance in which no $\alpha$-core clustering exists an instance in which no $\alpha$-core clustering exists for $\alpha\lt\frac{1+\sqrt3}{2}\approx1.366$.

:question:Open problem 1: For the maximum loss, does there always exist a clustering in the core?

:question:Open problem 2: For the average loss, does there always exist a clustering in the $\alpha$-core for some constant $\alpha$?

## GreedyCohesiveClustering Algorithm

It uses a subroutine $\mathcal A$, in which given a subset of agents $N'\subseteq N$, metric $d$, and threshold $\tau$, finds a **cohesive** cluster $S$.

The threshold $\tau$ indicate the smallest size at which a group of agents deserve to form a cluster.

$\mathcal A$ is SmallestAgentBall. It finds the smallest ball centered at agent that captures at least $\tau$ agents, and returns a set of $\tau$ agents from this ball.

- Natural choice: $\tau=\lceil n/k\rceil$

So GreedyCohesiveClustering with SmallestAgentBall iteratively finds the smallest agent-centered ball containing $\lceil n/k\rceil$ agents and removes $\lceil n/k\rceil$ in that ball, until fewer than $\lceil n/k\rceil$ agents remain, at which point all remaining agents are put into one cluster  and any remaining clusters are left empty.

### Pseudo code for GreedyCohesiveClustering

Input: $N,d,k$

Output: $k$-clustering $C$

1. $N'\gets N$ // remaining set of agents
2. $j\gets 1$ // current cluster member
3. while $N'\neq\emptyset$ do
   - $C_j\gets\mathcal A(N',d,\lceil n/k\rceil)$ // find and remove the next cohesive cluster
   - $N'\gets N'\diagdown C_j$
   - $j\gets j+1$
4. $C_j,C_{j+1},\cdots,C_k\gets\emptyset$
5. return $C=(C_1,\cdots,C_k)$

### Pseudo code for SmallestAgentBall

Input: Subset of agent $N'\subseteq N,d,\tau$

Output: a cluster $S$

1. if $|N'|\le\tau$ then return $S\gets N'$
2. if $i\in N'$ do
   - $\ell_i\gets\tau$-th closest agent in $N'$ to agent $i$ // arbitrarily broke the tie
   - $r_i\gets d(i,\ell_i)$ // smallest ball centered at agent $i$ capturing at least $\tau$ agents
3. $i^*\gets\arg\min_{i\in N'}r_i$
4. return $S\gets$ the set of $\tau$ closest agents in $N'$ to agent $i^*$



Actually the GreedyCohesiveClustering is an adaptation of Greedy Capture by Chen et al. There are two key difference:

1. Growing balls centered at the **agents** instead of all **feasible candidate centers**.
2. Stop a ball as soon as it captures $\lceil n/k\rceil$ agents. While in GC, it continues.

:dart:Theorem 3. For the average (resp. maximum) loss, the Greedy Capture algorithm is guaranteed to return a clustering in the $(2\lceil n/k\rceil -3)$-core (resp. $2$-core) in $O(kn)$ time complexity, and the bounds are tight.

# Fully Justified Representation

Peters et al. introduced FJR as a relaxation of the core in the context of approval-based committee selection.

:book: Definition 2 ($\alpha$**-FJR**). For $\alpha\ge1$, a $k$-clustering $C$ satisfies $\alpha$-FJR if there is no group of agents $S\subseteq N$ with $|S|\ge n/k$ s.t. $\alpha\cdot\ell_i(S)\lt\min_{j\in S}\ell_j(C(j))$ for each $i\in S$, i.e., if $\alpha\cdot\max_{i\in S}\ell_i(S)\lt\min_{j\in S}\ell_j(C(j))$. We refer to $1$-FJR as FJR.

:thinking:Proposition 1. For $\alpha\ge1$, $\alpha$-core implies $\alpha$-FJR for arbitrary loss functions.

> Trivially, $\ell_i(C(i))\ge\min_{j\in S}\ell_j(C(j))$.

## Arbitrary Loss Functions

FJR clustering is guaranteed to exist even for arbitrary losses.

:book:Definition 3 (most cohesive cluster). Given a set of agents $N$ and a threshold $\tau$, the MostChesiveCluster problem asks to find a cluster $S\subseteq N$ of size at least $\tau$ s.t. the maximum loss of any $i\in S$ for $S$ is minimized, i.e., find $\arg\min_{S\subseteq N':|S|\ge\tau}\max_{i\in S}\ell_i(S)$.

For $\lambda\ge1$, a $\lambda$-approximate solution $S$ satisfies $\lambda\cdot\max_{i\in S}\ell_i(S)\le\max_{i\in S'}\lambda_i(S')$ for all $S'\subseteq N$ with $|S'|\ge\tau$, and a $\lambda$-approx algorithm returns a $\lambda$-approx solution on every instance.

If we plug in $\mathcal A$ to the MostCohesiveCluster problem into GreedyCohesiveClustering algorithm yields $\lambda$-FJR clustering.

:dart:Theorem 4. For arbitrary losses, $\alpha\ge1$, and an $\alpha$-approximation algorithm $\mathcal A$ for the MostCohesiveCluster problem, GreeedyCohesiveClustering is guaranteed to return a $\alpha$-FJR clustering. Hence, FJR is guaranteed to exist.

> Proof omitted.

## Average and Maximum Loss Functions

Let $\mathcal A^*$ be an exact ALG for the MostCohesiveCluster problem for the average loss.

It may run not in polynomial time. It can be used to detect whether a given undirected graph admits a clique of at least a given size (NP-Complete).

:thinking:Lemma 1. For the average (resp. maximum) loss, SmallestAgentBall is a $4$-approximation (resp. $2$-approximation) algorithm for the MostCohesiveCluster problem, and this is tight.

:thinking:Corollary 1. The Greedy Capture algorithm is guaranteed to return a clustering that is $4$-FJR (resp. $2$-FJR) for the average (resp. maximum) loss.

:question:Open problem 3: For the average or maximum loss, what is the smallest $\alpha$ for which an $\alpha$-FJR clustering can be computed in polynomial time, assuming $P\neq NP$?

:question:Open problem 4: What is the smallest $\alpha$ s.t. there always exists a clustering that is simultaneously $\alpha$-FJR for both the average loss and the maximum loss?

## Auditing FJR

Ideas used to find an approx FJR can also be used to audit the FJR approx of any given clustering.

:book:Definition 4 ($\lambda$-approx FJR auditing). $\mathcal A$ is a $\lambda$-approx FJR auditing algorithm, if given any clustering $C$, it returns $\theta$ s.t. the exact FJR approx of $C$ is in $[\theta,\lambda\cdot\theta]$.

:dart:Theorem 5. For $\lambda\ge1$, if $\mathcal A$ is a $\lambda$-approx algorithm to the MostCohesiveCluster problem, then AuditFJR$(\mathcal A)$ is a $\lambda$-approx FJR auditing algorithm. Given lemma 1, it follows that for the average (resp. maximum) loss, AuditFJR(SmallestAgentBall) is an efficient $4$-approx (resp. $2$-approx) FJR auditing algorithm.

:question:Open problem 5: Does there exist a polynomial time, $\alpha$-approx core auditing algorithm for some constant $\alpha$?

:dart:Theorem 6. Assuming $P\neq NP$, there does not exist a polynomial-time $\lambda$-approx FJR auditing algorithm for the maximum loss, for any $\lambda\lt2$,

# Experiments

Omitted

# Discussion

**Introduction to Proportional Fairness in Non-Centroid Clustering**: This paper initiates the study of proportional fairness in non-centroid clustering and presents several open questions for further exploration.

**Key Open Questions**:

- Achieving a better approximation than $O(n/k)$ for the core concerning average loss.
- Determining if the core is always non-empty for maximum loss.

**Core Non-Emptiness Results**:

- Proven that the core is always non-empty for maximum loss in 1-dimensional metric spaces (Appendix C.1).
- For average loss, however, the core remains empty even on the line (Appendix C.2).

**Differences Between Clustering Settings**:

- Highlights significant distinctions between centroid and non-centroid clustering.

**Future Directions**:

- Investigate proportional fairness guarantees when an agent’s loss depends on both their cluster center and other agents in their cluster.
- Explore the possibility of intrinsically choosing the number of clusters $k$, which currently requires being fixed in advance for proportional fairness.

**Compatibility with Classical Algorithms**:

- While traditional algorithms like $k$-means and $k$-centers may be incompatible with the core and FJR (Appendix E), studying conditions under which they might align with fairness criteria or can be efficiently computed is an interesting avenue.

