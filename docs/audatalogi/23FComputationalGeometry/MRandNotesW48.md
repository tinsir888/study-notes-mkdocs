---
title: 计算几何 W48 Robot Motion Planning and Visibility (新课完结)
author: tinsir888
date: 2023/11/30
cover: /img/AUcg.png
katex: true
tags:
  - 笔记
  - 计算几何
categories:
  - AUDatalogi
  - 计算几何
abbrlink: b3ed4a5b
---

# Robot Motion Planning

Given a set of obstacles $P_1,\cdots,P_k$, and a polygonal robot $R$ with starting configuration $s$ and target configuration $t$. Find a path for the robot from $s$ to $t$ if possible.

机器人路径规划问题，首先应该先解决：存不存在这样一条路径的问题。

## Reference point

define a **reference point** $p$ of the robot and define position of the vertices of $R$ w.r.t. $p$.

Suppose our robot can move by translating

$R(x,y)$: position of the robot when the reference point is at $(x,y)$.

Suppose our robot can move by translating:

$R(x,y,\alpha)$: position of the robot when the reference point is at $(x,y)$ and the robot has orientation $\alpha$.



We represent the robot by a point in the parameter space (configuration space) $\mathbb R^2\times[0,360)$



先只考虑机器人只能平移，不能旋转。

**partition** the configuration space in *free space* and *forbidden space*

$p$ in forbidden space $\Leftrightarrow R(p)$ intersects an obstacle.

$R$ can move from $s$ to $t\Leftrightarrow$ there is a path from $s$ to $t$ in the free space.

对于机器人上的一个参考点，在图上都有一个 free space 图。如果 free space 联通起始点，那么就存在一个路径使得机器人可以从起点移动到终点。

if $R$ is a point then configuration space = work space.

## Finding a path in the Configuration Space

如果存在路径，那么就找出这样一条路径

given a configuration space $P_1,\cdots,P_k$ of complexity $n$ find a path from $s$ to $t$ if possible.

idea: construct a trapezoidal decomposition of the free space.

思路：将 free space 用竖线进行梯形分解。

construct a road map $G=(V,E)$

$V$: set of all **centers of all trapezoids** $\Delta$ and all **midpoints of all vertical segments** $s$.

梯形的中位线中点+所有梯形顶边和底边线段的中点

$(\Delta,s)\in E\Leftrightarrow s$ is a vertical segment bounding $\Delta$.

add $s$ and $t$ to $G$ and connect them to the trapezoids containing them.

Run a BFS in the road map to find a path from $s$ to $t$.

### Theorem

we can compute a path from $s$ to $t$ in $\mathcal O(n\log n)$  expected time.

如何搞定 free space 的计算问题呢？使用闵可夫斯基和解决！

# Minkowski Sums

Let $P$ and $R$ be two sets in $\mathbb R^2$

$P\oplus R=\{p+r|p\in R\land r\in R\}$ is the Minkowski sum of $P$ and $R$.

where $p+r=\{p_x+r_x,p_y+r_y\}$

observation: let $Q=P\oplus R$, an extreme point on $Q$ in direction $d$ is the sum of extreme points in direction $d$ on $P$ and $R$.

## Theorem

1. Let $P$ and $R$ be convex polygons with $n$ and $m$ edges, then $Q=P\oplus R$ is a convex polygon and has at most $m+n$ edges.
2. Let $P$ and $R$ be convex polygons with $n$ and $m$ edges, then $Q=P\oplus R$ can e computed in $\mathcal O(m+n)$ time.



Question: What if $P$ and $R$ are not convex?

## Pseudo-discs

a pair $P$ and $R$ are pseudo-discs

the boundaries of $P$ and $R$ intersect in at most two points.

i.e.,
$$
\partial P\cap int(R)\text{ is connected and}\\
\partial R\cap int(P)\text{ is connected.}
$$
observation: A pair of polygonal pseudo-discs $P$ and $R$ defines at most two boundary crossing

### Theorem

**Let $\mathcal S$ be a collection of convex polygonal pseudo-discs with a total of $n$ edges. the total complexity of their union is at most $2n$.**

- pseudo-disk vertices
- intersection points

Charge every pseudo-disk vertex to itself.

For an intersection point $q=e\cap f$, by observation: either

- $e$ has no other boundary crossing with $\partial R$.
- $f$ has no other boundary crossing with $\partial P$, or both

Let $e$ be the edge without other boundary crossings, and let $v$ be the endpoint of $e$ in $R$.

Charge $q$ to $v$.

Claim: Every vertex $v$ (of a pseudo-disk $P$) is charged at most twice.

- Case 1: $v$ not in any other pseudo-disk ∴ $v$ is a green vertex and is charged only by itself.

- Case 2: $v,v\in R$ lies in the interior of the union.

  Traverse $e=\overline{vw}$. There is only one point $q$ on the boundary of the union on $e$ that charges to $v$.

  Same for the other edge $\overline{vu}$

- Case 3: $v,v\in\partial R$ lies on the boundary of the union. As in case 2 $v$ is charged only by its incident edges $e$ and $f$ and by $v$ itself

  $v$ can not be charged by both its incident edges at the same time.

**Let $\mathcal S$ be a collection of pseudo-discs with a total of $n$ edges. The total complexity of their union is at most $O(n)$.**

Observation: Let $P$ and $R$ be convex polygons with disjoint interiors, let $d_1$ and $d_2$ be directions where $P$ is more extreme than $R$.

Then either

- $P$ is more extreme in all directions in $[d_1,d_2]$, or
- $P$ is more extreme in all directions in $[d_2,d_1]$.

**Let $P$ and $Q$ be two convex polygons with disjoint interiors, and let $R$ be another convex polygon. Then $P\oplus R$ and $Q\oplus R$ are pseudo-discs**.

To show: $\partial P\oplus R\cap int(Q\oplus R)$ is connected.

Proof by contradiction. Assume $\partial P\oplus R\cap int(Q\oplus R)$ is not connected.

By observation, an extreme point on $P\oplus R$ corresponds to a extreme point on $P$. Same for $Q$.

$\Rightarrow P$ more extreme on $d_p$ and $d_q$ than $Q$

$\Rightarrow Q$ more extreme on $d_s$ and $d_r$ than $P$

What if $P$ is not convex?

> observation: for any set $P,Q,R$
>
> $R\oplus(P\cup Q)=(R\oplus P)\cup(R\oplus Q)$
>
> Triangulate $P$
>
> $\Rightarrow$ this produces a collection of disjoint convex polygons, so the collection of Minkowski sums is a collection of pseudo-disks.

**Let $P$ be a polygon with $n$ vertices, and let $R$ be a convex polygon with $m$ vertices. Then complexity of $P\oplus R$ is $\Theta(mn)$**.

What if $P$ and $R$ are both not convex?

> Triangulate both $P$ and $R$. This gives a collection of $O(mn)$ triangles.

**Let $P$ be a polygon with $n$ vertices, and let $R$ be a convex polygon with $m$ vertices, The complexity of $P\oplus R$ is $\Theta(n^2m^2)$**.

**Let $P$ be a polygon with $n$ vertices, and let $R$ be a convex polygon with $m=O(1)$ vertices, we can compute $P\oplus R$ in $\mathcal O(n^2\log n)$ time.**

好麻啊这一段

## Minkowski Sum & Robot Motion Planning

Let $-p=(-p_x,-p_y)$ and $-\mathcal S=\{-p|p\in\mathcal S\}$

**Lemma**. Let $P$ be an obstacle and let $R$ be a planar, translating robot. A point $q$ in the configuration space is forbidden iff $q\in P\oplus(-R(0,0))$.

In direction:

suppose $q\in P\cap R(x,y)$
$$
q\in R(x,y)\Rightarrow q-(x,y)\in R(0,0)\\
-q+(x,y)\in-R(0,0)\\
q\in P\Rightarrow(x,y)\in P\oplus-(R(0,0))
$$
Only if direction: similar.

Question: How do we compute the free/forbidden space in the configuration space?

> Forbidden space $=\cup_iP_i\oplus-(R(0,0))$

So if $R$ is convex and has constant complexity, and the input polygons have a total $n$ vertices we can compute the forbidden space in $O(n\log^2n)$ time.

## Shortest Paths

Given a set of obstacles $P_1,\cdots,P_h$, a starting point $s$ and target point $t$. Find a shortest path from $s$ to $t$,

**Lemma** A shortest pat between $s$ and $t$ among a st of disjoint obstacles $P_1,\cdots,P_h$ is a polygonal path whose inner vertices are vertices of $P_1,\cdots,P_h$.

Question: how do we compute a shortest path between $s$ and $t$?

> idea: Construct a graph $G=(V,E,w)$ with edge length s.t. for $(u,v)\in E$ the edge corresponds to the distance between $u$ and $v$.
>
> $V=$ the set of obstacle vertex $v$ and $s$ and $t$
>
> $(u,v)\in E\Leftrightarrow u$ and $v$ can see each other
>
> $w(u,v)=||uv||$

**Theorem**: We can compute $G$ in $O(n^2\log n)$ time.

### Visibility Graph

Connecting two vertices if they can see each other.

Sub-goal: compute all visible vertices from any point $p$ in $O(n\log n)$ time.

i.e. visibility graph in $O(n^2\log n)$ time.

Idea: Radical sweep ray!

Exactly identical to normal sweep line, if we rotate the ball until $p$ is a point at infinity.

我的理解是把这个看成以该点为极点的极坐标，可以映射到一个以该点为原点的直角坐标。就可以转化为最普通的扫描线了。

Find vertices visible from $\infty$ X-coordinate.

Compute visible vertices from $s$ to $t$, add to visibility graph, then use Dijkstra for $O(n^2\log n)$ time in total.

(can be improved to $O(n\log n)$)