---
title: '计算几何 W44 Arrangements and Duality, Zone Theorem'
author: tinsir888
date: 2023/11/2
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: 9eac544
---

# Duality

there is a 1-to-1 mapping between points and non-vertical lines

map every point p to a line: $p^*:y=p_xx-p_y$, $p^{**}:y=p_xx+p_y$

every line $l:y=ax+b$ to a point $l^*=(a,-b)$.

claim: if $p$ is on(below/above) $l\Leftrightarrow l^*$ is on(below/above) $p^*$.


> Proof:
> $$
> p(p_x,p_y),l:y=ax+b\\
> \text{p is on l}\Rightarrow p_y=ap_x+b\\
> l^*(a,-b),p^*:y=p_xx-p_y\\
> \text{we plug }l^*\text{ into }p^*:p_xa-(-b)=ap_x+b=p_y\\
> Q.E.D.
> $$

observation: the duality transform has the following properties:

- it is incidence preserving: $q\in l\Lrarr l^*\in q^*$.
- order preserving: $p$ below $l\Lrarr l^*$ below $p^*$.

## Dual point for line segment

如何找线段所在直线的对偶点？

> 找到线段的两个端点 $P,Q$，得到两个点的对偶线 $P^*,Q^*$, 这两条对偶线的交点就是原线段的对偶点。

有趣的性质：一条直线上所有的点的对偶线，都必经过这条直线的对偶点。

对于一条线段上的所有点，可以把它们表示成两端点对应的两条对偶线的“夹角”（夹住的所有对偶线的集合）。

如果两条线段有交点，说明两条线对应的两个夹角能“相互看见”。

## Projective Plane

given any 2 points, $P,Q$, we can draw a line $l$ through them.

given any 2 line, $l_1,l_2$, we can find a point of intersection $X$. (if parallel, we make it intersect on infinity point $X$)

在投影平面 projective plane 上，只有一个 point at infinity，所以一条直线只和无穷远处的点有一个交点。

# Arrangements of lines

$n$ lines in the plane.

$\mathcal A$ is the subdivision of $\mathbb R^2$ induced by $L$. $\mathcal A$ is the arrangement induced by $L$. $\mathcal A$ has complexity $O(n^2)$.

$n$ lines, at most $\frac{n(n-1)}{2}$ intersect points, at most $1+\frac{n(n+1)}{2}$ faces.



vertex: intersection of $d$ hyperplanes 0-dimensional

edge: intersection of d-1 hyperplanes 1-dimensional

...

j-face: intersection of d-j hyperplanes j-dimensional

...

d-1-face or facet: on 1 hyperplane d-1-dimensional

cell: on 0 hyperplane d-dimensional



$\Phi_d(n)$: number of cells in an arrangement of n hyperplanes in d-dimensional

Claim:

$\Phi_2(n)=\frac{n(n+1)}{2}+1$

$\Phi_d(n)=\sum_{i=0}^dC_n^i$

## Zone Theorem

the complexity of the zone $\mathcal Z$ of a line $l$ in an arrangement of $m$ lines in $\mathbb R^2$ is $O(m)$.

2D Zone Theorem: complexity of cells crossed by a line is $O(n)$.

> 证明：对于一条直线，会和一些线有交点，经过了一些面，这些面的左交点对应的线 left bound edge 和右交点对应的线 right bound edge 数量之和最多有 2n 条。

complexity of cells crossed by a hyperplane is $O(n^{d-1})$.



How to compute DCEL in bounding box which contains all intersect point?

## An incremental algorithm

1. compute the bounding box $B$ and initialize an empty DCEL for $\mathcal A$.

2. for i=1 to n do

   find the face $f$ incident to the left boundary of $B$ containing $l_i$.

   while $f$ is not the unbounded face do

   - split the current face $f$ into $f_1,f_2$
   - walk to the next face intersected by $l_i$.

how to split a face? walk around each face.

running time: 
$$
O(n^2)+\sum_{i=1}^nO(\text{time to insert }l_i)\\
=O(n^2)+\sum_{i=1}^nO(\text{complexity of the zone }Z_i\text{ of }l_i)\\
=O(n^2)+\sum_{i=1}^nO(i)=O(n^2)
$$