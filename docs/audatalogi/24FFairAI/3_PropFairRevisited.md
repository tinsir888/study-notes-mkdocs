---
title: Fair AI/ML 3 PROP Fair Clustering Revisited
author: tinsir888
date: 2024/10/10
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
abbrlink: b69f1531
---

Read the paper [Proportionally Fair Clustering Revisited](https://www.semanticscholar.org/paper/Proportionally-Fair-Clustering-Revisited-Micha-Shah/c8ac6968a2d26acbd0316f3f9da81a2f8037901a) by Micha et Shah.

$k$ cluster centers must be placed given $n$ points in a metric space.

The cost to each point is its distance to the nearest cluster center.

This paper: Focus on the case where **cluster centers can be placed anywhere in metric space**.

E.g. $L^2$ distance over $\mathbb R^t$, the approximation ratio of greedy capture improves to $2$.

For $L^1,L^\infty$ metric, the approximation ratio remains $1+\sqrt 2$.

The paper gives universal lower bounds which apply to all algorithms.

# Introduction

Points $\mathcal N$, possible cluster centers $\mathcal M$.

Given $k\in\mathbb N$, task is to select a set $X\subseteq\mathcal M$ consisting of $|X|=k$ cluster centers.

Group fairness.

Proportional fairness.

## Contribution

Focus on the case where the metric consists of usual distance functions such as $L^1,L^2,L^\infty$ over $\mathbb R^t$.

:warning:**Cluster centers can be placed anywhere in the infinite metric space**. $\mathcal M=\mathbb R^t$:book:

!!! warning

    请注意此处，候选聚类中心和上一篇论文不一样，所以得出的近似下界不一样。



Greedy Capture by Chen et al. where $d\in\{L^1,L^2,L^\infty\}$ and $\mathcal M=\mathbb R^t$.

For $d=L^2,\mathcal M=\mathbb R^t$, GC provides $2$-PROP. The approximation is obtained by **Apollonius radius**.

For $L^1,L^\infty$, the approximation ratio can not be better than $1+\sqrt2$.

Universal lower bounds: for $d=L^2$ and $\mathcal M=\mathbb R^t$, no algorithm achieves better than $\frac{2}{\sqrt 3}\approx1.155$-PROP, whereas for $d\in\{L^1,L^\infty\}$ and $\mathcal M=\mathbb R^t$, the lower bound is $1.4$.

Also, this paper consider the case where $\mathcal M$ is the set of nodes of an unweighted graph, and $d$ measures the shortest distance between two nodes on the graph.

- If the graph is a tree, then PROP must exist. And there are an efficient algorithm.
- If the graph is arbitrary, $k\ge n/2$. PROP must exist and can be computed efficiently.
- Open problem: Whether an exact PROP exists for all graphs.

For $d=L^2$ and $\mathcal M=\mathbb R^t$ for $t\ge2$, checking whether a clustering is PROP is NP-hard.

When $t$ is large,even greedy capture is NP-hard. When $t$ is constant, the problem becomes efficiently solvable.

When $t$ is large, using a $PTAS$ as sub-routine of greedy capture, we can efficiently compute $2(1+\epsilon)$-PROP fair clustering for any fixed $\epsilon\gt0$

Problem of generalization: Would a clustering that is PROP w.r.t. samples drawn from $\mathcal N$ remain approximately PROP w.r.t. entire set $\mathcal N$? Chen et al. proved that the answer is yes when $|\mathcal M|$ is finite. Using the VC dimension, the answer is also yes for $\mathcal M=\mathbb R^t$.

# Preliminaries

$\mathcal N$: the set of $n$ data points lie in a metric space $(\mathcal X,d)$, where $d:\mathcal X\times\mathcal X\to\mathbb R$ is a distance function satisfying the triangle inequality.

Consider $\mathcal X=\mathbb R^t$ for $t\in\mathbb N$.

Distance function: $L^2,L^1,L^\infty$.

$\mathcal M$: set of candidate cluster centers. **In this paper** $\mathcal M=\mathcal X$.

Given $k\in\mathbb N$, a $k$-clustering is a set $X\in\mathcal M^k$.

Each $x\in X$ is called the open cluster center.

Cost to agent $i\in\mathcal N$ induced by a cluster center $x\in\mathcal M$ is the distance $d(i,x)$. The cost to agent $i$ should be the minimal distance.
$$
d(i,X)=\min_{x\in X}d(i,x)
$$
Agent $i$ is interested in minimizing the cost.

A set of points $S\subseteq N$ containing at least $\lceil n/k\rceil$, if they can find a new cluster that is better for each of them, it is a violation of fairness (block coalition)

:book:Definition 1. $\rho$-PROP, block coalition. Omitted.

PROP on graphs: given an undirected graph $G=(V,E)$, $\mathcal N\subseteq V,\mathcal M=V$. Distance between $u,v\in V$ is the length of the shortest path.

# Greedy Capture

Detail is omitted here.

$(1+\sqrt2)$-PROP 

This paper gives a refined analysis of Greedy Capture.

:book:Definition 2 (Apollonius radius) Given $\rho\ge1$, the $\rho$-Apollonius radius of a metric $(\mathcal X,d)$ is defined as $A_{\mathcal X,d}(\rho)=\sup_{x,y\in\mathcal X}\Delta(\rho,x,y)/d(x,y)$, where $\Delta(\rho,x,y)$ is the radius of the smallest ball centered at some point in $\mathcal X$ that contains the entire set $\{p\in\mathcal X:\rho\cdot d(p,y)\le d(p,x)\}$.

When $\rho\gt1$, the set is a ball.

:dart:Theorem 3. For any metric $(\mathcal X,d)$ and $\mathcal M=\mathcal X$, greedy capture finds a $\rho$-PROP clustering, where $\rho\ge1$ is the smallest positive number satisfying $A_{\mathcal X,d}(\rho)\cdot\frac{\rho+1}{\rho}\le1$.

!!! warning

    $\mathcal M=\mathcal X$: 候选聚类中心可以是空间上任意一点。



> Proof by triangle inequality. Proof that if $X$ is not $\rho$-PROP, then $A_{\mathcal X,d}(\rho)\cdot\frac{\rho+1}{\rho}\gt1$.

:dart:Theorem 4. For any metric $(\mathcal X,d)$, the $\rho$-Apollonius radius is $A_{\mathcal X,d}(\rho)\le\frac{1}{\rho-1}$. Hence, greedy capture finds a $(1+\sqrt2)$-PROP fair clustering for every metric.

!!! info

    在我看来，本篇论文定义了一个复杂的概念——阿波罗尼乌斯半径，来形式化证明算法结果的下界。

    但是这个阿波罗尼乌斯半径的定义过于复杂了。



Next, the paper shows for $d=L^2$, the $\rho$-Apollonius radius is better, $2$-PROP guarantee.

:dart:Theorem 5. For the metric space $(\mathbb R^2,L^2)$, where $t\in\mathbb N$, the $\rho$-Apollonius radius $A_{\mathbb R^2,L^2}(\rho)\le\frac{\rho}{\rho^2-1}$, and hence, greedy capture finds a $2$-PROP clustering.

> Proof omitted.

Whether the introduction of Apollonius improves approximation bound for other distance metrics. For $L^1,L^\infty$, the answer is unfortunately **NO**. The approximation remains $1+\sqrt 2$.

:dart:Theorem 6. For the metric space $(\mathbb R^t,d)$ where $t\ge2$ and $d\in\{L^1,L^\infty\}$, and $\mathcal M=\mathbb R^t$, there exists an example in which the clustering produced by greedy capture is not $\rho$-PROP for $\rho\lt1+\sqrt2$.

# Universal Lower Bounds

This paper generalizes lower bounds on approximation to PROP that apply to **ALL ALGORITHMS**.

Chen et al. showed that $\rho<2$ is not guaranteed. For $\mathcal N=\mathcal M$, the weaker lower bound $1.5$.

This paper: for the case where $\mathcal M=\mathcal X=\mathbb R^t$ and $d\in\{L^1,L^2,L^\infty\}$.

!!! warning

    再次提醒：$\mathcal M=\mathcal X$: 候选聚类中心可以是空间上任意一点。



- When $t=1$, it's easy to see that there always exist PROP clusterings.
- When $t\ge2$, a lower bound $2/\sqrt3$ for $d=L^2$ and a lower bound $1.4$ for $d\in\{L^1,L^\infty\}$.

:dart:Theorem 7. For the metric space $(\mathbb R^t,L^2)$ where $t\ge2$ and $\mathcal M=\mathbb R^t$, there is an example in which no clustering is $\rho$-PROP for $p\lt2/\sqrt3\approx1.155$.

Closing the gap between lower bound $1.155$ and upper bound $2$ for $L^2$ is an open problem.

:dart:Theorem 8. For the metric space $(\mathbb R^t,d)$, where $t\ge2$ and $d\in\{L^1,L^\infty\}$, and $\mathcal M=\mathbb R^t$, there is an example in which no clustering is $\rho$-PROP for $\rho\lt1.4$.

# Clustering in Graphs

In this section, we consider the special case where the metric space $(\mathcal X,d)$ is induced by an undirected graph $G=(V,E)$.

Consider the case of $\mathcal M=\mathcal X=V$. Distance $d(x,y)$ is the length of the shortest path between $x,y$.

!!! warning

    所有节点都是候选聚类中心，距离定义为最短路长度。



When $G$ is a tree, an exact PROP clustering always exists, and can be computed by an efficient algorithm.

## PROP Clustering for Trees

First root the tree at an arbitrary node $r$ to obtain a rooted tree $(G,r)$.

Denote with $h$ the height of the rooted rooted tree, and with $level(x)$ the height of node $x$ relative to root $r$ with $level(r)=1$.

Let $ST(x)$ denote the sub-tree of node $x$

The algorithm starts from the leaves, opens a center every time it finds a node whose sub-tree contains at least $\lceil n/k\rceil$ nodes, then deletes this sub-tree from the graph. At the end, the cost to each node is still defined using the closest node at which a center is opened by the algorithm.

## Pseudo Code - PROP Clustering for Trees

1. Root the tree $G$ at an arbitrary node $r$.
2. $X\gets\emptyset$
3. $G^d\gets G$.
4. for $\ell=d$ to $1$ do
   1. $G^{\ell-1}=G^\ell$
   2. for every $x\in V$ with $level(x)=l$ and $|ST(x)|\ge\lceil n/k\rceil$ do
      - $X\gets X\cup\{x\}$
      - $G^{\ell-1}\gets G^{\ell-1}\diagdown ST(x)$
5. if $G^0\neq\emptyset$ then $X\gets X\cup\{r\}$
6. return $X$



:dart:Theorem 9. Let $G=(V,E)$ be an undirected tree, $(V,d)$ be the metric induced by $G$, $\mathcal N\subseteq V,\mathcal M=V$, and $k\in\mathbb N$. Then, the algorithm yields a PROP clustering.

> Hard proof by contradiction.

## What about Non-tree

Consider the universal lower bound for $(\mathbb R^2,L^1)$ metric from Theorem 8.

If the graph is dense enough, we can derive same lower bound of $1.4$.

:question:Whether better lower bounds exist is an open problem.

:question:For special case where $\mathcal N=V$, we do not know whether PROP always exists

If $G$ is connected and we want to place a large number of clusters $k\ge n/2$, then PROP always exists. This is because a dominating set of any size $k\ge n/2$ is guaranteed to exist in a graph with $n$ nodes and can be computed efficiently.

Definition of dominating set: A set of nodes is called a *dominating set* if every node in the graph is either in this set or adjacent to a node in this set.

Thus the problem of finding a PROP clustering in general graphs with $\mathcal N=\mathcal M=V$ is trivial when $k\ge n/2$, but remains open when $k\lt n/2$.

# Computational Aspects

Two problems:

1. Checking whether a PROP clustering exits.
2. Implementing the Greedy Capture when $\mathcal M=\mathbb R^t$.

:dart:Theorem 10, Given a finite $\mathcal N$, finite $\mathcal M$, $k\in\mathbb N$, and $d=L^2$, checking whether a PROP clustering exists is NP-hard.

> Polynomial-time reduction from the planar monotone rectilinear 3-SAT problem.
>
> Planar monotone rectilinear 3-SAT problem: Given a 3-SAT formula, each clause $c_j$ consists of only positive or only negative literals, the graph connecting clauses to literals they contain is planar, and this graph has a planar embedding in which each variable $v_i$ is represented by a rectangle on the $x$-axis and each positive/negative clause is represented by a rectangle above/below $x$-axis with 3 vertical lines or legs to its 3 variables.

!!! warning

    ~~什么玩意啊，什么乱七八糟的 NP-hard 问题。欺负我不会证规约是吧。~~



:dart:Theorem 11. Let $t\in\mathbb N$, finite $\mathcal N\subset\mathbb R^t$, and $k\in\mathbb N$ be given as input. Suppose $\mathcal M=\mathbb R^t$ and $d=L^2$. Then, the following hold:

1. The clustering returned by Greedy Capture algorithm cannot be computed in polynomial time unless $P=NP$.
2. If $t$ is constant, then it can be computed in polynomial time.
3. Even if $t$ is not constant, for any constant $\epsilon>0$, there exists a polynomial time algorithm which finds a $(2+\epsilon)$-PROP clustering.

# Learning Fair Clustering by Sampling

VERY DIFFICULT!

Is a clustering that is approximately PROP w.r.t. random samples taken from an underlying population would remain approximately PROP w.r.t. the whole population.

YES!

:book:Definition 12. We say that a $k$-clustering $X$ is $\rho$-PROP fair to $(1+\epsilon)$-deviations w.r.t. $\mathcal N$ if for all $S\subseteq\mathcal N$ with $|S|\ge|\mathcal N|\cdot(1+\epsilon)/k$ and all $y\in\mathcal M$, there exists at least one $i\in S$ s.t. $\rho\cdot d(i,y)\ge d(i,X)$.

:book:Definition 13. Given a set of points $\mathcal N$, a $k$-clustering $X$, and $y\in\mathcal M$, define the binary classifier $h_{X,y}:\mathcal N\to\{0,1\}$ s.t. $h_{X,y}(i)=1$ iff $\rho\cdot d(i,y)\lt d(i,X)$. Define the error of this classifier on a set of points $S\subseteq\mathcal N$ as $err_S(h_{X,y})=(1/|S|)\cdot\sum_{i\in S}h_{X,y}(i)$.

The classifier equals $1$ iff $i$ can be part of a coalition that complains about the unfairness of $X$ by demonstrating $y$ as a location that provides them $\rho$-improvement.

$X$ is $\rho$-PROP to $(1+\epsilon)$-deviations w.r.t. $\mathcal N$ iff $err_\mathcal N(X,y)\le\frac{1+\epsilon}{k}$ for all $y\in\mathcal M$.

Goal: Show that given a large enough random simple $N\subseteq\mathcal N$, if we have clustering $X$ that is $\rho$-PROP w.r.t. $N$, then it is $\rho$-PROP to $(1+\epsilon)$-deviations w.r.t. $\mathcal N$ with high probability. But there is no control over $X,y$. Because of the uniform convergence guarantee.

$|err_N(h_{X,y})-err_\mathcal N(h_{X,y})|$ is bounded for all $X,y$.

:book:Definition 14 (VC Dimension). Let $\mathcal N$ be a set of points. Let $\mathcal H$ be a family of binary classifier over $\mathcal N$. We say that $\mathcal H$ shatters $S\subseteq\mathcal N$ if for every labeling $\ell:S\to\{0,1\}$, there exists a classifier $h\in\mathcal H$ s.t. $h(i)=\ell(i)$ for all $i\in S$. The VC dimension of $\mathcal H$, denoted $dim^{VC}(\mathcal H)$, is the size of the largest $S$ that can be shattered by $\mathcal H$.

:thinking:Proposition 15. Let $\mathcal H$ be a family of binary classifier over a set of points $\mathcal N$. If $N\subseteq N$ is a uniformly random sample with $|N|\ge\Omega\left(\frac{dim^{VC}(\mathcal H)+\ln(1/\delta)}{\epsilon^2}\right)$, then with probability at least $1-\delta$, $|err_N(h)-err_\mathcal N(h)|\le\epsilon$ for all $h\in\mathcal H$.

:dart:Theorem 16. Fix $\epsilon,\delta\gt0,\rho\ge1,k,t\in\mathbb N$, and metric $(\mathcal X,d)$ where $\mathcal X= \mathbb R^t$ and $d=L^2$. Let $\mathcal N$ be a set of points and $\mathcal M= \mathbb R^t$ be the set of candidate centers. Let $N\subseteq\mathcal N$ be sampled uniformly at random with $|N|\ge\Omega\left(\frac{k^2\cdot(tk\ln k+\ln(1/\delta))}{\epsilon^2}\right)$. Then with probability at least $1-\delta$, every $k$-clustering $X\in\mathcal M^k$ that is $\rho$-PROP w.r.t. $N$ is $\rho$-PROP to $(1+\epsilon)$-deviation w.r.t. $\mathcal N$.

# Open Questions

This paper mainly consider $\mathcal M$ is entire metric space.

There are some open questions.

## Bridging Gaps

Can we bridge the lower $(1+\sqrt2)$ and upper bounds on the approximation ratio to PROP.

Conjecture for $L^2$, the lower bound $2/\sqrt3$ may be achievable.

Maybe by Jung's theorem, for $L^2$ distance in $ \mathbb R^2$, any set of points with diameter at most $1$ is contained in a ball of radius at most $1/\sqrt3$. This could be useful in closing the gap for $L^2$.

## On Graphs

Does PROP clustering always exist for all graphs when $\mathcal N=\mathcal M$ is the set of all nodes of the graph?

## Computational Hardness

What is the computational hardness for other distance metrics?

## Other ML Models

Can we adapt this PROP to other machine learning settings such as regression or classification?

# Some Personal Unremarkable Rants

This paper revisit Greedy Capture. But remember $\mathcal M=$ whole metric space, this gives different bounds.

Some dazzling new concepts like "Apollonius radius", "shatter" etc.

Impressive spectacular showmanship on hardness proof.