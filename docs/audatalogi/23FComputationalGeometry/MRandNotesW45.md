---
title: 计算几何 W45 Orthogonal Range Searching
author: tinsir888
date: 2023/11/9
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: 9a687062
---

# Orthogonal Problems

aka 正交问题

input: set $P$ of $n$ points

goal: process $P$ in a DS​

query: axis-aligned rectangle $r$

orthogonal range counting: number of points in $r$

orthogonal range reporting: list of points in $r$

3-sided queries: 3 boundaries

## Rectangle stabbing

aka 穿刺矩形

input: set $P$ of $n$ rectangles

goal: process $P$ in a DS

query: a point $q$

output: list of rectangles containing $q$

## 1D Orthogonal Range Searching

input: set $P$ of $n$ points in 1D

goal: process $P$ in a DS

query: axis-aligned rectangle $r\rightarrow$ an interval

### solution

balanced BST + linked list

$O(n)$ space and $O(\log n)$ time to count, $O(\log n+k)$ time to report

query interval covered by $\log n$ canonical sets 规范集.

可以证明任意一个区间包含的规范集个数是 $\log n$ 数量级的。

>canonical set: subset contained in the subtree of a node of the BST
>
>规范集：BST 中特定节点的子树中包含的元素集。它包括节点子树中的所有元素。
>
>更简单一点的解释就是，规范集是一颗完整的子树。

$v$ highest node that splits the interval

cover the part of the interval to left and right of $v$ with $O(\log n)$ canonical sets

left $w$ child of $v$:

1. outside interval: move right
2. inside interval: move left, add right can. set to cover.

cover the part of the interval to left and right of $v$ with $O(\log n)$ canonical sets

total size of the canonical sets: $O(n\log n)$

## Range Trees

input: set $P$ of $n$ points in 2D

goal: process $P$ in a data structure

query: axis-alligned rectangle $r$

building the DS:

1. a BST $T$ on $P$ ordered by x-coordinates
2. a BST $T(S)$ for every canonical set $S$ of $T$ ordered by y-coordinates

树套树：二叉搜索树套二叉搜索树？

> $T(S)$ is called a secondary DS.
>
> canonical sets of $T(S)$ are called secondary canonical sets.

answering query $r=[x_1,x_2]\times[y_1,y_2]$:

1. cover $[x_1,x_2]$ with $l=O(\log n)$ canonical sets $S_1,\cdots,S_l$ from $T$
2. for each $S_i$, cover $[y_1,y_2]$ with $O(\log n)$ secondary canonical sets.

query time: $O(\log^2n)$ (n is number of secondary canonical sets)

space analysis: $\sum_S|T(S)|=\sum_SO(|S|)=O(n\log n)$

## Range Trees in Higher Dimensions

input: set $P$ of $n$ points

goal: process $P$ in a DS

query: axis-aligned rectangle $r$

assume, we have a DS $\mathcal A_d$ with $S_d(n)$ space and $Q_d(n)$ query time for d-dimensional rectangles
$$
S_d(n)=O(n\log^{d-1}n)\\
Q_d(n)=O(\log^dn)
$$


$RT_{d+1}(P)$:

1. partition $P$ into 2 subsets $P_1,P_2$ of $\lfloor\frac{n}{2}\rfloor$ and $\lceil\frac{n}{2}\rceil$ using a hyperplane $h$ perpendicular 垂直 to the $d+1$ dimension
2. project $P_1$ onto $h$ and store the projected points in a DS $\mathcal A_d(P_1)$
3. project $P_2$ onto $h$ and store the projected points in a DS $A_d(P_2)$
4. recurse on $P_1,P_2$

$$
S_{d+1}(n)=O(2S_d(\frac{n}{2}))+2S_{d+1}(\frac{n}{2})\\
=O(S_d(n))+2S_{d+1}(\frac{n}{2})\\
=O(\log nS_d(n))
$$

Query$_{d+1}(q)$:

1. if $q$ completely to one side of $h$ then recurse on either $P_1$ or $P_2$
2. else
   - let $q=q'\times[l,r]$
   - Query$UL_{d+1}(q'\times(-\infty,r])$ on $P_1$
   - Query$UL_{d+1}(q'\times[l,+\infty)$ on $P_2$

降为：在二分时，能保证子树不会超过这个范围。

Query$UL_{d+1}(q)$:

1. let $q=q'\times(-\infty,r]$
2. if q completely to left of $h$ then recurse on $P_2$
3. else
   - Query$UL_{d+1}(q'\times(-\infty,r])$ on $P_1$
   - Query$_d(q')$ on the projection of $P_2$ on $h$

$$
Q_{d+1}(n)=O(2Q_{d+1}^{UL}(\frac{n}{2}))=O(Q_{d+1}^{UL}(n))\\
Q_{d+1}^{UL}(n)=Q_{d+1}^{UL}(\frac{n}{2})+Q_d(n)\Rightarrow O(\log nQ_d(n))
$$

## Priority Search Tree

input: set $P$ of $n$ points

goal: process $P$ in a DS

query: 3-sided rectangle $[x_1,x_2]\times[y,\infty)$

output: list of points in the query

$PST(P)$:

1. $p\leftarrow$ point in $P$ with highest y-coordinate
2. $P_l\leftarrow\lfloor\frac{n-1}{2}\rfloor$ points of $P-\{p\}$ with smallest x-coordinate
3. $P_r=P-(P_l\cup\{p\})$
4. create a node $v$, sotre $p$ at $v.p$, the x-coordinate of the dividing line at $v.d$.
5. left child of $v\leftarrow PST(P_l)$
6. right child of $v\leftarrow PST(P_r)$
7. return $v$

$O(n)$ space

$3SQuery(v,q)$:

1. if $v.p$ below $q$ then return
2. if $v.p$ inside $q$ then output $v.p$
3. if $q$ is to the left of $v.d$ then $3SQuery(v.l,q)$
4. else if $q$ is to the right of $v.d$ then $3SQuery(v.r,q)$
5. else
   - $3SQuery(v_l,q)$
   
   - $3SQuery(v_r,q)$
   
     in fact, $3S$ there is $2S$.
   
     因为保证了子树的范围不会超

$2SQuery(v,q)$:

1. if $v.p$ below $q$ then return
2. if $v.p$ inside $q$ then output $v.p$
3. if $q$ is to the left of $v.d$ then $2SQuery(v.l,q)$
4. else if $q$ is to the right of $v.d$ then $2SQuery(v.r,q)$
5. else
   - $2SQuery(v_l,q)$
   - $2SQuery(v_r,q)$

> in fact, a 2-sided query + a 1-sided query = 3-sided query

$$
\begin{cases}
T_3(n)=O(1)+T_3(\frac{n}{2})\\
T_3(n)=O(1)+2T_2(\frac{n}{2})
\end{cases}
\\
\begin{cases}
T_2(n)=O(1)+T_2(\frac{n}{2})\\
T_2(n)=O(1)+T_2(\frac{n}{2})+T_1(\frac{n}{2})
\end{cases}
\\
T_1(n)=O(\text{output size})\\
T_2(n)=O(\log n+k)\\
T_3(n)=O(\log n+k)
$$

**$O(n)$ space, $O(\log n+k)$ query time**