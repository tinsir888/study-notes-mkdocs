---
title: 计算几何 W46 More Orthogonal problems
author: tinsir888
date: 2023/11/16
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: 955dd42e
---

# Kd-Trees

## Problem set

given a set $P$ of $n$ points in $\mathbb R^2$

store $P$ in a data structure s.t. given a query rectangle $R$, we can find the points in $R$ efficiently.

## idea

- generalize BST to $\mathbb R^2$
- every node $v$ corresponds to a rectangular region $S_v$; the points in the subtree of $v$ lie in $S_v$.

## Build Kd-Tree

BuildKDTree($P,d$)

1. if $P=\{p\}$ then return a leaf node representing $p$.

2. if $d$ is even then

   partition $P$ into $P_1,P_2$ using a vertical line through the point $v$ with median x-coordinate

3. else

   partition $P$ into $P_1,P_2$ using a horizontal line through the point $v$ with median y-coordinate

4. $l\leftarrow$ BuildKDTree($P_1,d+1$)

5. $r\leftarrow$ BuildKDTree($P_2,d+1$)

6. return a tree with root $v$ and left subtree $l$ and right subtree $r$.

### Analysis

running time: $O(n\log n)$

size: $O(n)$

## Search in Kd-Tree

SearchKDTree($v,R$)

1. if $v$ is a leaf then report the point $p$ stored at $v$ if $p\in R$.

2. else 

   ​	if $S_{lc(v)}\subseteq R$ then ReportSubstree($lc(v)$)

   ​	else if $S_{lc(v)}$ intersects $R$ then SearchKDTree($lc(v),R$)

   ​	if $S_{rc(v)}\subseteq R$ then ReportSubstree($rc(v)$)

   ​	else if $S_{rc(v)}$ intersects $R$ then SearchKDTree($rc(v),R$)

### Analysis

query time is proportional to number of regions in $S_v$ intersected by $R$.

rectangles visited by ReportSubtree produce output, so their cost $\le k$

We bound number of regions $Q(n)$ intersected by a vertical line:

assume $v$ corresponds to a vertical splitting time, $q$ in one region of $lc(v)$ and $rc(v)$, this suggests $Q(n)=1+Q(\frac{n}{2})$
$$
Q(n)=\begin{cases}
O(1),\text{if }n=1\\
2+2Q(\frac{n}{4}),\text{otherwise}
\end{cases}
$$
4 regions after two steps. any vertical or horizontal lines, intersects 2 regions
$$
Qt(n)=O(\sqrt n)
$$

## Theorem

a Kd-Tree uses $O(n)$ space, can be built in $O(n\log n)$ time, and can report all points in a query rectangle $R$ in $O(\sqrt n+k)$ time.

# Windowing Queries

## Problem set

given a set $\mathcal S$ of $n$ disjoint line segments in the plane.

store $\mathcal S$ in data structure s.t. given a query rectangle $R$, we can find the segments in $\mathcal S$ intersecting $R$ efficiently.

The segments that intersect $R$

1. have an endpoint in $R$

   find them using a range query with $R$ on the set of end points

2. intersect the boundary of $R$



**先考虑一种退化的情况：**what if the disjoint lines are orthogonal?

store $\mathcal S$ in a data structure s.t. given a vertical query segment $q$, we can find the segments in $\mathcal S$ interesting $q$ efficiently.

## Interval Stabbing Queries

问题：一些平行的线段和一条**直线**相交的查询

given a set $\mathcal S$ of $n$ intervals in $\mathbb R^1$

store $\mathcal S$ in a data structure s.t. given a query value $q$, we can find the intervals in $\mathcal S$ intersecting $q$ efficiently.

we store $\mathcal S$ in a segment tree (aka interval tree) $\mathcal T$

线段树，太典了

$\mathcal T$ is balanced BST on the end points

the root of the tree $v$ stores the intervals $l(v)$ that contain $v$.

the left subtree $l$ of $v$ stores the intervals that lie completely left of $v$.

the right subtree $r$ of $v$ stores the intervals that lie completely right of $v$.

store these intervals twice:

1. sorted on increasing left endpoint
2. sorted on decreasing right end point

### Pseudo code

Query$(q,T)$

- if $q$ is left of $v$ then

  report intervals from $l(v)$ using the list of left-end points, stop at the first interval right of $q$.

  Query$(q,l)$

- else if $q$ is right of $v$ then

  report intervals from $r(v)$ using the list of right-end points, stop at the first interval left of $q$.

  Query$(q,r)$

### Analysis

space usage: $O(n)$

query time: $O(\log n+k)$, $k$ is numbers of intervals reported

preprocessing time: $O(n\log n)$

## Segment stabbing queries

问题：一些平行的线段和一条**线段**相交的查询

相当于两个维度上的覆盖问题

space usage: $O(n)$

query time: $O(\log^2 n+k)$, $k$ is numbers of intervals reported

preprocessing time: $O(n\log n)$

## Unparalleled stabbing queries

再进一步，如果给出的线段不平行呢？（问题进一步变成：给出一些不平行的线段和一条直线，求和这条直线相交的线段个数）

那么线段树 + 优先搜索树方法就行不通了。

split the problem into elementary intervals in which a vertical line intersects the same segments

storing all segments segments in all elementary intervals uses $\Theta(n^2)$ space.

再将这些 elementary segments 投影到一个方向上，变成平行的。Project the segments onto the x-axis, yielding intervals. we build a different data structure for interval stabbing.

问题又转成了 interval stabbing

Store the elementary intervals as leaves in a balanced BST $\mathcal T$.

Every node $v$ corresponds to an interval $l_v$, which is the union of the elementary intervals stored in its subtree.

store a canonical subset $S(v)\subseteq S$ of intervals s.t. $s\in S$ if and only if $l_v\subseteq s$ but $parent(v)_l\not\subseteq s$.

这里有点 tricky，为了尽量少的节点存下这些 element intervals，尽量往上面的祖先节点存，aka 节点存储的是包含 element intervals 的最大正规集 maximal canonical set。这样复杂度就是 $\log n$ 级别的了（对于每根线段，有 $O(\log n)$ 个线段树节点存储）

$\mathcal T$ is a segment tree.

query: find all nodes $v$ s.t. $q\in l_v$, and for each such node report all intervals in $S(v)$.

query time: $O(\log n+k)$ where $k$ is the output size.

space: every interval is stored $O(\log n)$ times, at most twice per level. $O(n\log n)$ in total.

how do we build $\mathcal T$?

> build a BST on the elementary intervals, insert the intervals in $s\in S$ one by one.
>
> to insert $s$ we visit at most 4 nodes per level.

preprocessing time: $O(n\log n)$

把查询的直线换成线段呢？

space $O(n\log n)$

query $O(\log^2n+k)$

preprocessing time $O(n\log n)$

## Come back to window queries

the segment that intersect $R$

1. have an endpoint in $R$

   find them using a range query with $R$ on the set of end points

   ($O(\log^2n+k)$ query, $O(n\log n)$ space)

2. intersect the boundary of $R$

   find them using a segment tree

   ($O(\log^2n+k)$ query, $n\log n$ space)

## Theorem

we can solve windowing queries in $O(\log^2n+k)$ time, using $O(n\log n)$ space after $O(n\log n)$ preprocessing time.

这个 $\log^2n$ 可以优化成 $\log n$？？？