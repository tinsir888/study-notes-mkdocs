---
title: 计算几何 W43 Point Location
author: tinsir888
date: 2023/10/26
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: bd9d2a2b
---

# Planar Point Location using Persistency

使用持久化数据结构进行平面点定位（在线查询）

Given a planar subdivision with $n$ segments, pre-process it for point location queries.

给定一个具有 n 段的平面细分，对其进行预处理以进行点位置查询。

*$n$ disjoint line segments that divide the plane into some regions*

query: given a point, ask which region it lies in.

Goal:

$O(n)$ space

$O(\log n)$ query time

$O(n\log n)$ pre-processing



Recall the solution for line segment intersection:

- sweep a vertical line $l$ over the plane
- maintain the segments that intersect $l$ in the status structure.

- at a query-event: query the status structure. *we find the segment above the query.*

Idea: remember/store the status structure for later use

naive: $O(n^2)$ storage

## Persistent Data Structures

Regular data structure are ephemeral 暂时的: updates destroy old version of the data structure.

A persistent data structure also allows access to previous versions of the data structure.

- partial persistence:

  allow **queries** in previous versions

- full persistence:

  allow **queries and updates** in previous versions

Theorem: Any pointer machine data structure with $O(1)$ pointers to any node can be made fully persistent with $O(1)$ amortized multiplicative time and space overhead per update.  

任何具有 $O(1)$ 指向任何节点的指针的指针机器数据结构都可以完全持久化，每次更新的摊销乘法时间和空间开销为 $O(1)$。

## Approach

- Sweep a vertical line $l$ over the plane

- maintain the segments that intersect $l$ in the status structure:

  the status structure: a partially persistent red-black tree 部分持久的红黑树存储状态

At every event we update the status structure

- maintain the lines (i.e. X-coordinates) when the status line has changed in a BST.

Fact: a red-black tree can be rebalanced using $O(1)$ rotations.

Each such update takes $O(\log n)$ time, and thus creates at most $O(1)$ changes in the status structure.

Theorem: Let $\mathcal S$ be a planar subdivision with $n$ edges. Using $O(n\log n)$ pre-processing, we can store $\mathcal S$ in a data structure of size $O(n)$ so that we can answer point location queries in $O(\log n)$ time.

# Planar Point Location using a Trapezoidal Decomposition

使用梯形分解的平面点定位

given a planar subdivision with $n$ segments, pre-process it for point location queries.

Goal:

$O(n)$ space

$O(\log n)$ query time

the partition into slabs creates a refined subdivision, in which planar point location is easy.

板的分区创建了精细的细分，其中平面点定位很容易。

Question: is there a refined subdivision of size $O(n)$ in which planar point location is easy?

问题：是否存在大小为 O(n) 的精细细分，其中平面点定位很容易？

Observation: All faces are trapezoids 梯形, they are bounded by 2 vertical line segments and 2 input segments. 它们由两个垂直线段和两个输入线段界定

The subdivision is a trapezoidal decomposition 平面细分就是梯形分解

Add 2 vertical extension segments incident to every vertex, stop when they hit another segment.

添加与每个顶点相关的两个垂直延伸线段，当它们碰到另一个线段时停止。

Observation: the resulting subdivision is a trapezoidal decomposition and has size $O(n)$

## The Search Structure

we build the trapezoidal decomposition using a randomized incremental construction

As a side effect, the algorithm builds a search structure $\mathcal D$.

A DAG in which

- trapezoids stored in the leaves only once.
- internal nodes are line segments
- could be store multiple times

每个线段有 left 区域、above 区域、below 区域、right 区域。把线段看成一个父节点，这些区域可以看成子节点。

## Algorithm

procedure TRAPEZOIDALMAP(S)
1. Compute a bounding box, and initialize the decomposition $\mathcal T$ and a search structure $\mathcal D$.
2. Compute a random permutation $s_1,\cdots,s_n$ of the segments in $\mathcal S$.

Invariant: $\mathcal D$ is a search structure for $\mathcal T$.

3. for $i\leftarrow 1$ to $n$ do

   find the trapezoids $\Delta_1,\cdots,\Delta_k$ intersected by $s_i$.

   replace $\Delta_1,\cdots,\Delta_k$ by new trapezoids.

   update $\mathcal D$: remove the leaves for $\Delta_1,\cdots,\Delta_k$ and insert internal nodes containing $s_i$.

   place pointers from nodes containing $s_i$ to the new trapezoids and update DCEL



Running time $O(k)$ in the for loop.

$O(n^2)$ worst-case running time

$O(n^2)$ worst-case space

$O(n^2)$ worst-case query time

## Analysis

we bound the expected pre-processing time, query time, and size of the data structure

expected query time for a given query point $q$ = average query time over all $n!$ permutations of $s_1,\cdots,s_n$

Fix a query point $q$, consider the path $\mathcal P$ in $\mathcal D$ to $\Delta^*$ containing $q$.

$\mathbb E[$query time for $q]=\mathbb E[$length of $P]=\mathbb E[\sum_iX_i]=\sum_i\mathbb E[X_i]$.

What is the probability that $s_i$ is top($\Delta$)?

> We use backwards analysis:
>
> suppose we remove $s_i$, what is the probability that top($\Delta$) disappears?
>
> $\frac{1}{i}$
>
> What is the probability that $s_i$ is bottom($\Delta$)?
>
> $\frac{1}{i}$
>
> What is the probability that by removing $s_i$ we remove rightp($\Delta$)?
>
> $\frac{1}{i}$
>
> $P_i$=probability that a new node created when inserting $s_i$
>
> =$s_i$ is top, is bottom; leftp is an endpoint of $s_i$, or rightp is an endpoint of $s_i$
>
> $\le\frac{4}{i}$

$\mathbb E[$query time for $q]\le4\sum_i\frac{1}{i}=4H_n$

$H_n$ is the n-th harmonic number.



Similarly $\mathbb E[$size $\mathcal D]=O(n+\sum_i\mathbb E[k_i])=O(n)$

$k_i$ is number of trapezoids created when inserting $s_i$

Again, we use backwards analysis:

> $k_i$ is trapezoids removed from $\mathcal T_i$ when deleting $s_i$
>
> $\mathbb E[k_i]=\frac{1}{i}\sum_{s\in\{s_1,\cdots,s_i\}}\sum_{\Delta\in\mathcal T_i}\delta(\Delta,s)=\frac{O(i)}{i}=O(1)$
>
> $\delta(\Delta,s)=\begin{cases}1\ \text{if there are at most 4 segments that can cause } \Delta \text{ to disappear.}\\ \Delta \text{ otherwise}\end{cases}$
>
> So $\sum_{s\in\{s_1,\cdots,s_i\}}\sum_{\Delta\in\mathcal T_i}\delta(s,\Delta)\le4|\mathcal T_i|=O(i)$

$$
\mathbb E[\text{preprocessing time}]=O(1)+\sum_i\mathbb E[\text{time to insert }s_i].
$$

$$
=O(1)+\sum_i(O(\log i)+O(\mathbb E[k_i]))
$$

$$
=O(1)+\sum_i(O(\log n)+O(1))
$$

$$
=O(n\log n)
$$

$k_i$ is number of trapezoids created when inserting $s_i$.

## Results

Given a planar subdivision with n segments, pre-process it for point location queries.

Result:

- expected preprocessing: $O(n \log n)$
- expected space: $O(n)$
- expected query time: $O(\log n)$
- worst case query time: $O(n)$
- worst case space: $O(n^2)$

