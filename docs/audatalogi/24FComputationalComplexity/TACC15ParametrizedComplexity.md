---
title: 算法和计算复杂度 15 Fine-grained and Parameterized Complexity
author: tinsir888
date: 2024/11/14
cover: /img/AUtacc.jpg
katex: true
tags:
  - 笔记
  - 算法
  - 计算复杂度
categories:
  - AUDatalogi
  - 算法和计算复杂度
abbrlink: 3bc55ba9
---

# Fine-grained Complexity

Vassilevska Williams, "On some fine-grained questions in algorithms and complexity" (original paper), pages 1-9.

Mimic $NP$-completeness,

## 3 Hypothesis

### SETH

Recap **Strong Exponential Time Hypothesis** (SETH). For every $\epsilon\gt0$ there exists an integer $k\ge3$ s.t. $CNF-SAT$ on formulas with clause size at most $k$ and $n$ variables can not be solved in $O(2^{(1-\epsilon)n})$ time even by a randomized algorithm.

### 3-SUM

**3-SUM Hypothesis**. $3-SUM$ on integers in $\{-n^4,\cdots,n^4\}$ can not be solved in $O(n^{2-\epsilon})$ time for any $\epsilon\gt0$ by a randomized algorithm.

$3-SUM$ Problem: Given a set $S$ of $n$ integers from $\{-n^c,\cdots,n^c\}$ for some constant $c$, determine whether there are $x,y,z\in S$ s.t. $x+y+z=0$. By a standard hashing trick, assume that $c\le3+\delta$ for any $\delta\gt0$.

SOTA for $3-SUM$: an algorithm in $n^2(\log\log n)^{O(1)}/\log^2n$ by Chan et al.

### APSP

**All-Pairs Shortest Paths** (APSP) problem: Given an $n$ node graph $G=(V,E)$, and integer edge weights $w:E\to\{-M,\cdots,M\}$ for some $M=n^{O(1)}$, compute for every $u,v\in V$, the shortest path distance $d(u,v)$ in $G$ from $u$ to $v$. Assume that $G$ does not contain negative weight cycles.

~~求所有点的最短路径，有经典弗洛伊德算法啊。~~

Floyd-Warshall algorithm can solve APSP in $O(n^3)$. One can run Dijkstra's algorithm from every vertex using some tricks, SOTA is $n^3/\exp(\sqrt{\log n})$.

**APSP Hypothesis**. No randomized algorithm can solve APSP in $O(n^{3-\epsilon})$ time for $\epsilon\gt0$ on $n$ node graphs with edge weights in $\{-n^c,\cdots,n^c\}$ and no negative cycles for large enough $c$.

## Fine-grained Reductions

Consider problem $A$ with textbook runtime $a(n)$ and problem $B$ with text book runtime $b(n)$. Given a supposed $O(b(n)^{1-\epsilon})$ time algorithm for $B$ for $\epsilon\gt0$, we would like to compose it with another algorithm that transforms instances of $A$ into instances of $B$, to obtain an algorithm for $A$ running in time $O(a(n)^{1-\epsilon'})$ time for $\epsilon'\gt0$ being a function of $\epsilon$.

:book:Definition of **fine-grained reduction**. Assume that $A$ and $B$ are computational problems and $a(n)$ and $b(n)$ are their *conjectured* running time lower bounds, respectively. We say $A$ $(a,b)$-reduces to $B$ ($A\le_{a,b}B$), if for every $\epsilon\gt0$, there exists $\delta\gt0$, and an algorithm $R$ for $A$ that runs in time $(a(n))^{1-\delta}$ on inputs of length $n$, making $q$ calls to an oracle for $B$ with query lengths $n_1,\cdots,n_q$, where
$$
\sum_{i=1}^q(b(n_i))^{1-\epsilon}\le(a(n))^{1-\delta}.
$$
If $A\le_{a,b}B$ and $B\le_{a,b}A$, we say that $A$ and $B$ are fine-grained equivalent, $A\equiv_{a,b}B$.

!!! info

    若存在一个能在**指数位置上**打破 $A$ 问题猜想时间复杂度下界的算法 $R$，并且该算法包含若干次调用**在指数位置上**打破 $B$ 问题猜想复杂度下界的算法，则称 $A$ 能细粒度归约到 $B$。



~~被绕晕了没有？~~

The definition implies that if $A\le_{a,b}B$ and $B$ has an algorithm with running time $O(b(n))^{1-\epsilon}$, then $A$ can be solved by replacing the oracle calls by corresponding runs of the algorithm, obtaining a runtime of $O(a(n)^{1-\delta})$ for $A$ for some $\delta\gt0$.

If $A\equiv_{a,b}B$, then we can not be able to improve $a(n)$ and $b(n)$ for $A$ and $B$ respectively, is the same.

## Hardness from SETH

**Orthogonal Vectors** (OV) problem requires quadratic time under SETH.

OV problem: Let $d=\omega(\log n)$; given two sets $A,B\subseteq\{0,1\}^d$ with $|A|=|B|=n$, determine whether there exist $a\in A,b\in B$ s.t. $a\cdot b=0$ where $a\cdot b=\sum_{i=1}^da[i]]\cdot b[i]$.

$k$-OV problem for constant $k\ge2$: Let $d=\omega(\log n)$; given $k$ sets $A_1,\cdots,A_k\subseteq\{0,1\}^d$ with $|A_i|=n$ for all $i$, determine whether there exist $a_1\in A_1,\cdots,a_k\in A_k$ s.t. $a_1\cdot...\cdot a_k=0$ where $a_1\cdot...\cdot a_k=\sum_{i=1}^d\prod_{j=1}^ka_j[i]$.

$k$-OV can be solved in $O(n^kd)$ time by exhaustive search, for any $k\ge2$. SOTA: $n^{k-1/\Theta(\log(d/\log n))}$. **It seems that** $n^{k-o(1)}$ **is necessary**.

$k$-OV Hypothesis: No randomized algorithm can solve $k$-OV on instance of size $n$ in $n^{k-\epsilon}d^{O(1)}$ time for constant $\epsilon\gt0$.

### kOV to CNF-SAT

:dart:Theorem of reduction from $k$-OV to $CNF-SAT$: If $k$-OV on sets with $N$ vectors from $\{0,1\}^m$ can be solved in $N^{k-\epsilon}m^{O(1)}$ time for any $\epsilon\gt0$, then $CNT-SAT$ on $n$ variables and $m$ clauses can be solved in $2^{(1-\epsilon')n}m^{O(1)}$ time for some $\epsilon'\gt0$ and SETH is false.

### kOV to Diameter probelm

$k$-OV can also do fine-grained reduction to **diameter problem**.

~~图的直径：图中所有点对中的最长距离~~

:dart:Theorem of reduction from $k$-OV to Diameter problem. If one can distinguish between diameter $2$ and $3$ in an undirected unweighted graph with $O(N)$ nodes and edges in $O(N^{2-\epsilon})$ time for some $\epsilon\gt0$, then $2$-OV on two sets of $n$ vectors in $d$ dimensions can be solved in $n^{2-\epsilon}d^{O(1)}$ time and SETH is false.

# Parameterized Complexity

Proposed by Rod Downey, Victoria University, New Zealand

Parameterized complexity:

1. The input to the problem.
2. The aspects of the input that constitute the parameter.
3. The question.

A **parameterized language** is $L\subseteq\Sigma^*\times\Sigma^*$ where we refer to the second coordinate as **parameter**.

For simplicity, we can think of $L\subseteq\Sigma^*\times \N$.

:book:Definition of FPT. A parameterized language $L$ is **fixed parameter tractable** (FPT), iff there is a computable function $f$, a constant $c$, and a deterministic algorithm $M$ s.t.
$$
\forall x,k:\langle x,k\rangle\in L\Leftrightarrow M(x,k)\text{ accepts},
$$
and the running time of $M(x,k)$ is $\le f(k)|x|^c$.

It's not hard to see that $f(k)|x|^c=O(|x|^c)+f(k)$ for some computable $f$.

Example: a parameterized version of Vertex Cover problem:

- Instance: A graph $G=(V,E)$.
- Parameter: A positive integer $k$.
- Question: Does $G$ have a vertex cover of size $\le k$?

There is an algorithm running in time $O(1.2738^k+kn)$.

So in this problem, we have $f(k)=1.2738^k$. Introduce $O^*$ notation which ignores $f(k)$ be it additive or multiplicative and is only concerned with the exponential part. Thus the algorithm is $O^*(n^1)$.

## Bounded Search Trees

Some optimized methods.

## Kernelization

:book:Definition of Kernelization. Let $L\subseteq\Sigma^*\times\Sigma^*$ be a parameterized language. A reduction to a problem kernel, or kernelization, comprises replacing an instance $(I,k)$ by a reduced instance $(I',k')$, called a problem kernel, such that

1. $k'\le k$
2. $|I'|\le g(k)$, for some function $g$ depending only on $k$.
3. $(I,k)\in L\Leftrightarrow(I',k')\in L$.

The reduction from $(I,k)$ to $(I',k')$ must be computable in time polynomial in $|I|+|k|$.

Reduction *rule*s for a kernel for Vertex Cover:

1. Remove all isolated vertices.
2. For any degree one vertex $v$, add its single neighbor $u$ to the solution set and remove $u$ and all of its incident edges from the graph.
3. If there is a vertex $v$ of degree at least $k+1$, add $v$ to the solution set and remove $v$ and all of its neighbors.

After applying these rules, we get to a graph $(G',k')$, where no vertex in the reduced graph has degree greater than $k'\le k$, or less than two. Then simple combinatorics shows that if such a reduced graph has a size $k$ vertex cover, its must have size $\le k^2$. This is the size $k^2$ kernelization.



## Lower bounds for some W\[1\]-hard Problems

Chen et.al.: "Tight lower bounds for certain parameterized NP-hard problems" (original paper), Section 4.

The class $SNP$ contains many $NP$-hard problems including $q$-SAT, $q$-Colorability, $q$-Set Cover, Vertex Cover, and Independent Set.

It is commonly believed that it is unlikely that all problems in $SNP$ are solvable in sub-exponential time.

:dart:Theorem (Vertex Cover reduction has lower bound). Given an instance $(G,k)$ of Vertex Cover, there is a polynomial time algorithm which either reports that $G$ doesn't have a vertex cover of size $k$, or produces a subgraph $G'$ of $G$ with at most $2k'$ vertices, where $k'\le k$, such that $G$ has a vertex cover of size $k$ iff $G'$ has a vertex cover of size $k'$.

:dart:Theorem of Independent set poly-time solvable hypothesis. If the parameterized Independent Set problem can be solved in time $n^{O(k)}$, where $n$ is the number of vertices in the graph, then all problems in $SNP$ can be solved in sub-exponential time.

:dart:Theorem of clique poly-time solvable hypothesis. If Clique, Weight $q$-SAT, Dominating Set, Dominating Clique, or Graph $k$-Cut can be solved in time $n^{O(k)}$ then all the problems in $SNP$ can be solved in sub-exponential time, where $n$ is the size of the universal set from which the $k$ elements are to be selected.