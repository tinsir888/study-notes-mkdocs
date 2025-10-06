---
title: 随机算法 4 Karger's Min Cut Algorithm
author: tinsir888
date: 2024/2/19
cover: /img/AUra.jpg
katex: true
tags:
  - 笔记
  - 随机算法
categories:
  - AUDatalogi
  - 随机算法
abbrlink: 4a8b4a8
---

Lecturer this time: Ioannis Caragiannis

Lecture notes is based on *Karger's Min Cut Algorithm* by Sanjeev Arora, Sanjoy Dasgupta

Karger's min cut algorithm and its extension. It's simple for finding the *minimum cut* in a graph.

# Clustering via Graph Cuts

The *minimum cut* of an undirected graph $G=(V,E)$ is a partition of the nodes into two groups $V_1,V_2$ (s.t. $V=V_1\cup V_2$ and $V_1\cap V_2=\empty$) so that the number of edges between $V_1$ and $V_2$ is minimized.

Variant of cut problem:

1. Minimum global cut: 将图分成两不连通部分所需的最小割

2. Minimum s-t cut: 分开源点 s 和汇点 t 所需的最小割

   size of minimum s-t cut = value of maximum s-t flow

   Algorithm: Ford-Fulkerson algorithm (compute then remove augmenting paths in a residual network 计算增广路径), Edmonds-Karp algorithm ($O(n|E|^2)$)

一个很自然的计算 minimal global cut 的 deterministic 算法：

1. 选择一个固定源点 s fix a node s

2. 将其他 $n-1$ 个点分别当作汇点 t，使用 EK 算法执行 $n-1$ 次，分别计算 s 到这些 t 的最大流 the global should disconnect node s from any other node t.

3. 返回这些最大流的最小值 return the minimum s-t cut in all these executions

   时间复杂度为 $O(n^2|E|^2)$ It's the algorithm we wanna beat today.
   $$
   O(n^4)\le O(n^2|E|^2)\le O(n^6)
   $$

# Karger's Algorithm

Randomized algorithm for finding the minimum cut:

- Repeat until just two nodes remain:
  - Pick an edge of $G$ at random and collapse its two endpoints into a single node.
- For the two remaining nodes $u_1,u_2$, set $V_1=\{\text{nodes that went into }u_1\}$ and $V_2=\{\text{nodes that went into }u_2\}$.

The algorithm has running time $O(n^2)$

- linear time to update node/edge information after a contraction
- $n-2$ contraction in total.

# Analysis

Some facts help to analyze the algorithm.

## Fact 1

If $degree(u)$ denotes the number of edges touching node $u$, then
$$
\sum_{u\in V}degree(u)=2|E|
$$

> Obviously, each edge contributes twice w.r.t. 2 nodes it connects.

## Fact 2

If there are $n$ nodes, then the average degree of a node is $2|E|\over n$.

> A very straightforward conclusion.
>
> When we randomly pick a node $X$ at random:
> $$
> \mathbb E[degree(X)]=\sum_{u\in V}\Pr(X=u)degree(u)={1\over n}\sum_{u\in V}degree(u)=\frac{2|E|}{n}
> $$

## Fact 3

The size of the minimum cut is at most $2|E|\over n$.

> Consider the partition of $V$ into two pieces, one containing a single node $u$, and the other containing the remaining $n-1$ nodes. The size of this cut is $degree(u)$. Since this is a valid cut, the minimum cut cannot be bigger than this. i.e.,
> $$
> \forall u\in V, mincut\le degree(u)\\
> \Rarr mincut\le\min_{u\in V}(degree(u))\le\text{avg}_{u\in V}(degree(u))=\frac{2|E|}{n}.
> $$

## Fact 4

If an edge is picked at random, the probability that it lies across the minimum cut is at most $2\over n$.

> This is because there are $|E|$ edges to choose from, and at most $2|E|\over n$ of them are in the minimum cut.



By facts above, we can easily analyze Karger's algorithm.

Each time an edge is collapsed, the number of nodes decreases by $1$. Therefore,
$$
\Pr(\text{final cut is the minimum cut})=\Pr(\text{first edge not in mincut})\times\\
\Pr(\text{second edge not in mincut})\times\cdots\\
\ge(1-\frac{2}{n})\times(1-\frac{2}{n-1})\times\cdots\times(1-\frac{2}{4})\times(1-\frac{2}{3})\\
={n-2\over n}\cdot{n-3\over n-1}\cdot{n-4\over n-2}\cdots{2\over4}\cdot{1\over3}\\
={2\over n(n-1)}=\left(\begin{array}{c}n\\2\end{array}\right)^{-1}
$$
Karger's algorithm succeeds with probability $p\ge {2\over n^2}$. Therefore, it should be run $\Omega(n^2)$ times, after which the smallest cut found should be chosen.

Another way to implement Karger's algorithm:

- Assign each edge a random weight
- Run Kruskal's algorithm to get the minimum spanning tree
- Break the largest edge in the tree to get the two clusters.

Why does Kruskal's algorithm work?

>~~Waiting for Iannis to tell me ...~~

## Run Multiple Times to Bound Error Probability

In order to boost the probability of success, we simply run the algorithm $\ell\left(\begin{array}{c}n\\2\end{array}\right)$ times.

The probability that at least one run succeeds is at least
$$
1-\left(1-(\begin{array}{c}n\\2\end{array})^{-1}\right)^{\ell(\begin{array}{c}n\\2\end{array})}\ge 1-e^{-\ell}.
$$
Setting $\ell=c\ln n$, we have error probability $\le{1\over n^c}$.

It's easy to implement Karger's algorithm so that one run takes $O(n^2)$ time. Therefore, by running the algorithm $O(n^2\log n)$ times, we have an $O(n^4\log n)$ time randomized algorithm with error probability $1\over\text{poly}(n)$. ==Still better than EK algorithm for not very sparse graphs.==

## The Number of $mincut$

Theorem: Any graph has at most $n(n-1)\over2$ distinct global cuts of $mincut$.

Recall that we fixed a particular minimum cut and proved the probability $2\over n(n-1)$.

Denote by $C_i$ the event that the $i$-th minimum cut is returned in an execution by the contraction algorithm, $\Pr[C_i]\ge2/n(n-1)$.

## Faster Version

Proposed by Karger and Stein.

### Key idea

In the initial merging, it's very **unlikely** we merged an edge in the $mincut$. Towards the end of the algorithm, our probability of selecting an edge in the $mincut$ grows.

For a fixed minimum cut $\delta(S)$, the probability that this cut survives down to $\ell$ vertices is at least $C_\ell^2/C_n^2$. Thus, for $\ell=n/\sqrt2$ we have probability $\ge{1\over2}$ of succeeding.

> Detail in slides page 91.

Improved algorithm: From a multi-graph $G$, if $G$ has at least $6$ vertices, repeat twice:

1. run the original algorithm down to ${n\over\sqrt2}+1$ vertices.
2. recurse on the resulting graph.

Return the minimum of the cuts found in the two recursive calls.

The choice of $6$ instead of other constant will only affect the running time by a constant factor.
$$
T(n)=2T(n/\sqrt2)+O(n^2)=O(n^2\log n).
$$
Since we succeed down to $n/\sqrt2$ with probability $\ge{1\over2}$, we have the following recurrence for the probability of success, denote by $P(n)$:
$$
P(n)\ge1-\left(1-\frac{1}{2}P(n/\sqrt2+1)\right)^2
$$

> Inner term is probability that the sub-instance preserved $mincut$ AND recursive call found this optimum.
>
> probability of success = 1 - probability of failure.
>
> probability of failure = probability of both subroutines fails
>
> probability of both subroutines fails = square of (probability of a subroutine fails)
>
> probability of a subroutine fails = 1 - 1/2 * one successful recursive call

This solves to $P(n)=\Omega({1\over\log n})$. WHY???

> Set $Q(h)=P(2^{h/2})$
>
> This inequality becomes $Q(h)\ge Q(h-1)-{Q(h-1)^2\over4}$
>
> It suffices to show inductively that $Q(h)\ge{1\over h+1}$
>
> Observe that the RHS of the inequality is increasing in $Q(h-1)$.
>
> By induction hypothesis, $Q(h-1)\ge{1\over h}$, the inequality implies that
> $$
> Q(h)\ge{1\over h}-{(1/h)^2\over4}\ge{1\over h}-{1\over h(h+1)}={1\over h+1}
> $$
> as desired.

Hence, similar to the earlier argument for the original algorithm, with $O(\log^2n)$ runs of the algorithm, the probability of success is $1-{1\over\text{poly}(n)}$.

There fore in $O(n^2\log^3n)$ total time, we can find the minimum cut with probability $1-{1\over\text{poly}(n)}$.
